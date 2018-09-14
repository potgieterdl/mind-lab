---
layout: post
current: post
cover:  assets/images/ssl.jpg
navigation: True
title: HAProxy SSL from creation
date: 2018-01-08 12:00:00
tags: [Code]
class: post-template
subclass: 'post tag-code'
author: derick 
disqus: false
---
# HAProxy SSL from creation

## Backstory
I recently needed to reload an (soon to be) expired SSL cert for HAProxy. I use HAProxy as my SSL end point rather than implementing the certs on each web server. I only allow a connection from this load balancer to the web servers (multiple instances) within a secure local network.

## Easy Peasy

#### Step 1 - Generate your own server private RSA key
You need to create your own private key from which you generate the CSR. Keep this key private and in a safe place

{% highlight shell %}
$ openssl genrsa -out mywebserver.key 2048
{% endhighlight %}

#### Step 2 - Create the CSR (certificate signing request) for SSL Provider
The CSR is generated against the private key above and sent too your SSL provider. In my case RapidSSL. It contains only basic data about your company and *remember* to not input an email or password at all. This seems to fail for me

{% highlight shell %}
$ openssl req -new -key mywebserver.key -out mywebserver.crt
{% endhighlight %}

#### Step 3 - Request a new SSL certificate
[Here is a link to the top providers](https://www.whichssl.com/top-10-ssl-certificate-providers.php). Your need might vary, but in my case i wanted the secure icon and green address bar in the browser and paid a little extra for this type of certificate.

The process is unique per vendor, but should be very simple.
1. Decide on type of cert, either single domain (www.mydomain.com) or multi/wildcard (*.mydomain.com)
2. If its your first time and you want a verfied cert the vendor might ask for physical documentation, this is part of the verification process.
3. At this point you should be asked for your CSR generated in step 2 above. Copy the text, making sure not to accidentally remove and --- or changing the format.

{% highlight text %}
-----BEGIN CERTIFICATE REQUEST-----
MIICvDCCAaQCAQAwdzELMAkGA1UEBhMCVVMxDTALBgNVBAgMBFV0YWgxDzANBgNV
BAcMBkxpbmRvbjEWMBQGA1UECgwNRGlnaUNlcnQgSW5jLjERMA8GA1UECwwIRGln
aUNlcnQxHTAbBgNVBAMMFGV4YW1wbGUuZGlnaWNlcnQuY29tMIIBIjANBgkqhkiG
9w0BAQEFAAOCAQ8AMIIBCgKCAQEA8+To7d+2kPWeBv/orU3LVbJwDrSQbeKamCmo
wp5bqDxIwV20zqRb7APUOKYoVEFFOEQs6T6gImnIolhbiH6m4zgZ/CPvWBOkZc+c
1Po2EmvBz+AD5sBdT5kzGQA6NbWyZGldxRthNLOs1efOhdnWFuhI162qmcflgpiI
WDuwq4C9f+YkeJhNn9dF5+owm8cOQmDrV8NNdiTqin8q3qYAHHJRW28glJUCZkTZ
wIaSR6crBQ8TbYNE0dc+Caa3DOIkz1EOsHWzTx+n0zKfqcbgXi4DJx+C1bjptYPR
BPZL8DAeWuA8ebudVT44yEp82G96/Ggcf7F33xMxe0yc+Xa6owIDAQABoAAwDQYJ
KoZIhvcNAQEFBQADggEBAB0kcrFccSmFDmxox0Ne01UIqSsDqHgL+XmHTXJwre6D
hJSZwbvEtOK0G3+dr4Fs11WuUNt5qcLsx5a8uk4G6AKHMzuhLsJ7XZjgmQXGECpY
Q4mC3yT3ZoCGpIXbw+iP3lmEEXgaQL0Tx5LFl/okKbKYwIqNiyKWOMj7ZR/wxWg/
ZDGRs55xuoeLDJ/ZRFf9bI+IaCUd1YrfYcHIl3G87Av+r49YVwqRDT0VDV7uLgqn
29XI1PpVUNCPQGn9p/eX6Qo7vpDaPybRtA2R7XLKjQaF9oXWeCUqy1hvJac9QFO2
97Ob1alpHPoZ7mWiEuJwjBPii6a9M9G30nUo39lBi1w=
-----END CERTIFICATE REQUEST-----
{% endhighlight %}

#### Step 4 - Download (sometimes sent via email) your certificates from provider and generate .pem
For HAProxy you require a .pem file. This involves taking the X.509 certs from your provider and concatenating them together into a single .pem file (nothing other than several keys in one file)

This part was tricky to understand, my provider RapidSSL gave me two files, a ServerCertificate.cer (my new certificate) and an intermediate certificate (representing them). I also needed to add my server key (generated in step 1) into this file, ie 3 keys in total

{% highlight shell %}
$ cat ServerCertificate.cer >> mydomain.com.pem
$ cat mywebserver.key >> mydomain.com.pem
$ cat Intermediate.cer >> mydomain.com.pem
{% endhighlight %}

You should have 3 keys in this file now, double check with `$ cat mydomain.com.pem`

#### Step 5 - Add to HAProxy and restart        
Next step is too add this .pem file to HAProxy. I normally put this .pem file in `/etc/ssl/private/mydomain.com.pem` and then add the below into HAProxy config (`/etc/haproxy/haproxy.cfg`)

{% highlight shell %}
global
    ...
    ...
    # Default SSL material locations
    ca-base /etc/ssl/certs
    crt-base /etc/ssl/private

    tune.ssl.default-dh-param 2048
    ssl-default-bind-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:ECDH+3DES:DH+3DES:RSA+AESGCM:RSA+AES:RSA+3DES:!aNULL:!MD5:!DSS
    ssl-default-bind-options no-sslv3
    ...
    ...

frontend http
        bind :80
        http-request set-header X-SSL %[ssl_fc]

        redirect prefix https://www.mydomain.com code 301 if { hdr(host) -i mydomain.com }
        redirect prefix https://www.mydomain.com code 301 if { hdr(host) -i www.mydomain.com }
        redirect scheme https if !{ ssl_fc }

frontend ssl
        bind :443 ssl crt mydomain.com.pem no-sslv3
        #bind :80 //comment port 80 out as we want to redirect  80 to 443
        ...

{% endhighlight %}

Restart HAProxy, depending on OS, `sudo /etc/init.d/haproxy restart`. I have found that sometime doing a `stop` and `start` seem to reload the confirm where `restart` sometimes doesnt.

#### Congrats
This should conclude a successful SSL change over on your load balancer. If you want to test this, an easy one is to use [RapidSSL's online testing tool](https://www.rapidsslonline.com/ssl-tools/ssl-checker.php) to verify your site works correctly now.

---
layout: post
current: post
cover:  assets/images/writing.jpg
navigation: True
title: Installing Ghost on Ubuntu 16.04
date: 2018-04-02 20:05:11
tags: [code, getting-started]
class: post-template
subclass: 'post tag-getting-started'
author: derick 
disqus: false
---
# Installing Ghost on Ubuntu 16.04

## Backstory
I've been running Jekyll for a bit, but the theming support has gotten me down. With that low point i decided to try [Ghost](https://ghost.org/), i've run it before for another projects and really enjoyed it. This was a couple years back so keen to see where its all at now

## Introducing Ghost - Blogging Platform
<small class="recap">
Ghost is a fully open source, hackable platform for building and running a modern online publication. We power blogs, magazines and journalists from Zappos to Sky News. - From [Ghost.org](https://ghost.org/)
</small>

#### Recommendation
There are two ways of running ghost, Hosted through [Ghost.org](https://ghost.org/pricing/) or self hosted via your own or hosted server. I will be running it on a small 1 Core - 1 Gb online instance and setting up self renewing ssl certs via [LetsEncrypt](https://letsencrypt.org/). I'm using a local provider called [CloudAfrica](https://www.cloudafrica.net), they have realy cheap [pricing](https://www.cloudafrica.net/pricing/) for an South African online server provider .

#### Step 1 - Installing prerequisites
My instance is running Ubuntu 16.04. We will 

{% highlight shell %}
$ sudo apt-get update
$ sudo curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash
$ sudo apt-get install nodejs
$ sudo apt-get install npm
$ sudo apt-get install build-essential
$ sudo apt-get install nginx
$ sudo apt-get install mysql-server
$ sudo npm i -g ghost-cli
{% endhighlight %}

Now verify that node and nginx are working as expected
`nodejs -v`
`nginx -v`
`mysql -V`

<small class="recap">
You should be able to access nginx on your local address [http://localhost](http://localhost), if not start it using `sudo systemctl start nginx `
</small>

#### Step 2 - Setup prerequisites
Importantly, you are installing and setting up a line, internet facing platform. So you need to think about security and also allow the ports through your default os firewalls. Lets ran through these in prepping for an SSL facing blogging engine.

{% highlight shell %}
$ sudo ufw allow 'Nginx Full'
{% endhighlight %}

#### Step 3 - Setup web directory
Its recomended to install ghost in the /var/www/ghost folder to remove issues with permissions on both /root and /home/[user] setup folders. Its also good practice to create a new username for ghost to own the www folder and restrict any issues in breaches into this system.

{% highlight shell %}
$ sudo mkdir -p /var/www/ghost
$ sudo chown [user]:[user] /var/www/ghost
$ sudo chmod 775 /var/www/ghost
$ cd /var/www/ghost
{% endhighlight %}

<small class="recap">
Its important that your DNS and any port forwarding on the host provider has been setup correctly. Ensure that your DNS is pointing both port 80 and 443 to your new blog url, before completing step 4. Also ensure that the firewall internally (ufw) and extnally (host provider) enables both ports too be forwarded too your server. A good test is too start nginx and access your server on your external ip on port 80. Port 443 wouldnt work yet as nginx hasnt been configured for it. This will be done in step 4
</small>

#### Step 4 - Installing and setting up Ghost
Using the Ghost CLI will prompt a couple of questions. I highly recommend you run with the SSL setup, we will cover the details post install. The CLI will enforce best practices, like generating a mysql user for your Ghost platform, so run through and see you on the other side

Questions asked are 
* Enter your blog Url: *https://blog.myhq.co.za*
* Enter your MySQL hostname : *localhost*
* Enter your MySQL username: *root*
* Enter your MySQL password: *[as per previous step]*
* Ghost database name: *blog*
* Do you wish to set up a ghost MySQL user? *Yes*
* Do you wish to set up nginx? *Yes*
* Do you wish to set up ssl? *yes*
* Enter your email? *[user]@[server]*
* Do you wish to set up systemd? *Yes*
* Do you want to start Ghost? *YES*

{% highlight shell %}
$ ghost install
{% endhighlight %}

#### Step 4 - Cleanup
One important step post install is ensuring that [LetsEncrypt](https://letsencrypt.org/) is configured to check your cert lifecycle and auto renew.

{% highlight shell %}
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install python-certbot-nginx
{% endhighlight %}

Now edit cron and add the certbot renew command in it using `sudo crontab -e` and insert the following into it

{% highlight shell %}
15 3 * * * certbot renew --noninteractive --post-hook "systemctl restart nginx"
{% endhighlight %}
---
layout: post
current: post
cover:  assets/images/bus.jpg
navigation: True
title: Setting up VPN for remote access on Ubiquiti USG
date: 2018-07-30 08:44:74
tags: [home-networking]
class: post-template
subclass: 'post tag-getting-started'
author: derick 
disqus: false
visible: 1
---
# Remote access to your home network via USG

## Backstory
I've recently swapped to [Ubiquiti Unifi](https://unifi-sdn.ubnt.com/) as 

#### Recommendation
There are two ways of running ghost, Hosted through [Ghost.org](https://ghost.org/pricing/) or self hosted via your own or hosted server. I will be running it on a small 1 Core - 1 Gb online instance and setting up self renewing ssl certs via [LetsEncrypt](https://letsencrypt.org/). I'm using a local provider called [CloudAfrica](https://www.cloudafrica.net), they have realy cheap [pricing](https://www.cloudafrica.net/pricing/) for an South African online server provider .

#### Step 1 - Installing prerequisites
My instance is running Ubuntu 16.04. We will 

#### Step 2 - Setup prerequisites
Importantly, you are installing and setting up a line, internet facing platform. So you need to think about security and also allow the ports through your default os firewalls. Lets ran through these in prepping for an SSL facing blogging engine.

{% highlight shell %}
$ sudo ufw allow 'Nginx Full'
{% endhighlight %}

#### Step 3 - Setup web directory
Its recomended to install ghost in the /var/www/ghost folder to remove issues with permissions on both /root and /home/[user] setup folders. Its also good practice to create a new username for ghost to own the www folder and restrict any issues in breaches into this system.


<small class="recap">
MacOS - Remember to check the "Send all traffic over VPN connection" if you having issues within not getting too your internal servers
</small>

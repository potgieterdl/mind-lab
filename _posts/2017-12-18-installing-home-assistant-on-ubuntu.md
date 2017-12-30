---
layout: post
current: post
cover:  assets/images/face-recognition.jpg
navigation: True
title: Installing Home Assistant - HASS.io
date: 2017-12-18 11:21:17
tags: [Home Assistant]
class: post-template
subclass: 'post tag-getting-started'
author: derick 
disqus: true
---
# HA on Ubuntu in Python Virtual Env

## Backstory
I've been playing with [Home Assistan](https://home-assistant.io/) (aka HA) for close on 2 months and with a couple years programming experience it has certainly helped me. But like many others new too dabble with home automation platforms it isn`t that simple and can be overwhelming. This series will hopefully guide you step by step in creation your own [Home Assistant](https://home-assistant.io/) setup

## Introducing Home Assistant
There are multiple blogs detailing the [Home Assistant](https://home-assistant.io/) installs, so i'm not going to go over all of it again. This is specific to Debian 14.04 LTS and will work with Debian 16.04 as well.

#### Recommendation
Unless you going the Raspberry PI route with hass, which is brilliant, i would go full virtual env for this setup. Easy to install and run

#### Step 1 - Installing python
Make sure you have the needed python 3.5 (3.4 still supported but dwindling by the day)

{% highlight shell %}
$ sudo apt-get update
$ apt-get install python3-pip python3-venv
{% endhighlight %}

<small class="recap">
If using *Python 3.4* on *Ubuntu 14.04* you light have to change python3-venv to python3.4-venv
</small>

#### Step 2 - setup env and install homeassistant
We want HA to run on its own with its own user so
{% highlight shell %}
$ sudo useradd -rm homeassistant
{% endhighlight %}

Change directories to the folder you want to install HA into and set ownership to new user
{% highlight shell %}
$ cd /srv
$ sudo mkdir homeassistant
$ sudo chown homeassistant:homeassistant homeassistant
{% endhighlight %}

Create virtual env activate
{% highlight shell %}
$ python3 -m venv homeassistant
$ cd homeassistant
$ source bin/activate
{% endhighlight %}

Once you run the above command, you should see the beginning of prompt change to `$ (homeassistant) homeassistant@...`

Now install HA inside the venv
{% highlight shell %}
$ python3 -m pip install homeassistant
{% endhighlight %}

<small class="recap">
You have now created a new user called `homeassistant` and installed HA in the folder `/srv/homeassistant` using a python virtual env. _For reference, when you upgrade or want to run home assistant, you need to do so in the virtual env._ <mark> For reference, when you upgrade or want to run the HA, you need to do so in the virtual env</mark>
</small>

#### Step 3 - configure runtime and auto-start

#### Step 4 - first run
Ensure you are in the `/srv/homeassistant` folder and run :

{% highlight shell %}
$ hass --open-ui
{% endhighlight %}

This will start HA for the first time and install the necessary libraries needed by the modules in use.

#### Congrats
This is your basic installation done and dusted, up next we will be looking at the basic config needed to expose your installation via SSL to the outside work. The preferred way being via a custom domain with SSL cert.
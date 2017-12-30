---
layout: post
current: post
cover:  assets/images/welcome.jpg
navigation: True
title: Installing Home Assistant - HASS.io
date: 2017-12-18 11:21:17
tags: [Getting started, Home Automation, Home Assistant]
class: post-template
subclass: 'post tag-getting-started'
author: derick 
disqus: true
---
Hey! Welcome to my first ever post, it's great to have you :)

## Backstory
I've been playing with [HA](https://home-assistant.io/) (aka Home Assistant) for close on 2 months and couple years programming experience it has certainly helped me along. But like many others which are trying to dabble with home automation platforms it isn`t that simple. 

## Introducing Home Assistant
There are multiple blogs detailing the [HA](https://home-assistant.io/) installs, so i'm not going to go over all of it again. This is specific to Debian 14.04 LTS and will work with Debian 16.04 as well.

##### Installation on Ubuntu in Python Virtual Environment
### Recommendation
Unless you going the Raspberry PI route with hass, which is brilliant, i would go full virtual env for this setup. Easy to install and run

#### Step 1 - install python
Make sure you have the needed python 3.5 (3.4 still supported but dwindling by the day)

~~~ shell
apt-get install python3 python3-pip python3-venv
~~~

{% highlight shell %}
$ sudo apt-get update
$ apt-get install python3-pip python3-venv
{% endhighlight %}

~~~ ruby
def what?
  42
end
~~~

{{ "{% highlight javascript " }}%}  
/* Some pointless Javascript */
var rawr = ["r", "a", "w", "r"];
{{ "{% endhighlight " }}%}  

> If using python 3.4 on ubuntu 14.04 you light have to change python3-venv to python3.4-venv

#### Step 2 - setup env and install homeassistant
1. We want HA to run on its own with its own user so
{% highlight shell %}
sudo useradd -rm homeassistant
{% endhighlight %}

2. Change directories to the folder you want to install HA into and set ownership to new user
{% highlight shell %}
$ cd /srv
$ sudo mkdir homeassistant
$ sudo chown homeassistant:homeassistant homeassistant
{% endhighlight %}

3. Create new virtual env and install
{% highlight shell %}
$ python3 -m venv homeassistant
$ cd homeassistant
$ source bin/activate
{% endhighlight %}

Once you run the above command, you should see the beginning of prompt change to `$ (homeassistant) homeassistant@...`

{% highlight shell %}
$ python3 -m pip install homeassistant
{% endhighlight %}

> You have now created a new user called `homeassistant` and installed HA in the folder `/srv/homeassistant` using a python virtual env. _For reference, when you upgrade or want to run the HA, you need to do so in the virtual env_ <mark>For reference, when you upgrade or want to run the HA, you need to do so in the virtual env</mark>

#### Step 3 - configure runtime and auto-start

#### Step 4 - first run
{% highlight shell %}
$ hass --open-ui
{% endhighlight %}

> This will start HA for the first time and install the necessary libraries needed by the modules in use.

##### Congrats
This is your basic installation done and dusted, up next we will be looking at the basic config needed to expose your installation via SSL to the outside work. The preferred way being via a custom domain with SSL cert.

### More Posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt | remove: '<p>' | remove: '</p>' }}
    </li>
  {% endfor %}
</ul>
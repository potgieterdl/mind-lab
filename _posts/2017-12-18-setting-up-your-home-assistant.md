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

## Installation on Ubuntu in Python Virtual Environment
### Recommendation
Unless you going the Raspberry PI route with hass, which is brilliant, i would go full virtual env for this setup. Easy to install and run

#### Step 1
Make sure you have the needed python 3.5 (3.4 still supported but dwindling by the day)

```shell
apt-get install python3-pip python3-venv
```

```bash
apt-get install python3-pip python3-venv
```

test

{% highlight shell %}
apt-get install python3-pip python3-venv
{% endhighlight %}

test2

{% highlight ruby %}
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{% endhighlight %}

```ruby
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
```

<pre>
    <code class="ruby">
        def show
            @widget = Widget(params[:id])
            respond_to do |format|
                format.html # show.html.erb
                format.json { render json: @widget }
            end
        end
    </code>
</pre>

#### Step 2

#### Step 3

#### Step 4

### More Posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt | remove: '<p>' | remove: '</p>' }}
    </li>
  {% endfor %}
</ul>
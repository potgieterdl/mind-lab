---
layout: post
current: post
cover:  assets/images/welcome.jpg
navigation: True
title: Face Recognition on Home Assistant
date: 2017-12-30 12:00:00
tags: [Home Assistant]
class: post-template
subclass: 'post tag-getting-started'
author: derick 
disqus: true
---
# Setup Face Recognition on Home Assistant

## Backstory
In this segment we will configure your home assistant installation to recognize a member of your family and trigger an action via telegram bot. This notification will allog you to trigger another event. In my case, a member of the family at the front door (think my parents) will send a telegram message to my phone with an image of the person and there name, allowing me to open the door for them.

#### Congrats

### Related Posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt | remove: '<p>' | remove: '</p>' }}
    </li>
  {% endfor %}
</ul>
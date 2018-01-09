---
layout: post
current: post
cover:  assets/images/advanced.jpg
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
In this segment we will configure your home assistant installation to recognize a member of your family and trigger an action via telegram bot. This notification will allow you to trigger another event, like opening a gate. In my case, a member of the family at the front door (think my parents) will send a telegram message to my phone with an image and their name, allowing me to open the door for them.

This could be used for presence setting, for automatic unlocking of your security system or allowing friends to feed the dogs by automatically opening the yard gate for them. All while keeping you in the know if need be.

## Azure: Register account and create Face API service

### Create new Azure account
[Create Account](https://azure.microsoft.com/en-us/free/), its free for 30 days and you get to play around with $200 worth of credit during this time frame. I already used this before and had to input my Credit Card to create the Face API service.

### Create FACE API Service
Once in, you will need to create a new Cognitive Service for the Face API.

#### Step 1 : Create service
For more details and a start link, check out [Face API](https://azuremarketplace.microsoft.com/en/marketplace/apps/Microsoft.CognitiveServicesFace/?utm_source=mkt-CognitiveServicesFace&utm_content=fa9bbbf9741c49cebd4116d9a745ae04&utm_campaign=social&utm_medium=E)

To get right into it, follow this link [Create Face API](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)

I used the name `hass` and resource group `hass`

#### Step 2 : Grab access keys
One your service is created, you can click on the Resource Management section under Keys. Copy KEY1, this is your API key needed in HA to make the service work

![Example Key Location](assets/images/post-2017-12-30/ms-face-api-keys.png){:class="img-responsive"}

## Segment 2: Configure HA

### Camera

#### Setup your camera
The faces api requires a source, and the most likely source is a camera already connected to HA. We will quickly go over the basics here, but it will depend on your cameral. [Check here for possible options](https://home-assistant.io/components/camera/)

{% highlight shell %}
$ vi devices/cameras.yaml

### viewing devices/cameras.yaml ### 
- platform: ffmpeg
  name: Back Yard
  input: rtsp://user:pass@x.x.x.x:554/snl/live/1/1
  extra_arguments: -an -q:v 25
{% endhighlight %}

### Image Processing (Face API)
{% highlight shell %}
$ vi image_processing.yaml

### viewing image_processing.yaml ### 
- platform: microsoft_face_identify
  group: family
  confidence: 50
  source:
    - entity_id: camera.back_yard
{% endhighlight %}

### Add Microsoft Face service in configuration.yaml
{% highlight shell %}
$ vi configuration.yaml

### viewing configuration.yaml ### 
...
camera: !include devices/cameras.yaml
image_processing: !include image_processing.yaml
...

...
microsoft_face:
  api_key: abcdefghijklmnopqrstuv
  azure_region: westeurope
{% endhighlight %}

#### Restart and check the logs
Once the above is configured, you should restart your Home Assistant and check the logs too see the faces api being called. This will happen strait after your camera getting an image, this same image is sent through too Face API for recognition

#### Congrats
This is really a glorified proof of concept. The amount of images sent for analysis to Microsoft`s Face API is insane, i logged couple thousand request in one day, almost using up my free account. For this to work and be free, we would need to only send the images when movement or a person is recognised locally and then send off to Microsoft. I'll follow this blog post up with a more sustainable working prototype.


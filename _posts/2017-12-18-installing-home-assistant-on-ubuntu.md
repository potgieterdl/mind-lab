---
layout: post
current: post
cover:  assets/images/welcome.jpg
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

#### Step 2 - Setup env and install homeassistant
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

#### Step 3 - Configure runtime and auto-start
The preferred way would be to auto start Home Assistant with a init script

Create a file under /etc/init.d/hass-daemon and copy the script at the end of this post into it
{% highlight shell %}
$ sudo vi /etc/init.d/hass-daemon
{% endhighlight %}

Once you have copied the script (end of this post) into the above file, you need to set the execute permission on the script for auto-startup (on boot)
{% highlight shell %}
$ sudo chmod +x /etc/init.d/hass-daemon
{% endhighlight %}


#### Step 4 - First run
Ensure you are in the `/srv/homeassistant` folder and run :

{% highlight shell %}
$ hass --open-ui
{% endhighlight %}

This will start HA for the first time and install the necessary libraries needed by the modules in use. Any failures are easier to spot this way vs doing the daemon run which wount print out anything. You need to view the log in `tail -f /home/homeassistant/.homeassistant/homeassistant.log` to see any output from the auto-start script.

Once this initial run is complete, press `crtl+c` to exit and then start using the init script `sudo /etc/init.d/hass-daemon start`, view the output using above tail command on the log file.

#### Congrats
This is your basic installation done and dusted, up next we will be looking at the basic config needed to expose your installation via SSL to the outside work. The preferred way being via a custom domain with SSL cert.

##### Python Virtual Start-up Script

{% highlight shell %}
#!/bin/sh
### BEGIN INIT INFO
# Provides:          hass
# Required-Start:    $local_fs $network $named $time $syslog
# Required-Stop:     $local_fs $network $named $time $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Description:       Home\ Assistant
### END INIT INFO

# /etc/init.d Service Script for Home Assistant
# Created with: https://gist.github.com/naholyr/4275302#file-new-service-sh
PRE_EXEC="cd /srv/homeassistant && python3 -m venv . && source bin/activate &&"
RUN_AS="homeassistant"
PID_FILE="/var/run/hass/hass.pid"
CONFIG_DIR="/home/homeassistant/.homeassistant"
FLAGS="-v --config $CONFIG_DIR --pid-file $PID_FILE --daemon"
REDIRECT="> $CONFIG_DIR/home-assistant.log 2>&1"

start() {
  if [ -f $PID_FILE ] && kill -0 $(cat $PID_FILE) 2> /dev/null; then
    echo 'Service already running' >&2
    return 1
  fi
  echo 'Starting service…' >&2
  local CMD="$PRE_EXEC $HASS_BIN $FLAGS $REDIRECT;"
  su -s /bin/bash -c "$CMD" $RUN_AS
  echo 'Service started' >&2
}

stop() {
    if [ ! -f "$PID_FILE" ] || ! kill -0 $(cat "$PID_FILE") 2> /dev/null; then
    echo 'Service not running' >&2
    return 1
  fi
  echo 'Stopping service…' >&2
  kill $(cat "$PID_FILE")
  while ps -p $(cat "$PID_FILE") > /dev/null 2>&1; do sleep 1;done;
  echo 'Service stopped' >&2
}

install() {
    echo "Installing Home Assistant Daemon (hass-daemon)"
    echo "999999" > $PID_FILE
    chown $RUN_AS $PID_FILE
    mkdir -p $CONFIG_DIR
    chown $RUN_AS $CONFIG_DIR
}

uninstall() {
  echo -n "Are you really sure you want to uninstall this service? That cannot be undone. [yes|No] "
  local SURE
  read SURE
  if [ "$SURE" = "yes" ]; then
    stop
    rm -fv "$PID_FILE"
    echo "Notice: The config directory has not been removed"
    echo $CONFIG_DIR
    update-rc.d -f hass-daemon remove
    rm -fv "$0"
    echo "Home Assistant Daemon has been removed. Home Assistant is still installed."
  fi
}

case "$1" in
  start)
    start
    ;;
  stop)
    stop
    ;;
  install)
    install
    ;;
  uninstall)
    uninstall
    ;;
  restart)
    stop
    start
    ;;
  *)
    echo "Usage: $0 {start|stop|restart|install|uninstall}"
esac
{% endhighlight %}
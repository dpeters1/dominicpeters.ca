---
layout:     post
title:      "Ceiling Light/Fan Control with Cayenne"
subtitle:   "Re purposing a propriety rf remote control for maximum laziness"
date:       2016-10-25 12:00:00
categories: tidbits
author:     "Dominic Peters"
---

<div class="avatar bigAvatar" style="background-image: url('{{ site.baseurl }}/img/breathalyzer/main.jpg');">
</div>

## Description

The previous owner of my house installed remote controlled ceiling lights in all the bedrooms. This was really cool for a couple weeks, but now I’m realizing that it can be really annoying when I misplace the remote. Each room has a separate remote that only works for that room. Extra remotes are both expensive and hard to come by. Making my own rf remote is impractical as they run in the 400mhz range and there is generally very little documentation about the radio codes for this fan model. 

To circumvent this, I disassembled one remote and soldered transistors directly to the physical button contacts. An esp8266 is used to control the transistors, allowing me to switch them using the cayenne dashboard or via other wifi connected microcontrollers.
![]({{ site.url }}/img/cayenne-lightswitch/schematic.jpg){:class="center" width="520px"}
###### Wiring Schematic

## Components

* ESP8266
* 5x 2N2222 Transistor
* 5x 2k ohm Resistor
* 3.3V Linear Regulator
* Female Barrel Connector
<p></p>

## Build Pictures
<p></p>
{% slider 4:3 %}

  {% for image in site.static_files %}
  {% if image.path contains 'img/cayenne-lightswitch/build' %}
<img data-src="{{site.baseurl}}{{image.path}}">
  {% endif %}
  {% endfor %}

{% endslider %}
<p></p>

## Arduino Sketch

The arduino sketch is very simple and just deals with the inputs from the cayenne dashboard. For the light on/off and fan control, I am just controlling the HIGH/LOW behaviour of the GPIO pins. The dimmer is a bit more complicated because it is normally adjusted by long-pressing the on/off button on the remote. Cayenne does not have this functionality so instead the arduino has to keep track of the current dimmer level and calculate how long the pin should be pulled HIGH to reach the next desired brightness. As usual with the ESP8266 boards, I am using the OTA method to upload to them as it allows me to modify the code even once it's installed in the remote.

[Source Code](https://github.com/dpeters1/Wifi-Ceiling-Light-Fan-Remote)

## Cayenne Integration

Cayenne, a company who's primary product is a 'drag and drop' web dashboard for IoT devices, is sponsoring this build so to speak. The only requirement is that I use their dashboard and API in some way. They have a nice arduino library which lets you send and receive data from the dashboard. On the dashboard, I am using two sliders to control fan speed and brightness, and a button to control on/off of the light. I am also able to set triggers to perform actions based on events, like time of day. This could be useful to automatically turn off the lights past a certain time at night or when I leave the house.

![]({{ site.url }}/img/cayenne-lightswitch/cayenne.jpg){:class="center" width="100%"}
###### Cayenne Web Dashboard
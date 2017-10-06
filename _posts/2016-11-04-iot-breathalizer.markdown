---
layout:     post
title:      "IoT Breathalyzer"
subtitle:   "Scientifically prove your intoxication in style with this internet connected breathalyzer!"
date:       2016-11-04 12:00:00
categories: projects
author:     "Dominic Peters"
---

<div class="avatar bigAvatar" style="background-image: url('{{ site.baseurl }}/img/breathalyzer/main.jpg');">
</div>

## Description

The idea for this project came when I found a cheap sensor online that claimed to measure alcohol concentration in the air. Who hasn't at some point wanted to use a breathalyzer, to see to a scientific degree just how intoxicated they were? Police around the world use breathalyzers to obtain suspected drunk drivers' BAC. These breathalyzers give accurate and reproducible readings, but cost hundreds of dollars. 

The MQ3 sensor only costs a couple bucks, but it does not give accurate, nor reproducible BAC readings. In other words, don't use this sensor to decide if you can drive or not. However, we can still have fun comparing relative values between friends. The Cayenne platform and an ESP8266 are used to connect the MQ-3 sensor to the internet so data can be viewed and gathered over time.

## Components

* ESP8266
* MQ-3 Alcohol Gas Sensor
* 5V Linear Regulator
* 3.3V Linear Regulator
* 11.1V 2.2Ah Lipo
<p></p>

## Hardware

The device needs to be portable and durable if it should be used while under the influence. The lipo battery provides about 8 hours of continuous use. All the components are stuck to the battery pack with thick double sided tape. I hope this provides some shock absorption in case of drops. The enclosure is made of two 3mm aluminum plates bolted together. Aluminum is a great material to work with because it cuts and drills easily, but that also means that it is easily scratched as seen by the pictures. Regardless, it will protect the battery and electronics in most situations and I think it gives the device a rugged feel.

{% slider 4:3 %}

  {% for image in site.static_files %}
  {% if image.path contains 'img/breathalyzer/build' %}
<img data-src="{{site.baseurl}}{{image.path}}">
  {% endif %}
  {% endfor %}

{% endslider %}
<p></p>

## Software

Cayenne, a company who's primary product is a 'drag and drop' web dashboard for IoT devices, is sponsoring this build so to speak. The only requirement is that I use their dashboard and API in some way. They have a nice arduino library which lets you send and receive data from the dashboard. The template provided in the cayenne dashboard provides a very good starting point. The first data channel sends live readings from the sensor to the dashboard. I added a second channel/input to keep track of the highest reading obtained, aka the "high score". If the cayenne devs are reading this, I wish there were more input types other than luminosity, voltage, etc. I don't see why the data has to be tied to a unit at all. Lastly, there's an http server running so that the board firmware can be updated over the air.

![]({{ site.url }}/img/breathalyzer/dashboard.jpg){:class="center" width="100%"}
###### Cayenne Web Dashboard

[Source Code](https://github.com/dpeters1/IoT-Breathalyzer)






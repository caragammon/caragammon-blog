---
title: Building A Smart Water Tank Meter With The ESP8266
description: The want for a smart device to measure the amount of water left in our water tank led to a new interest in hobby electronics opening up a whole new world of possibilities when it comes to cheap and easy to build smart home devices.
date: 2022-06-08T15:13:13.021Z
author: Cara Gammon
tags: electronics
---

A wandering suspicion that I could build something to measure the level of the water tanks that feed the house led me down a path that once I started, I could not stop.

## How it began

We moved into our new house - which includes a lot of new firsts for us, including living off tank water that is collected from the roof of the house. The tank is concrete, with no way of measuring the water level - short of climbing up the tank and poking a stick into the top. Luckily we moved in at the end of summer, and for a few weeks we played the water conservation game, hoping that it wasn't about to run dry on us.

We had no idea how much water was in there, and how much we were collecting every downpour. I started to look up smart water meters, barely finding a handful of options, most looking like sketchy projects for hundreds of dollars. So I started down a different path. Could we build our own? I had a Raspberry Pi I had recently unpacked and was feeling like anything could be possible.

This is when I found the world of hobby electronics.

## The sensor

First thing was first. Is there something we could use to actually measure the amount of water in there. A quick Google and I found the [HC-SR04](https://core-electronics.com.au/hc-sr04-ultrasonic-module-distance-measuring-sensor.html). Only around $3 dollarydoos to buy one of these bad boys and now all I had to do was figure out what the hell to do with it.

## The Raspberry Pi
I knew I wanted something that could use Wifi to connect to. I don't have a Zigbee or Z-Wave hub and just wanted something simple. I also knew I didn't want to have to run a wire to the plug, so it needed to be able to be powered by batteries. I started to look at the Raspberry Pi Zero W, which looked like the perfect candidate... except that it was out of stock everywhere and scalpers were looking for ~$100 for a single device. Not the winner I had hoped. 

So I started looking at the Pico - and Wifi modules, and it just started to be complicated to get started. Then I was suggested the Particle Z Wave, which also was out of stock everywhere. That was when my searches led me to the Expressif development boards. Seemed like it ticked all the boxes at this point: tiny, low powered, and wifi built in. Specifically built for and used by the iot community. So I found a [dev board kit on Amazon](https://www.amazon.com.au/Freenove-ESP32-WROVER-Contained-Compatible-Bluetooth/dp/B09CGB1BGV/) with a heap of sensors included (including the HC-SR04 I needed for my water meter) and ordered one.

## The ESP32 development board

This was where the real fun began. The kit came with all kinds of sensors, so naturally I had to go through the tutorial book and build all kinds of little light switches and sensors. The Arduino software was much easier to getst atarted with than the Python scripts included with it. The internet was a wild and wonderful place to find all the pieces I needed to put together my little projects. 

After playing around with my new toys for a couple of weeks, I set up the ultrasonic sensor with the board, and set about finding a way to send the data back to something, with some kind of way of displaying it.

## Down the MQTT, Mosquitto, NodeRED, InfluxDB, Grafana rabbit hole

So I had already set the Raspberry Pi up as an [internet speed monitor using Grafana and InfluxDB](https://pimylifeup.com/raspberry-pi-internet-speed-monitor/)... surely I could set up the water meter to do the same. Down the rabbit hole I went - setting up the ESP32 to send MQTT messages to the Raspberry Pi with the Mosquitto MQTT broker set up, with NodeRED to manage the routing of the messages to InfluxDB and Grafana running as my dashboard to view the results. Seemingly going down the right path. As far as software was concerned, the only part left to sort out was deep sleep (to extend battery life), and managing network updates (what happened if I changed my network and the device could no longer connect to the hardcoded network address).

## Enter HomeAssistant

At the same time I was navigating these problems, I decided that I would set up Home Assistant to manage my home smart devices. It was while setting this up and looking through the device options that I saw the ESPHome service.

A service that promised to automatically connect to my ESP smart devices, add them to Home Assistant, provide a wifi hotspot I could connect to with a portal to reset the wifi should it fail, and over the air programming after the fact (!!!). All that, with a yaml file to control most sensors and dev boards? Hell yes, don't mind if I do.

## ESPHome

Getting started with ESPHome was a little bit fiddly, until I realised that there were a few things I needed to do with HomeAssistant before getting started. It didn't like the lack of SSL on my local instance, so I set up a dynamic DNS and port forwarding so I could access HA outside the network, and also add an SSL to my setup. 

After this, setting up ESPHome was a breeze. Plug the ESP board into my machine, flash it with the software, and then reprogram from over the air from then on. I could set up a little line graph on my HA dashboard to display my water level, and ESPHome already had built in setup for the ultrasonic sensor, and everything else I needed.

## Battery Power

This is the last piece of the puzzle. TBA
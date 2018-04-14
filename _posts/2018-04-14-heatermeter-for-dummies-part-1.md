---
layout: post
title: "HeaterMeter for Dummies, part 1"
description: ""
category: 
tags: pi
date: "Sat, 14 Apr 2018 15:15:00 -6"
---
{% include JB/setup %}

I recently built a Raspberry Pi powered [HeaterMeter](http://heatermeter.com) barbeque pit controller, which generated a bit of interest via social media.  This post will focus on as many pre-built, "off the shelf" components as possible.  A slightly cheaper solder-it-yourself kit can be purchased from the [HeaterMeter Store](https://store.heatermeter.com/).

Even if you get the pre-assembled HeaterMeter board, you will have to solder two wires.  You'll need an Ethernet cable (not listed below) to sacrifice as the blower power cord.  Directions for connecting the wires can be found on the [HeaterMeter Wiki](https://github.com/CapnBry/HeaterMeter/wiki/Blower-and-Servo-Wiring).

The remainder of this post is simply a build sheet (Large Big Green Egg specific) with links and prices as of this post.  Expect to spend around $350, depending on the options chosen.

## HeaterMeter Store
* [$144.00 - HeaterMeter 4.3 Soldered (no blower)](https://store.heatermeter.com/products/heatermeter-4-3-soldered?variant=42418512597)
* [$16.50 - Case for HeaterMeter 4.3](https://store.heatermeter.com/products/case-for-heatermeter-4-2?variant=31245653830)
* [$7.50 - 8GB Micro SD Card (pre-imaged)](https://store.heatermeter.com/products/sandisk-8gb-microsdhc-card?variant=3061406269461)
* [$9.50 - Probe Spool (OPTIONAL)](https://store.heatermeter.com/products/probe-spool-for-maverick-and-thermoworks-probes?variant=40966768134) - works ok, but may not be worth the cost

## Amazon
* [$36.33 - Rasperry Pi 3B](https://www.amazon.com/gp/product/B01CD5VC92/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1)
* [$9.99 - K-Type Thermocouple (Alligator Clip)](https://www.amazon.com/gp/product/B0142SKL0Y/ref=oh_aui_detailpage_o04_s01?ie=UTF8&psc=1) \*
* [$9.99 - K-Type Thermocouple (Metal Probe)](https://www.amazon.com/gp/product/B0142RXG84/ref=oh_aui_detailpage_o04_s01?ie=UTF8&psc=1) \*
* [$14.99 - K-Type Thermocouple Extension Cable (OPTIONAL)](https://www.amazon.com/K-Type-Thermocouple-Extension-Miniature-Connectors/dp/B075NSS8JB/ref=sr_1_1_sspa?ie=UTF8&qid=1523734520&sr=8-1-spons&keywords=thermocouple+extension&psc=1) \*\*

\* NOTE: You only need one thermocouple (pit/dome) probe.  I recommend getting one of each type (above) so that you can clip a probe to the cooking grate or insert one through the hole in the BGE's dome, depending on the cook.

\*\* NOTE: The linked probes only have 3-4 feet of wire attached.  Buy the linked extension cable if you need a bit more reach.

## BBQ Guru Store
* [$44.00 - Pit Viper Fan](https://www.bbqguru.com/storenav?CategoryId=2&ProductId=44)
* [$20.00 - Ceramic Adapter (for BGE and similar)](https://www.bbqguru.com/storenav?CategoryId=2&ProductId=14)

## ThermoWorks
* [$16.00 - Right Angle Probe](https://www.thermoworks.com/TX-1001X-OP) \*
* [$16.00 - Straight Probe](https://www.thermoworks.com/TX-1004X-SP) \*
* [$79.00 - Thermapen (VERY OPTIONAL)](https://www.thermoworks.com/Classic-Thermapen) \*\*

\* NOTE: You only need one of the Thermoworks food probes.  The HeaterMeter supports up to three of them.  I recommend getting one each of the ones linked above for a bit of flexibility.

\*\* NOTE: The Thermapen has nothing to do with the HeaterMeter build.  It's simply the best digital thermometer one can buy, and if you don't have one yet, but you're serious about cooking, you ought to consider snagging one!

---
layout: post
title: "Microprocessor powered, steam injected brewing"
description: ""
category: "beer"
tags: [beer, arduino]
date: "Sat, 8 Feb 2014 18:11:00 +0300"
---
{% include JB/setup %}


I started brewing beer because it seemed slightly interesting, and it was cheap to try using liquid extract, a few buckets, and some kitchen utensils.  I kept brewing beer because the hobby lends itself to *tons* of DIY projects across many genres.  I find the science and mechanics absolutely fascinating.  In this video series, I showcased my first microprocessor controlled homebrewery.  For mash temperature stabilization, I built a steam generator out of an old Cornelius keg and a water heater heating element.  Temperature and pressure were monitored and controlled via an [Arduino](http://www.arduino.cc) Duemilanove, connected to a serial port enabled Java front end, shown running on a Windows XP laptop.  You can read a bit more about that system in [this thread](http://www.homebrewtalk.com/f51/diy-steam-mash-system-yuri-27070).

<style>.embed-container { position: relative; padding-bottom: 56.25%; padding-top: 30px; height: 0; overflow: hidden; max-width: 100%; height: auto; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='http://www.youtube.com/embed/EvU2rhT-_p4?list=PLAQYN1wmrHN7047dOIs0loPnv-IxXFoVM' frameborder='0' allowfullscreen="allowfullscreen"> </iframe></div>

I've since upgraded multiple times.  The Cornelius keg had plenty of volume, but the lid didn't seal well the way I had it configured.  To replace it, I converted a full sized 1/2 bbl Sanke keg for use as the steam vessel ([thread here](http://www.homebrewtalk.com/f51/yuri-goes-big-steam-new-boiler-point-use-water-heater-125953)).

I never liked the Java/Windows interface, in part due to the fact that I negated the beauty of Java by using some OS specific serial port libraries.  I also discovered OWFS on Linux, which allows the use of a [1-Wire microLAN](http://en.wikipedia.org/wiki/1-Wire) as a file system.  So I changed the architecture of my brewery to use a [NSLU2 Debian "Slug"](http://www.cyrius.com/debian/nslu2) as the interface for a 1-Wire network.  The Slug also served up a webpage that allowed remote viewing and control.  "GatorDad" at Homebrewtalk has a [good writeup on OWFS](http://www.homebrewtalk.com/f51/monitoring-controlling-linux-cheap-240955).

Because I'm never satisfied, I'm looking at some combination of [Raspberry Pi](http://www.raspberrypi.org), [Teensy](https://www.pjrc.com/teensy), and/or Arduino as the next upgrade.  I've ordered the parts, and my friend jimmayhugh has some great info at <http://www.teensypi.com>. Progress is likely to be slow, but stay tuned, as I intend to cover the project here as it unfolds.

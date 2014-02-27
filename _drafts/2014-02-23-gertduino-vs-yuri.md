---
layout: post
title: "GertDuino vs Yuri"
description: ""
category: "geekery"
tags: [pi, arduino]
date: "Sun, 23 Feb 2014 22:55:41 +0300"
---
{% include JB/setup %}

TODO:
environment setup - including ama0 tty stuff and minicom vs picocom

This is covered by: http://friendsoftheunicorn.net/content/gertduino-setup
jumper - reset atmega48 to boot
erase atmega48, then no jumper

arduino-cmake setup:
git clone https://github.com/queezythegreat/arduino-cmake.git
copy cmake/* to /usr/share/arduino-cmake/
use my CMakeLists.txt file

PHP with Pi:
http://www.fritz-hut.com/2012/08/30/php-serialclass-with-arduino-raspberrypi/

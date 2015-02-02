---
layout: post
title: "Practical Arduino - RV Lighting"
tagline: "RVduino?"
description: ""
category: Geekery
tags: [arduino,linux]
date: "Fri, 30 Jan 2015 17:56:15 -0600"
---
{% include JB/setup %}

![RV Pic]({{ site.url }}/images/led_outside.png)

I replaced a recently failed RGB LED strip light controller in my RV with a homebrewed version using an Arduino Yún.  The result is wifi-controlled, schedulable exterior lighting that is far superior to the overpriced, failed piece of junk!

## Do as I say, not as I do... ##
I own a large fifth wheel RV that, until recently, lacked good porch lighting.  While browsing the aisles at a Camping World store, I found a 16' 12VDC RGB LED strip and RF remote boasting wireless control out to 40' without the need for IR line-of-sight like most similar models.  Instant gratitude dictated that I bite the pricetag bullet.  Two weeks later, the controller bit the dust.

During the two weeks of stock operation, I traveled several hundred miles from the nearest Camping World.  I installed the LED strip __very__ permanently with 3M extreme duty mounting tape, and I sealed the controller wire pigtail into the side of the RV with some (lots of) stout marine grade sealant.  A warranty return was likely impossible.

__Lesson #1:__ DO NOT pay north of $100 for a fancy controller that comes with an RGB LED strip that can otherwise be had for $10-$20.

## Parts bill: ##

* 12VDC RGB LED Strip
* [Arduino Yún](http://arduino.cc/en/Main/ArduinoBoardYun?from=Products.ArduinoYUN)
* Micro-SD card (of any size - minimal data is stored)
* Breadboard or Protoshield
* Automotive 12VDC power port (cigarette lighter)
* Micro-USB cell phone car charger
* 3 TIP3055 or similar NPN power transistors (at least 1A capable)
* Assorted 1/4 watt resistors (I settled on 100Ω)
* Assorted wire, wire jumpers, header pins, and connectors as required
* Solder-able protoboard, screw terminals, and appropriate enclosure if more permanent installation is desired


You'll also need a computer with an up-to-date [Aruino IDE installation](http://arduino.cc/en/Main/Software) (1.5.4 or later).

__Lesson #2:__ The parts for this project will cost almost as much as the original, overpriced strip (around $100).

The cost was worth it to me as an academic venture to try out the Arduino Yún, but there are certainly cheaper solutions.  For example, the combination of a Teensy microcontroller, WIZ820io ethernet module, and cheap TP-Link wireless router would virtually cut the hardware cost in half.  Choose pushbutton control rather than wireless, and the parts bill could be quite cheap.  Also, off-the-shelf IR remote LED controllers cost around $20.

## Wiring: ##

| ![Install Pic]({{ site.url }}/images/led_installation.png) | ![Wiring Pic]({{ site.url }}/images/led_wiring.png) |

<sup><sub>Due to a stroke of luck, the LED strip terminates exactly behind my RV's bathroom medicine cabinet, making it very easy to hide the (pressently ugly) installation.</sub></sup>

My RGB LED strip is configured with a common anode, meaning that each color is constantly connected to +12VDC, and each color's ground is separately switched in order to control the perceived color.  A common cathode strip could be substituted, but I will cover only the common anode configuration.

Connect the cigarette lighter port to a fused 12VDC source.  Also, connect the LED strip's common anode to +12VDC (also fused - same circuit, if possible).

Connect three NPN transistors as follows:

* Each base via a resistor to separate PWM pins on the Arduino board.  I used 9 (red), 10 (green), and 11 (blue).
* Each collector to its respective LED strip cathode pin.
* Each emitter to ground.

__IMPORTANT:__ To determine the appropriate transistor base resistor value, download the [datasheet](http://www.onsemi.com/pub_link/Collateral/TIP3055-D.PDF) for your transistors and then use [a calculator like this one](http://www.petervis.com/GCSE_Design_and_Technology_Electronic_Products/transistor_base_resistor_calculator/transistor_base_resistor_calculator.html), or you can try your hand at [some math](http://electronics.stackexchange.com/questions/83685/need-help-calculating-resistance-for-transistor-base).  [Ohm's law](http://www.ohmslawcalculator.com/ohms_law_calculator.php) will help to determine current and/or resistance values for the circuit load.  My LED strip draws about 1A per channel at full intensity.

__Lesson #3:__ Arbitrarily selecting a base resistor value that is too high (like 1kΩ, in my case) will not saturate the transistors, thus forcing them to act as current limiters and dissipate power as heat.  Ask me how I know...and note in the heat sinks I installed as a stop-gap measure before realizing my mistake and reducing the value to a more appropriate 100Ω.  Be careful not to select too low a resistance, which may demand too much current from the Arduino pins, resulting in unstable or damaging behavior.

Before applying power, double check that +12VDC will not be directly supplied to any portion of the Arduino board.  When you're ready to power the system, connect the Yún to the phone charger.

## Code: ##

All of my source code is [available on GitHub](https://github.com/yuri-rage/wifi_color_picker).  The included README contains most of what you'll need to know.  Here is a brief overview.

<sup><sub>Credit where it's due - [Matteo Loglio's GitHub repo](https://github.com/mat-lo/yun-examples/tree/master/color_picker) provided the initial code-base for this project.  I deviated signifcantly from his design, but the basic concept translated well.</sub></sup>

I encourage you to [download and install the latest OpenWrt-Yun image](http://arduino.cc/en/Tutorial/YunSysupgrade) before doing any significant sketch development.  Then follow the [Getting Started Guide](http://arduino.cc/en/Guide/ArduinoYun) to get familiar with the Yún and its architecture.  Also, take a look at the YunSerialTerminal and Bridge example sketches.

Basic code architecture:
<F11>
* User interface is via an OpenWrt/Linux served, Javascript-centric webpage
* Upon initial webpage load, the Yún's clock is synced (via REST and Bridge calls) with the user's system clock, overcoming Yún's lack of a RTC
* Web-initiated REST calls pass user input to the 32U4 sketch via the Bridge
* 32U4 sketch parses REST calls and sets LED state(s) accordingly using PWM
* 32U4 sketch passes any required shell commands back through the Bridge to initiate clock syncing and LED state scheduling (using the `at` command)

Simple directions for sketch upload (Linux) and use:

    cd ~/sketchbook
    git clone https://github.com/yuri-rage/wifi_color_picker.git
    arduino &

* Open the `wifi_color_picker.ino` sketch using the IDE.
* Ensure that an SD card is inserted into the Yún.
* Ensure that the Yún is powered and connected to an accessible port.
* Ensure that the `at` command is available on the Yún (see [README](https://github.com/yuri-rage/wifi_color_picker)).
* Select Upload from the IDE interface.
* Navigate to <http://arduino.local/sd/wifi_color_picker> via a web browser

Example REST calls:<br>
(you can bypass the web UI and directly access the sketch at its basic level)

* Set the color to blue-green: <http://arduino.local/arduino/rgb/00ffff>
* Turn the LEDs off: <http://arduino.local/arduino/rgb/000000>
* Set the color to red at noon, 21 Oct 2015 (UTC): <http://arduino.local/arduino/rgbat/ff0000/1445428800>
* View the schedule: <http://arduino.local/arduino/echo/atq>
* Clear the schedule: <http://arduino.local/arduino/echo/atrm>


The controller works both in access point and client wifi modes.  I find it most useful to have it connected to my local wifi network in client mode.  However, it may be useful in some cases to have the Yún operate on its own wifi network in access point mode.

The web UI includes some minor optimization for mobile devices, since I intend to use my iPhone as the primary remote control.  It looks nice on the roomy screen of an iPhone 6+ but may fall short on smaller displays.

Here are some screen grabs.  Note that my Yún is configured with a custom hostname (cycloneled).

| ![WebUI Pic]({{ site.url }}/images/ledwebui_init.png) | ![WebUI Pic]({{ site.url }}/images/ledwebui_on.png) |
| ![WebUI Pic]({{ site.url }}/images/ledwebui_datepicker.png) | ![WebUI Pic]({{ site.url }}/images/ledwebui_at.png) |
| ![WebUI Pic]({{ site.url }}/images/ledwebui_atq.png) | |

## TODO: ##

Tidy up the installation with a soldered (custom printed?) board and enclosure.

Determine idle and active power requirements.  I don't know what drain/demand the system will place on my RV's batteries when disconnected from shore power.  I'm guessing that the current draw (even when the lights are off) is somewhat significant, and the controller will likely need to be powered down if the RV is operating on battery power for long periods of time.  There may be some sleep/low power APIs available to help curb power idle consumption, but I haven't implemented any yet.

Code a more elegant schedule implementation.  At present, raw, ugly `atq` output is sent to the client webpage when the schedule is requested.  Also, the user cannot selectively remove scheduled state changes - the entire schedule is deleted with the "Clear Schedule" button.  Both of these behaviors could be improved by storing the schedule in a local (SD card) text file.

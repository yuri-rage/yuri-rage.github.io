---
layout: post
title: "Arduino IDE - Kustomized"
description: 
tagline: "Arduino + Teensy + CMake + *buntu"
category: "geekery"
tags: [linux,arduino]
date: "Thu, 27 Feb 2014 22:24:30 +0300"
---
{% include JB/setup %}
Next level Arduino nerd rage, including Teensy support and command line build/upload capabilities, all within a reasonably clean installation on any \*buntu variant.  Here's how:

##Install dependencies##
The Arduino IDE requires a working JRE.  It used to solely depend on Java 6, but it seems as if I'm up and running with Java 7.  Regardless, issue:

`apt-get install -s arduino`

The -s flag simulates installation of the Arduino IDE and will show all dependencies.  If java6-runtime and/or default-jre appear in the list of packages to be installed, then go ahead `sudo apt-get install` them now.  *Do not* install the arduino package - we are going to do that manually.  While you're at it:

`sudo apt-get install gcc-avr binutils-avr avr-libc avrdude cmake`

If you're running a 64-bit distro like me, install a few 32 bit libraries:

`apt-get install ia32-libs`

##Manually install the Arduino IDE##
Instead of using the outdated \*buntu repository version of the IDE, grab a fresh copy from <http://www.arduino.cc>.  The repository flavored IDEs do not support Teensyduino very well, if at all.  Arduino 1.0.5 was the latest stable release as of this post.  Extract the file's contents into your home directory, then copy all files to /usr/share/arduino:

    cd ~
    tar xvf arduino-1.0.5-linux64.tgz
    sudo mkdir /usr/share/arduino
    sudo cp -R arduino-1.0.5-linux64/* /usr/share/arduino

Now edit the arduino startup script and tell it where to find itself:

`sudo gvim /usr/share/arduino/arduino`

    #!/bin/sh

    # changed by Yuri to affect custom arduino install w/Teensy support 
    # APPDIR="$(dirname -- "$(readlink -f -- "${0}")" )"

    APPDIR=/usr/share/arduino

    cd "$APPDIR"
     
    for LIB in \
        java/lib/rt.jar \
        java/lib/tools.jar \
        lib/*.jar \
        ;
    do
        CLASSPATH="${CLASSPATH}:${LIB}"
    done
    export CLASSPATH

    LD_LIBRARY_PATH=`pwd`/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}
    export LD_LIBRARY_PATH

    export PATH="${APPDIR}/java/bin:${PATH}"

    java -Dswing.defaultlaf=com.sun.java.swing.plaf.gtk.GTKLookAndFeel processing.app.Base "$@"

Now move the edited arduino script to /usr/bin and give it a whirl!

    sudo mv /usr/share/arduino/arduino /usr/bin/arduino
    arduino

With any luck, you'll arrive at an almost fully functional Arduino IDE window.

To enable serial support, add yourself to the dialout group, then log off and log in again:

`sudo usermod -a -G dialout yourUserName`

##Install Teensyduino##
Follow the instructions at PJRC - <http://www.pjrc.com/teensy/td_download.html>.  You will need to run the installer using sudo.  When the it asks for the Arduino directory, select /usr/share/arduino.

Now, when you open the Arduino IDE, Teensy boards will be available options, and Teensyloader will run when you compile/upload.

##Configure CMake##
After all that, I *hate* the Arduino IDE.  I'd rather work in a better text/code editor and build from the command line.  Enter arduino-cmake!

    cd ~
    git clone https://github.com/queezythegreat/arduino-cmake.git
    sudo mkdir /usr/share/arduino-cmake
    sudo cp -R arduino-cmake/cmake/* /usr/share/arduino-cmake

You now have a CMake toolchain installed.  It will use the avr utilities you installed a little while ago.  The Arduino IDE includes its own avr utilities in /usr/share/arduino/hardware/tools/, so binaries produced from the command line may not be identical to those produced within the IDE.  You could go to the trouble of creating symlinks in /usr/local/bin to all of the executable files in the IDE directory, but I don't think that's entirely necessary.

To test CMake, let's create use the blink.ino example sketch as a single file in its own directory:

    cd ~
    mkdir blink
    cd blink
    gvim blink.ino
<p> </p>
    /*
      Blink
      Turns on an LED on for one second, then off for one second, repeatedly.
     
      This example code is in the public domain.
     */
     
    // Pin 13 has an LED connected on most Arduino boards.
    // Pin 11 has the LED on Teensy 2.0
    // Pin 6  has the LED on Teensy++ 2.0
    // Pin 13 has the LED on Teensy 3.0
    // give it a name:
    int led = 13;

    // the setup routine runs once when you press reset:
    void setup() {                
      // initialize the digital pin as an output.
      pinMode(led, OUTPUT);     
    }

    // the loop routine runs over and over again forever:
    void loop() {
      digitalWrite(led, HIGH);   // turn the LED on (HIGH is the voltage level)
      delay(1000);               // wait for a second
      digitalWrite(led, LOW);    // turn the LED off by making the voltage LOW
      delay(1000);               // wait for a second
    }

Create a CMakeLists.txt:

`gvim CMakeLists.txt`

    #====================================================================#
    #                                                                    #
    # Author: Yuri                                                       #
    # Date  : 26 Feb 2014                                                # 
    #                                                                    #
    # Description: Simple CMakeLists.txt for Linux/Arduino               #
    #                                                                    #
    # Usage: put CMakeLists.txt source directory                         #
    #        cd source_directory                                         #
    #        mkdir build                                                 #
    #        cd build                                                    #
    #        cmake ..                                                    #
    #        make                                                        #
    #        make TARGET-burn   # burn firmware with programmer          #
    #        make TARGET-serial # start terminal emulation as desired    #
    #                                                                    #
    #====================================================================#

    # arduino toolchain
    set(CMAKE_TOOLCHAIN_FILE /usr/share/arduino-cmake/ArduinoToolchain.cmake)

    # CMake Version
    cmake_minimum_required(VERSION 2.8)

    # TARGET name - EDIT THIS FOR EACH NEW PROJECT
    set(TARGET_NAME blink)

    # language
    project(${TARGET_NAME} C CXX)

    # uncomment these lines to display lists during cmake
    # print_board_list()
    # print_programmer_list()

    # sketch file - EDIT THIS FOR EACH NEW PROJECT
    set(${TARGET_NAME}_SKETCH Blink.ino)

    # source file(s) if c or cpp are used instead of a sketch
    # set(${TARGET_NAME}_SRCS blink.cpp)

    # enables a serial target that will start terminal emulation
    set(${TARGET_NAME}_SERIAL picocom @SERIAL_PORT@ -b 9600 -l)

    # board - use print_board_list() for options
    set(${TARGET_NAME}_BOARD uno)

    # programmer - use print_programmer_list() for options
    # set(${TARGET_NAME}_PROGRAMMER usbtinyisp)

    # port - used for SERIAL target, also required to get TARGET-burn option
    set(${TARGET_NAME}_PORT /dev/ttyUSB0)

    # generate firmware
    generate_arduino_firmware(${TARGET_NAME})

With any luck, you should be able to build and upload the sketch at this point without ever having touched the IDE:

    mkdir build
    cd build
    cmake ..
    make
    make blink-upload

Explore the arduino-cmake directory that you cloned earlier for more info.  Also, check out <https://github.com/queezythegreat/arduino-cmake>.  Unfortunately, the toolchain doesn't support Teensy just yet, so you'll still have to use Teensyduino for that.

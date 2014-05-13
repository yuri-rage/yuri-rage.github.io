---
layout: post
title: "Zorin to the Max"
tagline: "Linux for everynerd"
description: ""
category: 
tags: []
date: "Tue, 13 May 2014 23:17:39 +0300"
---
{% include JB/setup %}

[Zorin OS](http://www.zorin-os.com) promises "Linux for everyone."  The distro is targeted toward Windows users who want a free/open source OS.  It does a pretty good job of delivering a fairly intuitive interface.  I've taken to using it because it automatically includes a lot of the often problematic multimedia/restricted content found in other *buntu and apt-based distros.  It makes a great all-around laptop or media center distro.

Here's a screenshot of my extremely clean desktop followed by step-by-step instructions on how I achieved it:

![Zorin Screenshot]({{ site.url }}/images/zorin-desktop.png)

##initial login...a wtf?! moment##

Installation is straightforward except for this: If the user account you configured during setup is nowhere to be found on the login screen, that's because it's hidden due to poor theme choices by the Zorin OS folks.  I solved the problem by blindly clicking just above the login prompt until my user account appeared as an option.  Subsequent logins will default to the last account used.

##let the defenestration begin!##

First, install the Faenza icon theme with these two commands:

    wget https://launchpad.net/~tiheum/+archive/equinox/+files/faenza-icon-theme_1.3.1_all.deb
    sudo dpkg -i ~/Downloads/faenza-icon-theme_1.3.1_all.deb

##tweak tool##

In the application menu, go to System Tools->Preferences->Tweak Tool.  Here are the settings I changed to clean up the desktop, including reduced title bar font sizes and mixed theme elements:

![Tweak Tool Screenshot]({{ site.url }}/images/tweak-desktop.png)

![Tweak Tool Screenshot]({{ site.url }}/images/tweak-fonts.png)

![Tweak Tool Screenshot]({{ site.url }}/images/tweak-theme.png)

##awn preferences##

avant-window-navigator is a fantastic panel/dock.  It defaults to a Windows 7 look.  Let's make it even more unobtrusive.  The only applets I use are the Cairo Main Menu, DockBarX, Expander, Log Out, and Shut Down.  The notification area took up too much screen space and included features that I will never use.  We'll add a clock later.  If you want to further customize the awn applet icons, just drag an image file from the file manager onto any dock icon.

![Awn Preferences Screenshot]({{ site.url }}/images/awn-prefs.png)

![Awn Preferences Screenshot]({{ site.url }}/images/awn-applets.png)

##what time is it?##
Try as I might, I couldn't find a decent clock applet for awn.  Also, with the auto-hide feature enabled, a clock applet would not be displayed most of the time, anyway.  I opted to use the [conky](http://conky.sourceforge.net/) system monitor as a system clock, instead.  Install it with `sudo apt-get install conky`.  Create the following text file in your home directory.  I saved it as .conky-clock.

    use_xft yes
    xftfont Roboto:bold:size=8
    xftalpha 1.0
    update_interval 1.0
    total_run_times 0
    own_window yes
    own_window_transparent yes
    own_window_argb_visual yes
    own_window_type normal
    own_window_class conky-clock
    own_window_hints undecorated,above,sticky,skip_taskbar,skip_pager
    background no
    double_buffer yes
    draw_outline no
    draw_borders no
    imlib_cache_size 0
    draw_shades no
    alignment top_right
    gap_x 100
    gap_y 0
    no_buffers yes
    uppercase no
    override_utf8_locale no
    color1 gray57

    TEXT
    ${alignr}${color1}${time %a, %e %B %G   %T}

Configure conky to start at login.  Open your newly enabled Cairo menu, then System Tools->Preferences->Startup Applications.  Click Add, and use these settings:

![Conky Startup Screenshot]({{ site.url }}/images/conky-startup.png)

The next time you log in, there will be a clock in the upper right of the display.  It will overlay other windows.  I find it unobtrusive and useful.  Experiment with the settings.  Run multiple instances of conky and/or add features for system monitoring.

##compiz config time bandit##

You're going to hate me for showing you this app.  It is installed by default and you'll likely spend the next few hours tweaking window manager settings.  Open the menu, then System Tools->Preferences->CompizConfig Settings Manager.  Here are my starting recommendations:

Lose the stupid window dodge animation (and all others!) by unchecking the animations effect:

![CCSM Screenshot]({{ site.url }}/images/ccsm-effects.png)

[Web Upd8](http://www.webupd8.org/2009/11/gain-space-by-removing-maximized.html) provided this tip for increasing screen space by removing title bars from maximized windows: 

![CCSM Screenshot]({{ site.url }}/images/ccsm-decoration.png)

And finally, you can gain a bit of real estate with a maximized instance of Google Chrome by right clicking the title bar and choosing "Use System Title Bar and Borders."  Note the conky clock still displayed over the tab bar.

![Zorin Screenshot]({{ site.url }}/images/zorin-chrome.png)


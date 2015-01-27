---
layout: post
title: "Bleeding edge bspwm"
tagline: "Nerd level: extreme"
description: ""
category: Geekery
tags: [linux]
date: "Mon, 26 Jan 2015 16:31:11 -0600"
---
{% include JB/setup %}

The [bspwm tiling window manager](https://github.com/baskerville/bspwm) is fast, new, and feature rich, but unfortunately sparsely documented.  Finding myself under the weather and bored, I decided to give it a spin.  I'm impressed!  Here's my experience so far, along with my (hopefully well-commented) configuration files.

![bspwm screenshot]({{ site.url }}/images/bspwm-tiled.png)

I've tried tiling window managers before.  I found Awesome to be anything but.  I was confused by xmonad, and I gave up on it perhaps too quickly.  i3 has promise, but I ditched it because it relies on home row navigation keybinding that is shifted right from the standard vi keys (why?!).  I'm sure the keybinding could be re-mapped, but I wanted to try bspwm, anyway.

Anticipate a steep learning curve, but I encourage you to stick with it for a while.  I'm finding myself reverting to my comfort-zone Openbox session quite rarely after only a few days of bspwm exploration.

bspwm installation is straightforward.  I first used it on a Debian-based Crunchbang partition (where I often test weird software that I may not keep, including Crunchbang itself!), which required [compiling it from source](https://github.com/windelicato/dotfiles/wiki/bspwm-for-dummies).  The dependencies were easily met via the apt package manager, and I was up and running quickly.  However, the preceding link did not adequately describe configuring sxhkd.  I found [a well commented sxhkdrc](https://github.com/lietk12/dotfiles/blob/master/.config/sxhkd/sxhkdrc) via Google and adopted much of it for my own use.  Once confident that I could actually use bspwm, I installed it (along with sxhkd) via the AUR on my daily-use Arch partition.

<sub><sup>[Openbox](http://openbox.org/wiki/Main_Page), by the way, is super fast and can be configured to be very useful and responsive without a desktop environment - if tiling isn't for you, try a [bare bones Openbox config](https://github.com/yuri-rage/yuri_config/tree/master/.config/openbox).</sup></sub>

If you use a display manager for graphical login such as `gdm`, `slim`, or `lightdm`, you'll likely need a [`/usr/share/xsessions/bspwm.desktop`](https://github.com/yuri-rage/yuri_config/blob/master/.yuri_misc_sys_files/bspwm.desktop) greeter file and [`/usr/[local]/bin/bspwm-session`](https://github.com/yuri-rage/yuri_config/blob/master/.yuri_misc_sys_files/bspwm-session) script in order to successfully log in with bspwm.

## bspc ##
Users interact with the bspwm workspace through `bspc` commands, typically triggered by `sxhkd` keybindings.  Though largely impractical except for testing/debugging a user configuration, `bspc` is fully supported for CLI use in a terminal window.  Learning how `bspc` commands work is the key to customizing the bspwm UI for personal workflow.  The [README in the project repository](https://github.com/baskerville/bspwm) is really just a regurgitation of the `bspwm`/`bspc` man pages, which are best put into context as you browse `sxhkdrc` examples [(1)](https://github.com/lietk12/dotfiles/blob/master/.config/sxhkd/sxhkdrc) [(2)](https://github.com/yuri-rage/yuri_config/blob/master/.config/sxhkd/sxhkdrc) [(3)](https://github.com/baskerville/bspwm/blob/master/examples/sxhkdrc).

## sxhkd ##
`sxhkd` is the default keybinding (hotkey) daemon for bspwm.  It is configured through `~/.config/sxhkd/sxhkdrc`.  Following this paragraph is a brief excerpt of [my sxhkdrc](https://github.com/yuri-rage/yuri_config/blob/master/.config/sxhkd/sxhkdrc).  The "super" key is the "Windows" key, the suggested default modifier key for most window actions.  I found the curly brace syntax fairly intuitive for single-line configuration of multiple keys that do similar actions.  It helps to seek out others' `sxhkdrc` files when configuring your system - [r/bspwm](http://www.reddit.com/r/bspwm) is a good place to start.

    # swap this window with the largest one in the layout
    super + m
    	bspc window -s biggest
    
    # switch/move window (vim keybinding)
    super + {_,shift + }{h,j,k,l}
    	bspc window -{f,s} {left,down,up,right}
    
    # switch windows sequentially (like an app/task switcher)
    super + {_,shift + }c
    	bspc window -f {next,prev}
    
    # circulate binary tree leaves back/forward ("rotate" window layout)
    super + {comma,period}
    	bspc desktop -C {backward,forward}
    
    # rotate binary tree clockwise/counter-clockwise
    super + ctrl + {comma,period}
        bspc desktop -R {-90,90}
 
## bspwmrc ##

This startup script ,`~/.config/bspwm/bspwmrc`, is roughly analagous to Openbox's `autostart` script.  It primarily contains global `bspc` rules and options, and can set wallpaper, start panels/applets etc.  [Here is mine](https://github.com/yuri-rage/yuri_config/blob/master/.config/bspwm/bspwmrc).  I enabled compositing through `compton`, knowing full well that it incurs a bit of system performance cost.

    #! /bin/sh

    bspc config border_width        0
    bspc config window_gap          2
    
    bspc config split_ratio         0.50
    bspc config borderless_monocle  true
    bspc config gapless_monocle     true
    bspc config focus_by_distance   true
    
    bspc monitor -d 01 02 03 04 05 06 07 08 09 10
    
    bspc rule -a Gimp floating=on
    bspc rule -a URxvtQuake sticky=on floating=on
    
    xsetroot -cursor_name left_ptr &
    hsetroot -fill ~/images/wallpaper.png
    compton --config "$HOME/.config/compton.conf" -b
    udiskie --tray &
    nm-applet &
    numlockx on &
    urxvt &
    sleep 1 && tint2 &

## Adding a panel ##
It seems that [bar](https://github.com/LemonBoy/bar) is the "default" of sorts for bspwm.  I didn't try it because it doesn't appear to have an auto-hide feature, which violates my maximum-screen-real-estate mantra.  I used my existing [tint2 configuration](https://github.com/yuri-rage/yuri_config/blob/master/.config/tint2/tint2rc), using the `tint2-svn` package available in the AUR.

`dmenu` is the suggested default menu/launcher.  Since it requires almost no overhead, I kept it, though I installed a slightly patched `dmenu-mousey-git` from the AUR, mostly for its xft (font) and xrdb (color) support.

## A "scratchpad" terminal (a la Quake) ##

![bspwm screenshot]({{ site.url }}/images/bspwm-scratchpad.png)

I've grown accustomed to using F12 bound to [this script](https://github.com/yuri-rage/yuri_config/blob/master/.yuri_misc_sys_files/scripts/urxvt-quake.sh) to bring up an rxvt (`urxvt`) dropdown terminal, often referred to as a "scratchpad."  Unfortunately, bspwm doesn't support the HIDDEN attribute toggled by `wmctrl` in the script.  I was able to add the same functionality with a keybinding in `sxhkdrc`.  Add `bspc rule -a URxvtQuake sticky=on floating=on` to `bspwmrc` for proper window placement.  `xdotool` is available in the Arch community repository.  This method is briefly described in the [Arch Wiki](https://wiki.archlinux.org/index.php/Bspwm), but required some refinement for use with rxvt.

    # open a "scratchpad" terminal (quake-style, in this case, on a 1366x768 display)
    F12
      xdotool search --onlyvisible --classname URxvtQuake windowunmap \
        || xdotool search --classname URxvtQuake windowmap \
        || urxvt -name URxvtQuake -geometry 110x20+297+1

## Graceful exit ##
While spawning a graphical logout window may not be tiling paradigm correct, I like it.  I modified the Crunchbang python exit script/widget for use in bspwm.  It's available in my [Linux config file repo](https://github.com/yuri-rage/yuri_config/blob/master/.yuri_misc_sys_files/bspwm-exit).  In order to have sxhkd trap `{XF86PowerOff}` button presses (the power button on my laptop), I had to include `HandlePowerKey=ignore` in `/etc/systemd/login.conf` and restart the `systemd-logind` daemon.  That step will differ on non-systemd distros.

## TODO: ##
I've yet to fully explore the way I want to do system monitoring with bspwm.  I already have some pretty well thought-out [`conky` configurations](https://github.com/yuri-rage/yuri_config/tree/master/.config/conky), but none of them really work (yet) in a tiling environment.  Dedicating a bspwm desktop to system performance/monitoring is likely the way to go, but I have not yet configured it.

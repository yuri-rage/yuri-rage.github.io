---
layout: post
title: "Rolling with Arch"
tagline: "Hardcore SnobGeekery"
description: ""
category: Geekery
tags: [linux]
date: "Thu, 05 Jun 2014 23:19:38 +0300"
---
{% include JB/setup %}
![Arch Screenshot]({{ site.url }}/images/arch-desktop.png)

[Arch Linux](https://www.archlinux.org/) is a far cry from the feature laden, n00b friendly, stable Zorin distro I last wrote about.  Though Arch can incorporate all of the features usually installed by default in popular desktop distros like Ubuntu and Mint, its initial installation is arguably lighter weight than even Ubuntu minimal.  Though it's highly configurable and uses a rolling release model a la Gentoo, Arch uses a package manager ([pacman](https://wiki.archlinux.org/index.php/pacman)) that installs precompiled binaries from the Arch community repositories.  Arch has a fantastic [wiki](https://wiki.archlinux.org/), so rather than rambling about its philosophy, advantages, and disadvantages, I'll simply recommend reading [The Arch Way](https://wiki.archlinux.org/index.php/The_Arch_Way).

In pursuit of a Linux distro upon which to build an HTPC/media center, I stumbled upon [CrunchBang](http://crunchbang.org/).  I really liked the lightweight desktop environment and familiar apt package management, but the Debian backbone fell short when it came to up to date software.  I wound up compiling a lot of software from source code, which ultimately led me to drop CrunchBang as an HTPC candidate.  I'd wanted to try Arch for a while, so I set out to create a CrunchBang-styled installation using Arch.

Arch is not for the faint of heart.  The base installation will present the user with nothing more than a login shell and root prompt.  Though the learning curve was a bit steep, I managed to get up and running after a couple of evenings of tinkering.  The result is a suite of media software along with my usual smattering of development tools alongside a lightweight, snappy, standalone installation of OpenBox (no desktop environment!).  I'm likely to stick with it for quite some time!

Since a complete, step-by-step guide would be quite lengthy and largely just repeat what's already covered in the [Arch wiki](https://wiki.archlinux.org/), I'll give an overview of my installation and provide some of my config files via GitHub.

##openbox standalone##
If you want a lightweight X environment, it doesn't get much better than OpenBox.  OpenBox is one of few window managers that provides window decorations and a desktop (right click) menu without the usual host of desktop environment helper apps.  Add [tint2](https://aur.archlinux.org/packages/tint2-svn/) (task manager), and a couple of taskbar applets ([volumeicon](https://www.archlinux.org/packages/community/x86_64/volumeicon/), [nm-applet](https://www.archlinux.org/packages/extra/i686/network-manager-applet/)), and you've got a very sleek, capable desktop without the overhead of GNOME, KDE, or even Xfce.  Add some fonts and theming, [compton](https://aur.archlinux.org/packages/compton-git/) (compositing), [hsetroot](https://www.archlinux.org/packages/community/x86_64/hsetroot/) (wallpaper), and [conky](https://aur.archlinux.org/packages/conky-lua/) (desktop widget) to round out the look and feel of a more traditional DE.

##transparent desktop terminal##
![Arch Screenshot]({{ site.url }}/images/arch-terminal.png)
I chose urxvt as my default terminal app.  It's very lightweight and surprisingly configurable via .Xresources.  I even managed to "theme" it with [solarized](http://ethanschoonover.com/solarized) colors.  Rather than installing tilda, guake, yakuake, etc, I decided to see if urxvt could reside semi-permanently and unobtrusively on my desktop...and it can!  The trick is to use the -name flag to give the window a unique name, make the window title static/identifiable, and then tell OpenBox and compton to treat that window somewhat uniquely.  Search for UrxvtDesktop in these files for the details:<br>
[autostart](https://github.com/yuri-rage/yuri_config/blob/master/.config/openbox/autostart)<br>
[.bashrc](https://github.com/yuri-rage/yuri_config/blob/master/.bashrc)<br>
[compton.conf](https://github.com/yuri-rage/yuri_config/blob/master/.config/compton.conf)<br>
[rc.xml](https://github.com/yuri-rage/yuri_config/blob/master/.config/openbox/rc.xml)<br>
[.Xresources](https://github.com/yuri-rage/yuri_config/blob/master/.Xresources)<br>

##transparency in configuration##
I created a GitHub repo with many of my config files.  You're welcome to dig through them and see what works for you.  For starters, look at .config/tint2/tint2rc, the keybindings in .config/openbox/rc.xml, the custom commands in .config/openbox/menu.xml, the opacity rules in .config/compton.config, and the .config/openbox/autostart script.  I'll try to keep the repo updated with my latest hacks.<br>
[GitHub repo: yuri_config](https://github.com/yuri-rage/yuri_config)<br>
The table below includes some more software recommendations.  Enjoy!

##yuri's software picks##
<table>
<tr><td><strong>package name</strong></td><td><strong>description</strong></td></tr>
<tr><td>accountsservice</td><td>manage users - supported by lightdm</td></tr>
<tr><td>acpi</td><td>battery status (cli)</td></tr>
<tr><td>alsa-utils</td><td>sound utilities (cli)</td></tr>
<tr><td>archey</td><td>display system info (cli)</td></tr>
<tr><td>astyle</td><td>source code formatter - can be integrated with vim</td></tr>
<tr><td>chromium</td><td>browser of choice...for now</td></tr>
<tr><td>chromium-libpdf</td><td>browser pdf plugin</td></tr>
<tr><td>chromium-pepper-flash</td><td>browser flash plugin</td></tr>
<tr><td>compton-git</td><td>wm compositor - "compiz light"</td></tr>
<tr><td>conky-lua</td><td>system monitor (X)</td></tr>
<tr><td>faenza-crunchbang-icon-theme</td><td>subdued icon theme</td></tr>
<tr><td>faenza-icon-theme</td><td>colorful icon theme</td></tr>
<tr><td>fdupes</td><td>remove duplicate files (cli)</td></tr>
<tr><td>feh</td><td>image previewer</td></tr>
<tr><td>gmrun</td><td>GUI run command w/autocompletion</td></tr>
<tr><td>gnome-themes-standard</td><td>familiar widget themes</td></tr>
<tr><td>gstreamer0.10-bad-plugins</td><td>required by some xbmc plugins</td></tr>
<tr><td>gstreamer0.10-good-plugins</td><td>required by some xbmc plugins</td></tr>
<tr><td>gstreamer0.10-ugly-plugins</td><td>required by some xbmc plugins</td></tr>
<tr><td>gvim</td><td>graphical editor, also installs vim</td></tr>
<tr><td>hsetroot</td><td>set desktop wallpaper</td></tr>
<tr><td>lightdm</td><td>lightweight graphical login manager</td></tr>
<tr><td>lightdm-gtk-greeter-settings</td><td>GUI configuration for gtk greeter</td></tr>
<tr><td>lightdm-gtk3-greeter-devel</td><td>lightdm greeter theme</td></tr>
<tr><td>lxappearance-obconf</td><td>GUI configuration for openbox</td></tr>
<tr><td>mediterraneannight-theme-git</td><td>dark widget themes</td></tr>
<tr><td>mesa</td><td>3d graphics</td></tr>
<tr><td>mp3gain</td><td>normalize mp3 audio files (cli)</td></tr>
<tr><td>network-manager-applet</td><td>system tray applet for network-manager</td></tr>
<tr><td>networkmanager</td><td>manage networks (wifi)</td></tr>
<tr><td>nodejs</td><td>javascript platform</td></tr>
<tr><td>obconf</td><td>GUI configuration for openbox</td></tr>
<tr><td>obmenu</td><td>GUI configuration for openbox menus</td></tr>
<tr><td>openbox-themes</td><td>widget themes</td></tr>
<tr><td>packer</td><td>wrapper for pacman - seamlessly integrates AUR</td></tr>
<tr><td>rxvt-unicode</td><td>lightweight terminal</td></tr>
<tr><td>scrot</td><td>screenshot (cli)</td></tr>
<tr><td>thunar</td><td>file manager (Xfce's default)</td></tr>
<tr><td>tint2-svn</td><td>task manager</td></tr>
<tr><td>ttf-dejavu</td><td>Vera based font family</td></tr>
<tr><td>ttf-google-fonts-git</td><td>TONS of fonts - Arimo, Cousine, Droid, Inconsolata, Roboto, etc</td></tr>
<tr><td>ttf-mac-fonts</td><td>mac fonts - Garamond, Lucida Grande, etc</td></tr>
<tr><td>ttf-microsoft-consolas</td><td>nice terminal font</td></tr>
<tr><td>ttf-ms-fonts</td><td>fonts for when other fonts don't work</td></tr>
<tr><td>udiskie</td><td>automount usb drives - try udiskie --tray &</td></tr>
<tr><td>vlc</td><td>media player</td></tr>
<tr><td>volumeicon</td><td>system tray volume icon</td></tr>
<tr><td>waldorf-ui-theme</td><td>subdued widget theme</td></tr>
<tr><td>wmctrl</td><td>manipulate X windows (cli)</td></tr>
<tr><td>xbmc</td><td>media center</td></tr>
<tr><td>xclip</td><td>X clipboard (cli)</td></tr>
<tr><td>xcursor-vanilla-dmz</td><td>mouse cursor theme</td></tr>
<tr><td>xf86-video-intel</td><td>Intel video drivers</td></tr>
<tr><td>xfburn</td><td>GUI cd burner (Xfce's default)</td></tr>
<tr><td>xfce4-notifyd</td><td>X notify daemon (Xfce's default)</td></tr>
<tr><td>youtube-dl</td><td>download youtube video/audio (cli)</td></tr>
</table>

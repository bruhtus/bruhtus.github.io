---
author: Robertus Diawan Chris
title: Tiling Window Manager For Efficiency
date: 2020-10-15
description: Efficient way to manage your opened window.
tags: [Linux]
ShowToc: true
TocOpen: false
---

> A brief explanation about tiling window manager and configuration tiling window manager that i've tried.

## A Brief Explanation About Window Manager

> A window manager is a system software that controls the placement and appearance of windows within a windowing system in a graphical user interface (GUI). It can be part of desktop enviroment (DE) or be used standalone[^1].

So basically window manager is how to place a window. There're three types of window manager (according to arch wiki[^1]): stacking (aka floating) window manager, tiling window manager, and dynamic window manager.

Here's a brief explanation about those three window manager:
- Stacking (aka floating) Window Manager:
All window manager that allow the overlapping of windows are considered stacking window manager, although it is possible that not all stacking window manager use the same method. You can check a few list of stacking window manager [here](https://wiki.archlinux.org/index.php/window_manager#Stacking_window_managers).
- Tiling Window Manager:
Tiling window manager manage the window so that no window are overlapping with each other. You can check a few list of tiling window manager [here](https://wiki.archlinux.org/index.php/window_manager#Tiling_window_managers).
- Dynamic Window Manager:
Dynamic window manager is a tiling window manager that positioned based on the preset layouts which user can switch. You can check a few list of dynamic window manager [here](https://wiki.archlinux.org/index.php/window_manager#Dynamic_window_managers).

For more detailed comparision of tiling window manager, you can check [here](https://wiki.archlinux.org/index.php/Comparison_of_tiling_window_managers).

## The Window Manager I've Tried

For now (at the times of writing this post), i only have tried 2 window manager. My first window manager is qtile and my second window manager is i3. You can check below for more (not really) detailed explanation(?).

### i3 Window Manager
i3 is one of tiling type window manager, for more info you can check at [their website](https://i3wm.org/). I'm just gonna explain configuration that i've made at my [github repo](https://github.com/bruhtus/i3_config). For the record, this is my current window manager (at the time writing this post). I'm using the manjaro i3 edition.

First of all, i changed the mod keybinding from super key (or some people call it windows key) to alt key. You can change the mod keybinding by change `set $mod Mod4` (super key) to `set $mod Mod1` (alt key) in config file (you can find config file in folder `.i3`). For the most part i used the default config from manjaro i3 edition but i add a few program and even changed the i3 status bar with polybar. Here's what i used in this config:
- [pywal](https://github.com/dylanaraps/pywal): I use this for color scheme around my i3 environment (such as terminal, border color around window, etc)
- [polybar](https://github.com/polybar/polybar): I use this to replace i3 status bar because i can place the clock and date in the middle (i have no ide how to do that in i3 status bar or even py3status) and it has quite a lot of customization. You can also use py3status if you want.
- [rofi](https://github.com/davatorium/rofi): I use this as application manager instead of dmenu, because it's more convenient for me (rofi appear in the middle meanwhile dmenu appear at the top).
- [conky](https://github.com/brndnmtthws/conky): I use this to take a glance what process currently taking up resources (for more detailed info i use htop).
- [flameshot](https://github.com/flameshot-org/flameshot): I use this to take screenshot.

#### Setting Up Keybinding
For setting up keybinding you can use `bindsym`, for example: `bindsym $mod+q kill` for close focused or currenly active window. Other than setting up keybindings, you can also set a program to do a certain thing, for example: `set $myTerm alacritty`, every thing that used $myTerm gonna access the command via terminal alacritty. Alacritty is my current (at the time of typing this post) terminal emulator, i also have xterm as a backup terminal emulator. Example of using `$myTerm`: `bindsym $mod+e exec $myTerm -e ranger` to open ranger file manager.

#### Setting Up Polybar
For setting up polybar, you need to move the default polybar config. In my case, the default config is in /usr/share/doc/polybar/ but if it's not there, you can use `locate polybar | grep config`.

First of all, make a polybar folder in `.config` folder. After that move the default polybar config into those folder (the path should be like this `.config/polybar/config`). You can change the default config to anything you want, but remember the bar name because we're gonna use the bar name to launch the polybar. The default bar name should be like this `[bar/example]`, you can change it to the name you want and please specify the monitor for the polybar. You can check you monitor name by typing `xrandr` on terminal. Here's an example how to set a monitor in polybar config: <br>
> After i typed `xrandr` on my terminal, i got my laptop screen name `eDP-1` so i'm gonna use my laptop screen to display the polybar.

```
[bar/mainbar-i3]

monitor = {env:MONITOR:eDP-1}
```

After you're done with your polybar config, the next step is to add launch.sh. What is launch.sh? well, it's basically to launch all of our polybar bar config (that has this naming scheme `[bar/example]`). Here's an example of launch.sh in two monitor (each bar config has different monitor assigned to it):

```
#!/usr/bin/bash

#Terminate already running bar instances
killall -q polybar

#Wait until the process have been shut down
while pgrep -u $UID -x polybar >/dev/null; do
    sleep i; done

#launch bar
polybar mainbar-i3 &
polybar secondbar-i3 &

echo "bars launched..."
```

#### Who Should Try or Use i3?
i3 is a 'manual' tiling window manager so it doesn't really have default layout which is different from dynamic 'tiling' window manager, you need to specify where the window opened (whether the window opened on the right or below). If you want to use i3 you might want to consider that. So, who should try or use i3? everyone who wants a tiling window manager and  doesn't really mind to have manually control where the window appear, that's all.

### Qtile Window Manager
Qtile is one of dynamic window manager that use python as basis configuration, for more info you can check at [their website](https://qtile.org/).

In my [config repo](https://github.com/bruhtus/qtile_config) i usually use MonadTall layout or Max layout. MonadTall layout basically split the first two window into half vertically and then for the third and so on gonna split the right window horisontally. Max layout basically have the application automatically take up the whole screen, that's all. For more build-in layouts you can check their documentation [here](http://docs.qtile.org/en/latest/manual/ref/layouts.html?highlight=layouts).

I used qtile window manager first before switch to i3 because the configuration is in python but what i don't really like is how qtile treat multiple screen. When i want to switch to second monitor, it swapped the application on second monitor to first monitor (currently active monitor) and that's not what i want, i just want to switch to different screen and not have application on that screen swapped with application on my currently active screen. It was confusing and then i tried i3wm after that.

The config in my repo is a basic config i've done because i don't really like the workflow of qtile. Sorry about that.

#### Who Should Try or Use Qtile?
If you're fine with the workflow of qtile, wants to try or use dynamic window manager, like to have or doesn't really mind  preconfigured layout, and have experience with python then you might want to try qtile. Don't get me wrong, qtile is good as a window manager but unfortunately it doesn't meet my needs.

## The Pros and Cons of Tiling Window Manager
Pros:
- You don't have to worry about placement of your window because it's automatically split the workplace for you.
- You use mouse less.
- All your application is right there without any overlap window.
> Surprise, you haven't close me yet so i'm gonna take up your computer resources.

Cons:
- If you open too much application then the application size gonna become smaller. (that's why use virtual desktop or workspace 1 to 8).
- It's kind of hard to setting for first time. After that hard time, you can backup your previous config and use it again in other computer. Nice.

## To Wrap Things Up
If you want to use keyboard-oriented navigation or you don't want to use mouse often, then you probably should try using tiling window manager. But, as i've mentioned above, it took time to learn how to configure it (and i think it's worth your time).

If you're still not sure, you can try it first in virtual machine (such as virtualbox or virt-manager) and then you could copy those config file to your new installation.

## Example of i3 Window Manager
- Moderate use:

![Example of i3wm](example-of-i3wm.jpg)

- Too much window in one workspace can be bad:

![Too much window](too-much-window.jpg)

[^1]: [Arch linux wiki (window manager)](https://wiki.archlinux.org/index.php/window_manager).

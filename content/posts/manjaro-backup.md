---
author: Robertus Diawan Chris
title: "Backup: The Options"
date: 2020-11-22T05:21:31+07:00
tags: [Linux]
ShowToc: true
---

> A few options about backing up your system, especially on arch-based distro. It's mostly for linux, i'm not sure if you can use it for mac os or windows too.

## Why Should You Backup Your System?

In case you're wondering why you should backup your system (for some reason), let me briefly explain. There's always a risk of failure when you're updating or installing a new package (to make things easier to understand, it's basically a program), when that happens, do you want to restore your system before the error occur or you want to re-install the whole system from scratch? your choice. For me personally, i want to restore my system before the error occur rather than re-install everything from scratch. *Ideally*, before you install something, you should backup your system just in case there's some trouble. If you're insane enough to backup your system everytime you want to install new stuff or update your system then go ahead, you do you.

In this post, i'm gonna explain two most popular backup solution, that is [clonezilla](https://clonezilla.org/) and [timeshift](https://teejeetech.in/timeshift/), and also a bonus tip for arch-based linux user.

## Clonezilla

For the record, i've never tried this. I only do the research about backup on linux and this tool came up quite often. For more info on how to use this tool, you can check youtube video [here](https://youtu.be/yQ9NpWZ74BU).

Clonezilla is a tool to backup your entire system. That's right, it backup your whole system. You might think "wow that's great, i don't need other backup tool!", you might want to hold that thought. Yeah it backup your entire system, even your empty storage. Let me explain, for example your system has 200 GB allocated storage and let's say you used only 100 GB so you have another 100 GB storage left. When i said clonezilla backup your entire system, it means clonezilla gonna backup the 200 GB of allocated storage for your system and not only 100 GB of used storage. So if you're gonna install the whole system again (which is possible with clonezilla apparently), you need minimum the allocated storage space from previous machine for your system. If you didn't provide the minimum storage space, you gonna get the error. Unless you strive for that error then it's not gonna be a problem but if you didn't expect the error then you might get confused. That's the downside of clonezilla as far as i know it. I personally didn't use it because of that downside but if it's an advatage for you then you should try it.

## Timeshift

This is a backup tool that i've always used since i started using linux on daily basis. The concept is simple, it made *checkpoint* for your system and then you can revert back to that *checkpoint* if something wrong after the update or after installing new stuff.

For arch-based linux user, this tool is a **must**. Arch-based linux is a rolling release linux, that means you get an update regularly and those update not always gonna work so there's a high chance your system gonna break from the update. This tool is a livesaver.

The difference between this and clonezilla is that this tool only backup the used storage and also exclude the home directory (you can include backup home directory in the setting). In case you're wandering why exclude the home directory is a default, here's an example: <br>
If you have 3 file on your home directory, let's just say 'anu.py', 'nganu.py', 'anumu.py' then you backup your system. After backing up your system you add another file, let's say 'itumu.py', then you revert back to the *checkpoint* you created previously. After you revert back, the file 'itumu.py' is gone because it's re-write everything according to *checkpoint* list and file 'itumu.py' not in the *checkpoint* list yet. That's why excluding home directory is a default, you can change it in the setting if you want.

> I've never used this tool on newly installed system like clonezilla did, so i'm not sure if you can using that `checkpoint` on newly installed system.

## Bonus Tip

Here's a bonus tip for arch-based linux user, just in case those two tools didn't save your system (it's not gonna hurt if you have another backup option you know).

Type this two command in your terminal: <br>
- For official arch repository: `pacman -Qqen > pkglist-repo.txt`
- For arch user repository (AUR): `pacman -Qqem > pkglist-aur.txt`

What it did is make a package list installed on your system from official arch repository and AUR and save it to `.txt` file. After that you can install all your package with those file using the command below: <br>
- For official arch repository: `sudo pacman -S --needed - < pkglist-repo.txt`
- For arch user repository (AUR): `for x in $(cat pkglist-aur.txt); do pamac build $x; done` <br>
(you can also use other alternative such as `yay`, you don't need to use `pamac` if you don't want to).

For more info you can check my repo [here](https://github.com/bruhtus/package-backup), i write a few references there.

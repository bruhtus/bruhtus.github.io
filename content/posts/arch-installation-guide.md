---
author: Robertus Diawan Chris
title: "Arch Linux Installation Guide (Feb 2021)"
date: 2021-02-23T07:14:39+07:00
tags: [Linux]
ShowToc: true
---

> Don't install arch linux, install TempleOS instead. All hail HolyC! (R.I.P Terry). Jokes aside, please keep in mind that this guide might become obsolete because the nature of rolling release so you should always check the [arch wiki installation guide](https://wiki.archlinux.org/index.php/installation_guide).

## A Brief Intro

I'm trying to install an OS as minimal as possible but not from scratch like [Linux From Scratch](http://www.linuxfromscratch.org/) or compiling everything like [Gentoo](https://www.gentoo.org/). I might try to install both of them but i don't think i'm gonna use one of them as my main OS.

## Pre-Installation

First thing to do is download the iso from [here](https://archlinux.org/download/) and make bootable usb drive. After that check if your machine using legacy BIOS or UEFI by doing `ls /sys/firmware/efi/efivars`. If it shows directory without error then the system is booted in UEFI mode, and if the directory doesn't exist then the system may be booted in legacy BIOS (or CSM). Most modern computer usually using UEFI but please check first because it could save you a lot of time during the installation.

For this guide, i'm installing arch linux on machine with UEFI so i won't cover the legacy BIOS installation here.

## Installation
Here's step-by-step arch linux installation

### Live Environment
After booting into live environment, there're a few things you need to do. Below is the things you need to do inside live environment.

#### Set Keyboard Layout
The default keyboard layout is US so if you want to use US keyboard layout then you can skip this step. You can check available layouts using this command
```sh
ls /usr/share/kbd/keymaps/**/*.map.gz
```
and to modify the layout you can use `loadkeys`. For example, to set a german keyboard layout you can do
```sh
loadkeys de-latin1
```

#### Verify The Boot Mode
If you already know which one your machine use (legacy BIOS or UEFI), then boot into the right boot mode. You can verify the boot mode by using the same command
```sh
ls /sys/firmware/efi/efivars
```

#### Connect To The Internet
At the time of writing this guide, the installation ISO using `IWD` (Internet Wireless Daemon) to connect to wifi. You can check if you have internet connection or not by doing this
```sh
ping google.com #or any website you like such as pornhub.com
```

If there's no internet connection, you can do:
- If you're using ethernet, plug in the cable.
- If you're using wifi, you can use `iwctl` (IWD command).

Here's the basic command after you use `iwctl` (don't forget to press enter to execute the command):
```sh
device list #to check the device name
station <device-name> scan #to scan the wifi
station <device-name> get-networks #to check the available networks
station <device-name> connect <wifi-name> #and then enter the password if there's any
exit #exit iwctl
```

After that check again using the `ping` command.

#### Update The System Clock
After there's an internet connection, you can update the system clock by using
```sh
timedatectcl set-ntp true
```
to check the service status, use `timedatectl status`

#### Partition The Disks
> This is for UEFI, for legacy BIOS please check [arch wiki](https://wiki.archlinux.org/index.php/installation_guide#Update_the_system_clock).

To check the disks that available, you can use `lsblk` command and pick which disk you want to install arch linux on. The disk start with `sd` and without a number at the end like `sda`

For the disk partitions, i use `cfdisk` command. For example, `cfdisk /dev/sda` (replace `sda` with the disk you want to use).

> I didn't make swap partition so if you want to make one please refer to [arch wiki](https://wiki.archlinux.org/index.php/installation_guide#Update_the_system_clock).

The first partition is bootloader, usually around 120M and 512M (M means megabytes) depends on what bootloader you want to use. And then change the partition type to `EFI System`.

The second partition is root directory. I use everything that's left but you can spare some for another partition. And then change the partition type to `Linux Filesystem`.

After you're sure about your disk partitions, then write the changes and then quit.

#### Format The Partitions
The next step is format the disk partition. For bootloader partition, format it to `FAT32` by using this command
```sh
mkfs.fat -F32 /dev/<efi-system-partition>
```
For root directory, format it to `ext4` by using this command
```sh
mkfs.ext4 /dev/<root-partition>
```

For example:
```sh
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
```

#### Mount The Filesystems
> More info please refer to [EFI System Partition Arch Wiki](https://wiki.archlinux.org/index.php/EFI_system_partition#Mount_the_partition).
The next step is mount the partition that we've made. First, mount root partition to `/mnt` using this command
```sh
mount /dev/<root-partition> /mnt
```
after that create two directories, `/mnt/boot` and `/mnt/boot/efi` (basically create efi directory inside boot directory which both of them doesn't exist so you need to create one by one). You can create both directories using this command
```sh
mkdir -p /mnt/boot
mkdir -p /mnt/boot/efi
```
after that mount your bootloader to `/mnt/boot/efi` using this command
```sh
mount /dev/<efi-system-partition> /mnt/boot/efi
```

For example:
```sh
mount /dev/sda2 /mnt
mount /dev/sda1 /mnt/boot/efi
```

#### Install Essential Packages
You can install essential package by using this command
```sh
pacstrap /mnt base base-devel linux linux-firmware vim
```
- `/mnt` is where you mount your root partition.
- `base` is base packages.
- `base-devel` is development tool such as `sudo` and `grep.
- `linux` is the kernel.
- `linux-firmware` is the necessary part of linux kernel.
- `vim` is the text editor (i don't even install `nano`, sorry).

#### Generate Fstab
> Fstab is for telling the kernel what to load in the booting process.

To generate fstab use this command
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```
`-U` means using unique ID instead of human readable ID such as `sda`. You can check the content of fstab by opening `/mnt/etc/fstab` file using your text editor (in my case is vim, so i use `vim /mnt/etc/fstab`).

#### Change Root Into New System
To take a look inside our new system, we can change root to our new system by using this command
```sh
arch-chroot /mnt
```

### Configure New System
After setup our new system in live environment, now we need to configure our system inside the system (not in live environment anymore) after change root into our new system. There're a few things you need to configure, below is the things you need configure inside new system.

#### Set The Time Zone
To set the time zone, we can use this command
```sh
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```
For example:
```sh
ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```
You can check the region or the city by doing this
```sh
ls /usr/share/zoneinfo/Region
```
and
```sh
ls /usr/share/zoneinfo/Region/City
```
or just double `<tab>` while typing `ln -sf /usr/share/zoneinfo/`

After that, generate the clock using `hwclock` with this command
```sh
hwclock --systohc
```

#### Select Your OS Language
First, you need to edit `etc/locale.gen` using your favorite text editor (which is not nano for me) and uncomment the language you want.

For example, you want to use US English as your OS language then what you need to uncomment is `en_US.UTF-8 UTF-8` and `en_US ISO-8859-1`.

After that, use this command to generate locales
```sh
locale-gen
```
and then create the `locale.conf` file. For example:
```sh
vim /etc/locale.conf #you can replace vim with your favorite text editor
```
the content of `locale.conf` is
```sh
LANG=en_US.UTF-8 #if you choose US English
```
If you set the keyboard layout (at the beginning of installation), then you make the changes persistent in `vconsole.conf`. For example:
```sh
vim /etc/vconsole.conf
```
with the content
```sh
KEYMAP=de-latin1
```

#### Network Configuration
For the network configuration, create the `hostname` file with the name you want (it's not the user name, it's more like your machine name). For example:
```sh
vim /etc/hostname
```
with the content
```sh
bruhtus
```

After that, add this content to `/etc/hosts`
```sh
127.0.0.1	localhost
::1			localhost
127.0.1.1	<yourhostname>.localdomain	<yourhostname>
```
For example: <br>
The previous step i define my hostname as bruhtus, so what i need to do is add this content to `/etc/hosts`
```sh
127.0.0.1	localhost
::1			localhost
127.0.1.1	bruhtus.localdomain	bruhtus
```
> If the system has a permanent IP address, it should be used instead of 127.0.1.1.

After that, install network manager using this command `pacman -S networkmanager wpa_supplicant` and then activate on startup using `systemctl enable NetworkManager` and `systemctl enable wpa_supplicant`.

#### Set The Root Password
To set the root (or admin) password, use this command
```sh
passwd
```
and then enter your password.

#### Add New User
> The default user is only root (or admin).

To create new user, use this command
```sh
useradd -m <username>
```
after that create the password for that username by using this command
```sh
passwd <username>
```
and then enter your password.

For example:
```sh
useradd -m bruhtus #i like my hostname and my username to be the same
passwd bruhtus
```
and then enter the password.

#### Add New User To Group
> It's so that the new user can have root access using sude

To add new user to group, we can use this command
```sh
usermod -aG wheel,audio,video,optical,storage <username>
```

#### Edit Sudoers File
> Add the wheel group to have root access with sudo

Use `visudo` and look for `wheel` and then uncomment those line.

The line would be like this
```sh
%wheel ALL=(ALL) ALL
```

By default, visudo using `vi` to edit the file. If you didn't want to use vi then you can do
```sh
EDITOR=vim visudo
```

#### Install Bootloader
> In my case, i use grub as the bootloader. So, i'm just gonna cover grub installation in this section.

First, install the bootloader and efibootmgr using this command
```sh
pacman -S grub efibootmgr
```

After that, use this command to install grub on system partition
```sh
grub-install /dev/<disk-partition>
```

For example:
```sh
grub-install /dev/sda #without any number at the end
```

If there's no error, then generate grub config using this command
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

> If there's an error, please check [here](https://wiki.archlinux.org/index.php/GRUB#Installation_2) or use search engine. I can't cover every single error.

### Reboot

If there's no error, exit from chroot using `exit` command. After that, unmount the system using `umount -R /mnt` and then reboot (please make sure the disk partition is priority in bios).

## Graphical User Interface
If you only use command line interface, then you don't need to continue. But, if you still need graphical user interface (GUI) to open browser or something like that, then keep going.

### Login To Your Arch Installation
After reboot, login to your user you just created (in my case, it's `bruhtus`). After that check the internet connection by using `ping` command (for example: `ping google.com`). If there's an error then connect to your network using `nmtui` command. After that check the connection using `ping` command again. If there's still an error, please check arch wiki or use your search engine.

### Install And Configure Graphical Environment
Install a few package using this command
```sh
sudo pacman -S xorg xorg-init alacritty lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings i3-gaps
```
- `xorg` and `xorg-init` is for graphical interface.
- `alacritty` is the terminal emulator i choose (you can replace that with your favorite terminal emulator).
- `lightdm`, `lightdm-gtk-greeter`, and `lightdmn-gtk-greeter-settings` is for login manager (unless you want to start your graphical environment manually like a champ, then you should probably install one).
- `i3-gaps` is the window manager i choose (you can replace that with your favorite window manager).

> Please keep in mind that i use standalone window manager so i need to install xorg and xorg-init manually, but if you choose desktop environment then i think you don't need to install those two.

After everything installed, copy the `xinitrc` using this command
```sh
cp /etc/X11/xinit/xinitrc ~/.xinitrc
```
What that command do is copy `xinitrc` to your home directory. What `.xinitrc` do is start the window manager or desktop environment you choose (we'll get to that later in this post).

The next step is to edit `.xinitrc`, you can do the following step:
- Remove the last 5 row (from `twm &` until `exec xterm -geometry 80x66+0+0 -name login`)
- And then add `exec i3` in the last row (basically replace the last 5 row with this `exec i3`).

> Twm is default xorg window manager, in case you're wondering.

Please keep in mind that i use i3 window manager so i use `exec i3`. To makes thing simpler, `exec` is where you start your window manager or desktop environment so the usual syntax would be like this `exec <window-manager>`.

After that, enable login manager by using this command:
```sh
sudo sysmtemctl enable lightdm
```

The last step is to check if there's an error or not by using this command `startx` to start xorg. If everything fine (you can access your window manager or desktop environment) then reboot.

> For intel user, if there's an error while using `startx`, you can try installing `xf86-video-intel` first and see if that fix the error or not. If that doesn't fix your error then you know what to do.

## The Conclusion

That's all you need to do to install arch linux. After that you can install all the program you want. I suggest you to try it first inside virtual machine such as virtualbox, virt-manager or something else.

## References
- [Arch wiki installation guide](https://wiki.archlinux.org/index.php/installation_guide).
- [Arch wiki grub installation](https://wiki.archlinux.org/index.php/GRUB#Installation_2).
- [Arch wiki mount EFI system partition](https://wiki.archlinux.org/index.php/EFI_system_partition#Mount_the_partition).
- [Mental outlaw arch installation video part 1](https://www.youtube.com/watch?v=rUEnS1zj1DM&t=1030s).
- [Mental outlaw arch installation video part 2](https://www.youtube.com/watch?v=CwLCcRY-xac&t=796s).
- [Iwd usage video](https://www.youtube.com/watch?v=bOzLwODMqIo&t=297s&ab_channel=EF-LinuxMadeSimple).
- [Distrotube arch installation video part 1](https://www.youtube.com/watch?v=PQgyW10xD8s).
- [Distrotube arch installation video part 2](https://www.youtube.com/watch?v=pouX5VvX0_Q&t=847s).

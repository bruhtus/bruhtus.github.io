---
author: Robertus Diawan Chris
title: "Install Arch Linux on Virtual Machine"
date: 2025-03-02T05:48:13+07:00
tags:
ShowToc: true
---

## A Brief Intro

In this post, we'll install arch linux on virtual machine with QEMU and add a
shared directory between host machine and virtual machine.

Before we start, make sure to install QEMU on your system. If your system
using arch linux too, we can install `qemu-base` and `tigervnc` package.
`tigervnc` package for viewing the virtual machine from QEMU. We will be using
SeaBIOS, which is legacy BIOS implementation and the default BIOS on QEMU (at
the time of writing this post).

This post assume that you use `ext4` filesystem format. If you use another
file format, like `btrfs`, you might need to use different configuration.

## QEMU Disk Image

The first thing we need to do is to create a disk image. We can do that with
this command:
```sh
qemu-img create -f qcow2 <image-path> 15g
```

- `qcow2` is the current QEMU image format, and one of the benefit of it is that
it will increase the image size based on the virtual machine usage with the
limit being the image size given when creating the image.
- `<image-path>` is where we store the image. For example:
`~/arch-linux-qemu`.
- `15g` is the image size.

## Installation

The first thing we need to do is download the arch linux ISO on the [download
page](https://archlinux.org/download/).

After that, we can use this command:
```sh
qemu-system-x86_64 -enable-kvm -cdrom <path-to-arch-linux-ISO>.iso -boot
order=d -drive file=<image-path> -m 2g
```

- `-enable-kvm` is to enable full virtualization using KVM. Our machine might
not support KVM, so make sure we have KVM support when using this flag. We can
read the [arch
wiki](https://wiki.archlinux.org/title/KVM#Checking_support_for_KVM) for the
instruction.
- `-cdrom <path-to-arch-linux-ISO>.iso` is to use the file as CD-ROM image.
- `-boot order=d` is to specify that we want to boot from CD-ROM first.
- `-drive file=<image-path>` is to specify the disk image to use as the drive
on the system. Image path should be a full path like
`$HOME/arch-linux-img`
- `-m 2g` is to specify the RAM to use in the virtual machine.

Mostly we can follow the [arch linux installation guide](https://wiki.archlinux.org/title/Installation_guide), but be careful of the partition. Because we will use the default QEMU
BIOS, which is a legacy BIOS, we need to partition the drive with legacy BIOS
in mind. We can check on the partition example layout on [arch
wiki](https://wiki.archlinux.org/title/Partitioning#Example_layouts). For this
post, we'll use BIOS/GPT layout to not deviate too much from the UEFI/GPT
layout.

If after running the command above, the virtual machine is not automatically
appear, we can use command:
```sh
vncviewer :<port>
```

Make sure to install `tigervnc` or something similar before running the
command. We can get the port from the QEMU command above. For example:
```sh
VNC server running on ::1:5900
```
`5900` is the port we want.

After inside the virtual machine, you can follow the usual arch linux
installation guide. To be able to use host machine internet connection, we
might want to install `dhcpcd` in the `pacstrap` step, like this:
```sh
pacstrap -K /mnt base linux linux-firmware dhcpcd
```
and enable it after `arch-chroot` like this:
```sh
systemctl enable dhcpcd
```

The important part is the drive partition, so we'll skip into the partition
part.

Personally, i don't really need swap on my virtual machine so in this post
we'll only make 2 partition:
- BIOS boot partition (1 MB) with partition type `BIOS boot`.
- root partition (the rest of storage) with partition type `linux root (x86-64)`.

We can use `cfdisk /dev/<drive>` to partition our drive. To look what our
drive name is, we can use `lsblk` command. For example:
```sh
cfdisk /dev/sda
```

After the partition, we need to format the root partition to `ext4` with this
command:
```sh
mkfs.ext4 /dev/<root-partition>
```
for example:
```sh
mkfs.ext4 /dev/sda2
```

After that, we can mount the root partition with this command:
```sh
mount /dev/<root-partition> /mnt
```
for example:
```sh
mount /dev/sda2 /mnt
```

After that we can follow the rest of the arch linux installation guide until
we need to install the bootloader.

For this post, we'll use `grub2` as our bootloader. First, we need to install
grub with this command (after running `arch-chroot`):
```sh
pacman -S grub
```

After grub installed, we can use this command to install the bootloader:
```sh
grub-install --target=i386-pc /dev/<drive-name>
```
keep in mind that we need to provide the drive name, not the partition name.
For example:
```sh
grub-install --target=i386-pc /dev/sda
```

After that, we need to generate the grub config with this command:
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

To run the virtual machine, we can use this command:
```sh
qemu-system-x86_64 -enable-kvm -nic user,hostfwd=tcp::2222-:22 -m 2g -smp cores=4,cpus=4 <image-path>
```

If we want to add shared directory between virtual machine and host machine,
we can use `-fsdev` and `-device` like this:
```sh
qemu-system-x86_64 -enable-kvm -nic user,hostfwd=tcp::2222-:22 -fsdev local,id=fs1,path=<host-shared-directory-path>,security_model=none -device virtio-9p-pci,fsdev=fs1,mount_tag=<mount_tag> -m 2g -smp cores=4,cpus=4 <image-path>
```
and then add to `/etc/fstab` like this:
```
...
<mount_tag> <mounting-path-on-virtual-machine> 9p uid=xxxx,gid=xxxx,trans=virtio 0 0
```

Let's say we have `<mount_tag>` as `shared_directory` and we want to mount it
on `/home/user/shared`. Also, to get the `uid` and `gid` we can use `id <user>`,
for example `id bruhtus`. Let's assume the `uid` and `gid` of the user is
`1000`. We can use the command like this:
```sh
qemu-system-x86_64 -enable-kvm -nic user,hostfwd=tcp::2222-:22 -fsdev local,id=fs1,path=<host-shared-directory-path>,security_model=none -device virtio-9p-pci,fsdev=fs1,mount_tag=shared_directory -m 2g -smp cores=4,cpus=4 <image-path>
```
and in `/etc/fstab` like this:
```
shared_directory /home/user/shared 9p uid=1000,gid=1000,trans=virtio 0 0
```

Alright, that's it. See you next time!

## Side Note

If we want to use `shutdown` or `reboot` command from non-root user, we can
install `polkit` package, like this:
```sh
pacman -S polkit
```

## References

- [FLUSP Use QEMU To Play with Linux Kernel](https://flusp.ime.usp.br/kernel/use-qemu-to-play-with-linux/)
- [Create an ArchLinux image for kernel
testing](https://andrealmeid.com/post/2020-03-10-bootstrap-arch/).

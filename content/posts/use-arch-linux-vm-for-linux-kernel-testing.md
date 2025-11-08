---
author: Robertus Diawan Chris
title: "Use Arch Linux VM for Linux Kernel Testing"
date: 2025-03-30T11:14:44+07:00
tags: [Notes, Linux]
ShowToc: true
---

## A Brief Intro

Have you wondered "how can we test a linux kernel without breaking our linux
operating system installation?". One of the method is using virtual machine
(VM) and in this post we'll use arch linux in virtual machine to try out
different version of linux kernel.

There are 2 scenarios that we will discuss on this post:
1. Using arch linux VM with arch linux as host system.
2. Using arch linux VM with different linux distro as host system.

We're going to work with 2 systems on 1 machine, so we need to know which
command need to run on which machine. For that purpose, we're going to use
this indicator to differentiate:
```sh
# @host   # for real machine system
# @guest  # for virtual machine system
```

## Arch Linux as Host System

The prerequisite for this scenario is that we need to use arch linux on the
real machine or host machine we want to install the virtual machine.

After we make sure that the host machine system we have is using arch linux,
the first thing we need to do is create a file to store arch linux
installation for the virtual machine. We can define how much space we'll
allocate to the installation, but in this post we'll use 15G just to be save.
To create a file for virtual machine OS installation, we can use this command:
```sh
# @host
truncate -s 15g arch-linux-disk.raw
```

We can name the file whatever we want, but in this case, we'll use
`arch-linux-disk`.

After that, we need to create a filesystem volume by formatting the file
into `ext4` with this command:
```sh
# @host
mkfs.ext4 arch-linux-disk.raw
```

And then, we can mount the filesystem partition into `/mnt` like this
(assuming we're not login as root user):
```sh
# @host
sudo mount arch-linux-disk.raw /mnt
```

> If your `/mnt` is occupied, you can use another directory such as `vm`, like
> this: `sudo mount --mkdir arch-linux-disk.raw vm`

After mounting the filesystem, make sure `arch-install-scripts` and `qemu-base`
package installed on our system by running this command:
```sh
# @host
sudo pacman -S arch-install-scripts qemu-base
```

> `arch-install-scripts` contains some useful scripts to install arch linux such
> as `pacstrap` and `arch-chroot`. `qemu-base` is for creating the virtual
> machine we will use later.

After those packages installed, we can use this command to install arch linux
on `arch-linux-disk.raw`:
```sh
# @host
sudo pacstrap -c /mnt base base-devel vim dhcpcd
```

> flag `-c` is to use package cache on host system, so we only need to
> download some out of date packages rather than downloading all the packages.
> `base` is a package for basic arch linux installation and `base-devel` is
> for packages such as `sudo` and `grep`. `vim` is my editor of choice, so you
> don't have to install this if you don't want to. `dhcpcd` is to connect the
> virtual machine system to the internet.

After everything installed, we can go into the new filesystem with this
command:
```sh
# @host
sudo arch-chroot /mnt
```

After that, we can setup password for root user with this command:
```sh
# @guest
passwd
```

Also, we can start `dhcpcd` for internet connection on our virtual machine
with this command:
```sh
# @guest
systemctl enable --now dhcpcd
```

To exit from the guest filesystem, we can use `ctrl-d` or `exit` command.
After exit from the guest filesystem, we can unmount the filesystem using this
command:
```sh
# @host
sudo umount /mnt
```

### Use Custom Linux Kernel on QEMU VM

Before we spin up our virtual machine, we need to compile the linux kernel.

First thing we need to do is download the linux kernel source code from one of
available source on https://web.git.kernel.org/pub/scm/linux/kernel/git. In
this post, we will download the source from Linux Torvalds source code. For
example, let's say we want to download the source code for tag v6.14, we can
use `wget` like this:
```sh
# @host
wget https://web.git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/snapshot/linux-6.14.tar.gz
```

Or we can just click on the Download link. Or we can clone the repo with this
command:
```sh
# @host
git clone --depth=1 git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git torvalds-kernel
```

> `--depth=1` means that we download 1 commit instead of all the commits.
> `torvalds-kernel` is the directory name where we clone the linux kernel source
> code git repository (instead of naming it `linux`, we name it
> `torvalds-kernel`).

After we download the linux kernel source code, we can change directory into
it. And then, we can create a basic config x86_64 config using this command:
```sh
# @host
make x86_64_defconfig
```

And then we need to add config for virtual machine with this command:
```sh
# @host
make kvm_guest.config
```

After that, we can compile the linux kernel with this command:
```sh
# @host
make -j <n>
```

> `<n>` is how much threads we use. For example, `make -j 8` means we use 8
> threads.

We can change the config with command:
```sh
make nconfig
```

> Please keep in mind, DO NOT directly edit the `.config` file. Use `make
> nconfig` instead.

After we finish compiling the linux kernel, we can use that linux kernel in
our QEMU virtual machine with this command:
```sh
# @host
qemu-system-x86_64 \
    -hda /path/to/arch-linux-disk.raw \
    -m 2g \
    -nographic \
    -kernel /path/to/linux-6.14/arch/x86/boot/bzImage \
    -append "root=/dev/sda rw console=ttyS0 loglevel=5" \
    -enable-kvm \
    -nic user,hostfwd=tcp::2222-:22
```

For example:
```sh
# @host
qemu-system-x86_64 \
    -hda $HOME/.local/state/qemu/kernel-dev-disk.raw \
    -m 2g \
    -nographic \
    -kernel $HOME/.local/state/qemu/linux-6.14-rc5/arch/x86/boot/bzImage \
    -append "root=/dev/sda rw console=ttyS0 loglevel=5" \
    -enable-kvm \
    -nic user,hostfwd=tcp::2222-:22
```

Here's a brief explanation of each flags:
- `-hda` is to use a file as a hard disk. `-hda` is equivalent to `sda`
partition, `-hdb` is equivalent to `sdb`, and so on.
- `-m` is RAM size for the virtual machine.
- `-nographic` make QEMU run on the terminal instead of graphical window.
- `-kernel` is to define the custom kernel we'll use in virtual machine.
- `-append` is to define kernel parameters we'll be using. We can't use this
without `-kernel` flag. For more info about kernel parameters, we can take a
look the [kernel
docs](https://www.kernel.org/doc/html/v4.14/admin-guide/kernel-parameters.html).
- `-enable-kvm` is to enable KVM virtualization support (if available).
- `-nic` is to forward internet connection of host machine to virtual machine.

To exit the virtual machine in `nographic` mode, we can use `ctrl-a x`.

## Another Linux Distro as Host System

If we use another linux distro, we need to install arch linux in virtual
machine from scratch. You can take a look at [my previous
post](../install-arch-linux-on-virtual-machine) for how to
install arch linux using QEMU. Please keep in mind that you need to install
`qemu` and `vncviewer` package on your host system.

### Use Custom Linux Kernel on QEMU VM

After installing arch linux in virtual machine, we need to compile the linux
kernel based on the kernel config in the virtual machine.

We can run the virtual machine with shared directory with this command:
```sh
# @host
qemu-system-x86_64 \
    -m 2g \
    -enable-kvm \
    -fsdev local,id=fs1,path=/path/to/linux-kernel,security_model=none \
    -device virtio-9p-pci,fsdev=fs1,mount_tag=shared_directory \
    -nic user,hostfwd=tcp::2222-:22 \
    <qemu-image>
```

For example:
```sh
# @host
qemu-system-x86_64 \
    -m 2g \
    -enable-kvm \
    -fsdev local,id=fs1,path=$HOME/.local/state/qemu/linux-6.14,security_model=none \
    -device virtio-9p-pci,fsdev=fs1,mount_tag=shared_directory \
    -nic user,hostfwd=tcp::2222-:22 \
    arch-linux-img
```

> Please don't forget to add the shared directory in `/etc/fstab`.

In the example above, we're using the linux kernel source code directory as a
shared directory.

Inside the virtual machine, we need to copy the kernel config into the
shared directory. Assuming our shared directory is in `/home/user/shared`,
we can use this command:
```sh
# @guest
zcat /proc/config.gz > /home/user/shared/.config
```

After we got the config, we need to adjust the config so that we
only compile linux kernel with the module we need rather than compiling with
all module enabled. This can save time to compile the linux kernel. To do that
we can use this command:
```sh
# @guest
make localmodconfig
```

Sometimes, there are new features or new config parameters in the
source code repo and we need to confirm if we want to add those new
config parameters in our `.config` file or not. To reduce the amount of
confirmation we need to make, we can use this command:
```sh
# @guest
make olddefconfig
```

Also, we need to add a config for virtual machine with this command:
```sh
# @guest
make kvm_guest.config
```

If we want to edit some config parameters, we can use:
```sh
# @guest
make nconfig
```

After that, we can shutdown the virtual machine and compile the linux kernel
with this command:
```sh
# @host
make -j <n>
```

> `<n>` is how much threads we use. For example, `make -j 8` means we use 8
> threads.

And then, we need to compile the linux kernel modules with this command:
```sh
# @host
make modules
```

After we compile both linux kernel and the modules, we can turn on the virtual
machine again and put the modules into `/lib/modules/<kernel-name>` in
the virtual machine with this command:
```sh
# @guest
sudo make modules_install
```

And then, we need to copy the linux kernel into `/boot` with this command:
```sh
# @guest
sudo cp /home/user/shared/arch/x86/boot/bzImage /boot/vmlinuz-<name>
```

> We can use any `<name>` we want, but to make it easier to organize, i
> suggest you use the same `<name>` in the following step.

After that, we need to copy the default mkinitcpio preset like this:
```sh
# @guest
sudo cp /etc/mkinitcpio.d/linux.preset /etc/mkinitcpio.d/linux-<name>.preset
```

And then, we need to replace `ALL_kver`, `default_image`, and `fallback_image`
with the `<name>` we assign before. For example, let's say we use `torvalds-vm`
as `<name>`, we can use something like this:
```
...
ALL_kver="/boot/vmlinuz-torvalds-vm"
...
default_image="/boot/initramfs-torvalds-vm.img"
...
fallback_image="/boot/initramfs-torvalds-vm-fallback.img"
```

After that, we need to generate `initramfs` using this command:
```sh
# @guest
sudo mkinitcpio -p linux-<name>
```

Assuming we're using `grub` as our bootloader in a virtual machine, we need to
update the `grub` config like this:
```sh
# @guest
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Now, we need to reboot the virtual machine and check if everything works fine.
If we check on `grub` "Advanced options for Arch Linux", we should we able to
see our custom linux kernel.

Alright, that's it. See you next time!

## References

- [Create an ArchLinux image for kernel
testing](https://andrealmeid.com/post/2020-03-10-bootstrap-arch/)
- [FLUSP Kernel Compilation and
Installation](https://flusp.ime.usp.br/kernel/Kernel-compilation-and-installation/)

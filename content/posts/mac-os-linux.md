---
author: Robertus Diawan Chris
title: "The Difference Between GNU/Linux and Mac OS"
date: 2020-12-29T13:26:08+07:00
tags: [Linux, English]
ShowToc: true
---

> To make things clear that GNU/Linux and Mac OS is different and don't expect both of them do the same thing.

## Skip-able Part

What motivates me to make this post is a tweet from someone named David Bombal that trying to install ubuntu on macbook with M1 chip, here is the [tweet](https://twitter.com/davidbombal/status/1343619205686583297).

That tweet makes me wonder why would someone install ubuntu on mac, isn't mac os and linux similar? well it turn out they're not similar. In this post i'll try to explain what the difference between mac os and linux.

## A Brief Intro to Kernel and Operating System

First thing first, we need to know what kernel and operating system is because we're gonna talk a lot about both of them.

What is kernel? to make things simpler, kernel is a bridge between software and hardware. Kernel allocate the system resources (such as CPU, RAM, and other stuff, basically the hardware) to the program/software that we open.

What is operating system? according to [debian post](https://www.debian.org/releases/buster/amd64/ch01s02.en.html#:~:text=In%20a%20GNU%2FLinux%20system%2C%20Linux%20is%20the%20kernel%20component.&text=Because%20the%20Linux%20kernel%20alone,on%20the%20Unix%20operating%20system.): An operating system consists of various fundamental programs which are needed by your computer so that it can communicate and receive instructions from users; read and write data to hard disks, tapes, and printers; control the use of memory; and run other software. The most important part of an operating system is the kernel.

Then what's the difference? well, kernel is part of the operating system and kernel alone doesn't form a working operating system. You need another component other than kernel to make a working operating system which is beyond the scope of this post.

## A Brief Intro to GNU/Linux

What most people knew as "linux" operating system is usually GNU/Linux. GNU consist the component for operating system except the kernel and linux fill the gap on GNU system. So linux alone doesn't form a working operating system and GNU system with linux kernel make the whole operating system. You can use something other than GNU system if you want, like [alpine linux](https://alpinelinux.org/about/) which use musl libc and busybox instead of GNU. GNU/Linux is a unix-like operating system, what it means is that the operating system behaves in a similar manner to a unix system while not necessarily conforming to or being certified to any unix trademark.

## A Brief Intro to Mac OS

Mac OS is Apple Mac line up (macbook, mac pro, etc) operating system and it's based on darwin operating system which is based on BSD operating system. Here's the schema for better understanding what i mean:

![unix genealogy diagram](unix_timeline.png)

Mac OS also use XNU as a kernel. XNU stands for X is Not Unix (from [apple's github repo](https://github.com/apple/darwin-xnu)). So Mac OS based on Darwin and XNU which is already different from GNU/Linux.

## The Difference

So basically mac os was based on BSD system while GNU/Linux was based on GNU system. Other than that, mac os doesn't use linux as it's kernel and use XNU instead. I'm not sure the difference between XNU and linux at this point, but i can give one example that is KVM (Kernel-based Virtual Machine) which is built into linux kernel. Basically KVM let you interact with kernel directly instead of using emulation for virtual machine.

Other than the difference between kernel feature such as KVM, there's a  different feature or command from BSD system and GNU system. From this [article](https://www.topbug.net/blog/2013/04/14/install-and-use-gnu-command-line-tools-in-mac-os-x/), if you want to use GNU command, you need to install it first while in GNU/Linux you can use it immediately.

Also Mac OS doesn't come with the package manager, as far as i know, you need to install the package manager yourself (i might be wrong tho, i have no mac to test it out), whereas the package manager already installed in GNU/Linux.

To make it simpler, the package manager is for installing and uninstalling a program from terminal.

## The Conclusion

Mac OS and GNU/Linux are two different system and don't expect them to behaves the same. If you want Mac OS app then you should buy a mac, but if you want to have access to a lot of terminal or command line interface stuff then you should install GNU/Linux on PC or buy a machine that already came with it.

Installing GNU/Linux on mac is not really efficient because you could get a lot more computing power with the same price and mac hardware usually optimized with Mac OS so GNU/Linux might not run as smooth as Mac OS on mac (and mac user gonna judge that GNU/Linux not as good as Mac OS bla bla bla, which is stupid).

Hopefully you didn't confuse Mac OS with GNU/Linux again and choose what you need rather than expecting Mac OS and GNU/Linux to behaves the same (and it seems like a lot of people did that).

## References

- [Why GNU/Linux](https://www.gnu.org/gnu/why-gnu-linux.html).
- [Linux kernel vs mac kernel](http://www.linuxandubuntu.com/home/difference-between-linux-kernel-mac-kernel).
- [The difference between linux and bsd](http://www.linuxandubuntu.com/home/difference-between-linux-kernel-mac-kernel).
- [Darwin os (wikipedia)](https://en.wikipedia.org/wiki/Darwin_(operating_system)).
- [Unix-like term (wikipedia)](https://en.wikipedia.org/wiki/Unix-like).
- [Quora about mac os, darwin os, and gnu/linux](https://www.quora.com/Whats-the-difference-between-Mac-OS-X-Darwin-OS-and-a-popular-Linux-distribution-like-Ubuntu-What-can-be-done-on-Darwin).
- [Apple's darwin-xnu github repo](https://github.com/apple/darwin-xnu).
- [Wikimedia unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg).
- [Is mac os UNIX](https://www.howtogeek.com/441599/is-macos-unix-and-what-does-that-mean/#:~:text=macOS%20is%20a%20UNIX%2003,with%20MAC%20OS%20X%2010.5.).

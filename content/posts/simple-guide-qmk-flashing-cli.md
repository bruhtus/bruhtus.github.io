---
author: Robertus Diawan Chris
title: "Simple Guide Flashing Mechanical Keyboard with QMK Cli"
date: 2023-02-26T05:07:03+07:00
tags: [Notes, Linux]
ShowToc: true
---

## A Brief Intro

So recently, around the time i wrote this post, i need to flash my mechanical
keyboard. The harsh thing is that, the GUI QMK toolbox only available on
windows and macOS, meanwhile i use linux.

So, i need to use the QMK CLI to flash my mechanical keyboard. This post is
some kind of notes for my future self.

"Humans live by forgetting", some people said. Without further ado, let's get
started!

## Install QMK Firmware

First thing first, we need to check if QMK firmware already installed on our
system. We can do that with the command below:
```sh
command -v qmk
```

If the output is empty, then we need to install QMK firmware first. If the
output is not empty, we can skip this process.

There's a newbie guide on how to install QMK firmware on linux, we can them
out
[here](https://github.com/qmk/qmk_firmware/blob/master/docs/newbs_getting_started.md#-linuxwsl-).

There are a few options to install QMK firmware on linux. We can install QMK
firmware with our linux distro package manager or from python pip.

In this post, we will use python [pipx](https://github.com/pypa/pipx/) to
install QMK firmware. We can install QMK firmware from python pipx with this
command:
```sh
pipx install qmk
```

> Make sure you already have `pipx` installed on your machine.

Follow the instruction from the installation process, including installing
dependencies on your system and copy the `50-qmk.rules` to `/etc/udev/rules.d/`.

> Copying the `50-qmk.rules` make QMK can detect the bootloader of our
> mechanical keyboard. If we didn't do that, even if our mechanical keyboard
> already in bootloader mode, QMK won't be able to detect our mechanical
> keyboard.

After we install QMK firmware, we need to setup the QMK CLI. We can do that
with this command:
```sh
qmk setup
```

By default, the installation directory will be on our home directory.
Personally i don't really want to put the QMK installation on my home
directory, so i added `-H` flag to move the installation directory to
somewhere else, like this:
```sh
qmk setup -H all-repos/qmk_firmware
```

If the setup already finished, try running:
```sh
qmk doctor
```

and see if there's any issue with the installation.

## Flashing with QMK CLI

There's a newbie guide on how to flash our mechanical keyboard using QMK CLI,
we can find it
[here](https://github.com/qmk/qmk_firmware/blob/d70e9b8659a7fbbd7069fd542bd07e67e04327a1/docs/newbs_flashing.md#flash-your-keyboard-from-the-command-line).

The point is, we need to know if our mechanical keyboard supported by QMK. We
can check the supported mechanical keyboard [here](https://qmk.fm/keyboards/).

Let's say our mechanical keyboard is dz60rgb with ansi layout version 1, we
can flash our mechanical keyboard with this command:
```sh
qmk flash -kb dztech/dz60rgb_ansi/v1 -km default
```

> For more info about the flags, we can use `qmk flash --help`.

Unfortunately, my current mechanical keyboard is not supported, which is
synthesis60 version 2. I need to get the hex file from the mechanical keyboard
designer (and fortunately he's within reach).

> In case you also need the synthesis60 version 2 hex file, you can check it
> [here](simple-guide-qmk-flashing-cli/dyz_synthesis60_atmega_vial.hex).

So, to use our own hex file rather than using the default that QMK supported,
we can use this command:
```sh
qmk flash <path-to-hex-file>
```

Here's an example:
```sh
qmk flash ~/downloads/dyz_synthesis60_atmega_vial.hex
```

All right, that's it from me. See you next time!

## References
- [QMK newbie getting started guide](https://github.com/qmk/qmk_firmware/blob/master/docs/newbs_getting_started.md).
- [QMK newbie flashing guide](https://github.com/qmk/qmk_firmware/blob/master/docs/newbs_flashing.md).
- [Using QMK flash with the external
file](https://github.com/qmk/qmk_firmware/issues/15202#issuecomment-973076050).

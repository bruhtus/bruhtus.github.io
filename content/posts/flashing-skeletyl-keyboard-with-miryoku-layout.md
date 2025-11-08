---
author: Robertus Diawan Chris
title: "Flashing Skeletyl Keyboard With Miryoku Layout"
date: 2025-07-13T07:55:26+07:00
tags: [Keyboard]
ShowToc: true
---

## Setup Miryoku in QMK

**Disclaimer**:<br>
By the time of writing this post, there's a breaking change in QMK that remove
user keymaps, including miryoku. And miryoku QMK haven't porting it's config
in the new QMK userspace. So this section might become obsolete in the future.
For reference: https://github.com/manna-harbour/miryoku/discussions/287

First thing we need to do is to setup QMK. We can check the QMK documentation
below:<br>
https://github.com/qmk/qmk_firmware/blob/master/docs/newbs_getting_started.md

To check if QMK already installed on linux, we can use this command:
```sh
command -v qmk
```
if there's any output, it means QMK is already installed. If there's no
output, try again.

After QMK is installed, we need to setup QMK with this command:
```sh
qmk setup
```

By default, the installation directory will be on our home directory.
If we don't want to put the QMK installation on my home directory, we can add
`-H` flag to move the installation directory to somewhere else, like this:
```sh
qmk setup -H all-repos/qmk_firmware
```

If the setup already finished, try running:
```sh
qmk doctor
```
and see if there's any issue with the installation.

> If you see a warning about 50-qmk.rules, we might to copy 50-qmk.rules to
> /etc/udev/rules.d/. Like this: cp path/to/50-qmk.rules /etc/udev/rules.d

After that, we need to change our working directory `qmk_firmware`. In
`qmk_firmware` directory, we need to add `miryoku_qmk` fork like this:
```sh
git remote add miryoku_qmk https://github.com/manna-harbour/miryoku_qmk.git
git fetch miryoku_qmk
git checkout --track miryoku_qmk/miryoku
```

And then we need to merge `miryoku` with QMK `master` branch like this:
```sh
git checkout -b miryoku-merge-master
git revert --no-edit `git log --grep='^\[miryoku-github\]' --pretty='format:%H' | tr '\n' ' '`
git fetch origin
git merge origin/master
```
and solve all conflict.

After that, we need to copy
`layouts/community/split_3x5_3/manna-harbour_miryoku/` from `miryoku` branch
into `keyboards/bastardkb/skeletyl/keymaps/` in `miryoku-merge-master` branch.
We also need to copy `users/manna-harbour_miryoku/` from `miryoku` branch to
`users/` in `miryoku-merge-master` branch.

After that, we need to run this command:
```sh
make git-submodule
```

Next, we need to make sure that we can compile the `miryoku` layout by running
this command:
```sh
qmk compile -c -kb bastardkb/skeletyl/v2/splinky_3 -km manna-harbour_miryoku -e MIRYOKU_ALPHAS=QWERTY -e MIRYOKU_LAYERS=FLIP -e MIRYOKU_NAV=INVERTEDT
```

> I prefer to use qwerty layout, so i add flag `-e MIRYOKU_ALPHAS=QWERTY`. Also
> my unit using `rp2040`, that's why i'm using `splinky_3`. I'm not sure the
> difference between `splinky_2` and `splinky_3`, and the reason i'm using
> `splinky_3` instead of `splinky_2` is because i think `splinky_3` is the
> latest version of `splinky` variant. I prefer to use space on the right
> side, hence why i put flag `-e MIRYOKU_LAYERS=FLIP` to flip the base layer
> thumb keys and sub layers between right and left hands. I also prefer using
> arrow key in inverted T model, like `asdw` in fps game, hence why i put flag
> `-e MIRYOKU_NAV=INVERTEDT`.

If there's no error, we can go into the next section. If there's some error,
we might need to adjust a few things. Check this discussion and see if this
changes fix the error:
- https://github.com/manna-harbour/miryoku/discussions/361
- https://github.com/manna-harbour/miryoku/discussions/361#discussioncomment-12243200

## Flashing Skeletyl Keyboard

The first thing we need to do is make our keyboard enter bootloader mode. In
skeletyl keyboard, we can use the `update` button at the bottom of the
keyboard. For more info, we can check this guide:<br>
https://docs.bastardkb.com/fw/flashing.html

> Because we using new firmware, we can flash both side with the same
> firmware.

After our keyboard enter bootloader mode, we need to mount our micro
controller to our linux system. We can use `lsblk` to find out our micro
controller partition name and mount those partition. For example, if our micro
controller partition name is `sda1`, we can use this command to mount our
micro controller partition:
```sh
sudo mount /dev/sda1 /mnt
```

After that, we can run this command to flash our micro controller with
firmware that has miryoku layout in it:
```sh
qmk flash -c -kb bastardkb/skeletyl/v2/splinky_3 -km manna-harbour_miryoku -e MIRYOKU_ALPHAS=QWERTY -e MIRYOKU_LAYERS=FLIP -e MIRYOKU_NAV=INVERTEDT
```

If there's no error, we can use our skeletyl keyboard with miryoku layout
after the flashing process finish. If there's an error, you might want to take
a look at miryoku discussion or issue on github.

If the skeletyl keyboard is not usable after booting up the computer, we can
add `#define USB_VBUS_PIN GP19` in file
`keyboards/bastardkb/skeletyl/v2/splinky_3/config.h`. Defining `USB_VBUS_PIN`
will allow us to tell the controller whether it's master or slave right away,
resulting in immediate boot for both sides. After defining `USB_VBUS_PIN`, we
can flashing both side of the keyboard again using the command above.

All right, that's it. See you next time!

## Side Note

Currently the right side of miryoku qwerty layout using `hjkl'` instead of
`hjkl;`, so you might be surprised the first time using miryoku qwerty layout.
There's a plan to customize that, but as of writing this post, it's still work
in progress. You can check this discussion to see the progress:<br>
https://github.com/manna-harbour/miryoku/discussions/85

## References

- [Miryoku reference
manual](https://github.com/manna-harbour/miryoku/tree/master/docs/reference)
- [Miryoku build
guide](https://github.com/manna-harbour/miryoku_qmk/tree/miryoku/users/manna-harbour_miryoku)
- [Miryoku breaking change QMK
discussion](https://github.com/manna-harbour/miryoku/discussions/287)
- [Skeletyl flashing firmware
guide](https://docs.bastardkb.com/fw/flashing.html)
- [Github issue QMK rp2040 not usable from
boot](https://github.com/qmk/qmk_firmware/issues/18511)
- [Skeletyl USB VBUS PIN for splinky
controllers](https://github.com/Bastardkb/bastardkb-qmk/issues/33)

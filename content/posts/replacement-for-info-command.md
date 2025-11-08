---
author: Robertus Diawan Chris
title: "Replacement For Info Command"
date: 2022-06-11T06:38:52+07:00
tags: [Shell]
ShowToc: true
---

## A Brief Intro

Sometime ago, someone posted a tweet about a replacement for
`rm -rf $HOME/directory` that is `mv $HOME/directory /dev/null`. When i took
a look of it, using the `ls -l /dev/null` command, i found something like this:
```sh
crw-rw-rw- 1 root root 1, 3 Jun 11 04:21 /dev/null
```

Now, what is `c` in the `crw` below
```sh
crw-rw-rw- 1 root root 1, 3 Jun 11 04:21 /dev/null
```

After going around on internet, i found an answer in stackexchange[^1]. Which
explain that `c` stands for `character special file`. Please keep in mind that
everything in unix-like system is a file, even a directory (or some people
called it folder) is a file with type `directory`.

> The file we usually use has a type of `regular file`.

In the stackexchange, someone mentioned about `info ls` to show the file type
with their respective character symbol. That's what pique my interest, the
`info` command.

## Replacement For `info` Command

When i use the command `info ls`, i noticed that i can't use `j` and `k` for
navigation, which is a nightmare.

So, the first thing that i need to figure out is, how to change the pager used
in `info` command. According to this stackexchange answer[^2],
"`info` doesn't use separate pager because it handles navigation". So,
basically there's no hope with `info` command? Probably.

And then, the person who answer on the stackexchange also give a suggestion
about `pinfo` which, at least use `j` and `k` as down or up movement. Now i
need to read the manpage about `pinfo` to configure it.

## What's Next?

Other than trying to configure `pinfo`, i might need to figure out what is the
info documents is all about. This might be a new kind of documentation other
than manpage that i can use (if the developer support it). Also, figure out
about the character special file type.

[^1]: [Stackexchange: The meaning of `c` in `crw-rw-rw-`](https://unix.stackexchange.com/a/568573)
[^2]: [Stackexchange: Replacement for `info` command](https://unix.stackexchange.com/a/568573)

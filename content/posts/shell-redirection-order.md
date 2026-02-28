---
author: Robertus Diawan Chris
title: "Shell Redirection Order"
date: 2026-02-28T10:19:35+07:00
tags: [Shell]
ShowToc: true
---

## A Brief Intro

When i take a look at [bash's documentation about shell
redirection](https://www.gnu.org/software/bash/manual/html_node/Redirections.html),
i found out that this two examples are different. This one:
```sh
ls > dirlist 2>&1
```
and this one:
```sh
ls 2>&1 > dirlist
```

So let's try understanding why it is different.

## What 2>&1 means?

From this [hacker news
comment](https://news.ycombinator.com/item?id=47173059), it looks like the
expression `2>&1` is like calling system call `dup2(2, 1)`. If we take a look
at the man page (`man dup2`), here is one of the description:
> In other words, the file descriptor `newfd` is adjusted so that
> it now refers to the same open file description as `oldfd`.

From that man page description, when we use expression `2>&1`, we are telling
the shell to use standard output's file descriptor for standard error's file
descriptor, which means that the file descriptor used for standard output and
standard error is the __same__.

For those who are new to the term `standard output` and `standard error`, you
can take a look at this [standard output
definition](https://www.linfo.org/standard_output.html) and [standard error
definition](https://www.linfo.org/standard_error.html). To make things
simpler, `standard output` is the destination for the _non-error_ output and
`standard error` is the destination for the _error_ output.

In
[POSIX](https://pubs.opengroup.org/onlinepubs/9699919799/functions/stdin.html)
compliant system, the representation for `standard output` is `1` and `standard
error` is `2` and these number also representing the file descriptor number.
That's why when we are using expression `2>&1`, the number `2` in those
expression means `standard error` and the number `1` means `standard output`.

## The Order Matters

Let's try analyzing the possible scenario between the previous example
command.

Before we dive in, if you didn't know about shell redirection, you should read
the bash documentation above or this [blog
post about shell redirection](https://www.linfo.org/redirection.html).

If you are curious about file descriptor, you can take a look at this chapter
from [computer science from the bottom
up](https://bottomupcs.com/ch01s03.html).

### Analyzing First Example

Let's take a look at the _first_ command:
```sh
ls > dirlist 2>&1
```

The expression `> dirlist`, or we can also write `>dirlist`, means that we are
changing the file descriptor for `standard output` from the default file
descriptor `1` to `dirlist`'s file descriptor.

The expression `2>&1`, or we can also write `2>& 1` (Notice that the number `2`
and the symbol is not separated? That is one syntax), means that we are
changing the `standard error` file descriptor from the default file descriptor
`2` to _standard output's_ file descriptor.

Because we already changed the standard output's file descriptor previously
with expression `> dirlist`, that means the standard error's file descriptor
is also `dirlist`'s file descriptor. In this first command, the file
descriptor for `standard output` and `standard error` is __the same__, which is
`dirlist` file descriptor.

### Analyzing Second Example

Let's take a look at the _second_ command:
```sh
ls 2>&1 > dirlist
```

The expression `2>&1` means that we are changing the `standard error` file
descriptor from the default file descriptor `2` to _standard output's_ file
descriptor. Keep in mind that we have not changed the `standard output`'s file
descriptor yet, and that means the `standard output` still have default file
descriptor, which is file descriptor `1`.

The expression `> dirlist` means that we are changing the file descriptor for
`standard output` from the default file descriptor `1` to `dirlist`'s file
descriptor. Keep in mind that redirection _attached_ to the file descriptor, not
to specific standard stream like `standard output` or `standard error`. So
when the standard output's file descriptor changed later, like in the second
command, it does not effect the standard error's file descriptor.

With that in mind, in this second command, the file descriptor for `standard
output` and `standard error` is __not the same__. The file descriptor for
`standard output` is `dirlist`'s file descriptor, and the file descriptor for
`standard error` is `1` (the _former_ `standard output`'s file descriptor).

We can make the `standard error`'s file descriptor the same as `standard
output`'s file descriptor by using the redirection again, like this:
```sh
ls 2>&1 > dirlist 2>&1
```
but keep in mind that using redirection like that on different POSIX shell may
result in _undefined behavior_. I noticed running the command above in `zsh`
and `bash` give different result.

## Conclusion

The shell redirection order is important as it might produce unexpected
result when we use incorrect order.

Alright, that's it. See you next time!

## References

- [Bash's Redirection Documentation](https://www.gnu.org/software/bash/manual/html_node/Redirections.html).
- [Hacker news comment about `2>&1`
in shell](https://news.ycombinator.com/item?id=47173059).

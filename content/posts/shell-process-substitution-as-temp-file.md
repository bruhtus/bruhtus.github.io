---
author: Robertus Diawan Chris
title: "Shell Process Substitution as Temp File"
date: 2022-06-11T09:28:09+07:00
tags: [Notes, Linux, Shell]
ShowToc: true
---

## A Brief Intro

This is all started when i'm trying to make a shell alias using `fzf` and
`git add -p` command. And i was surprised that the interactive selection from
`git add -p` immediately terminated before i can even press any key. Let's get
started, shall we?

## First Attempt

My first attempt to make that shell alias was something like this:
```sh
alias gap='git status -s | awk "{print \$2}" | fzf | xargs -r git add -p'
```

Let me briefly explain one by one the command that i use in my shell alias:
- `git status -s` -> The short format of git status, without unnecessary
info (at least for me).

- `awk "{print \$2}"` -> Only use the second column. The backslash is to
prevent shell to expand `$2` into a second argument instead of a second column
in `awk`.

- `fzf` -> Fuzzy finder by Junegunn[^1].

- `xargs -r` -> Do not run the command if there's no standard input.

- `git add -p` -> To add a chunk of changes, only works if the file already
tracked by git.

This alias is where the problem arise. After i select the file name using
`fzf`, the interactive interface of `git add -p` only appear for a few seconds
and then terminated.

According to stackoverflow answer[^2]:
> Without further arguments xargs does not work with interactive (command
> line) applications.
>
> The reason for that is, by default `xargs` gets its input from `stdin` but
> interactive applications also expect input from `stdin`.
>
> To prevent the applications from grabbing input that is intended for
> `xargs`, xargs redirects stdin from `/dev/null` for the applications it
> runs.
>
> This leads to the application just receiving an EOF[^3].

With that in mind, we need to use `--arg-file=<file>` or `-a <file>` flag,
which means that `xargs` will read from a `<file>` instead of `stdin` (
standard input) so that the `stdin` remains unchanged. Alright, let's go to
the second attempt.

## Second Attempt

Following the stackoverflow answer[^2], my second attempt was something like
this:
```sh
alias gap='xargs -a <(git status -s | awk "{print \$2}" | fzf) git add -p'
```

The expression `<(git status -s | awk "{print \$2}" | fzf)` is what we called
process substitution (or to be precise, shell process substitution).

Instead of using a file, we use a command to act like a temporary file.

Now, the problem is, in my experiment i can't use `fzf` with the shell process
substitution because shell process substitution produces a special file that
can only be opened and read, but not written or seeked[^4].

Commands that treat their arguments as pure streams will works with shell
process substitution, but the commands that seek a file they are given
(or write to a file) won't work[^4].

And that is why we can't use interactive command such as `fzf` with shell
process substitution.

## Conclusion

With all the information from my experiment before, i decided to remove
`fzf` from the alias, which result with this alias:
```sh
alias gap='xargs -a <(git status -s | awk "{print \$2}") git add -p'
```

Is it fulfilling what i need? Not really, i might replace that with shell
function instead of shell alias soon. But, i learn something new about shell
process substitution.

## Extra Note

For more info about the `xargs` and `git` flags, check the manpage.
For example: `man xargs` or `man git-add`.

If we use `echo` with shell process substitution like this:
```sh
echo <(git status -s)
```

That will show us where the temporary file created.

[^1]: [Fuzzy finder by Junegunn](https://github.com/junegunn/fzf).
[^2]: [Stackoverflow: `xargs` explanation](https://stackoverflow.com/a/30050792).
[^3]: [Triggering EOF explanation](https://askubuntu.com/a/724995).
[^4]: [Process substitution result in special file](https://unix.stackexchange.com/a/164109).

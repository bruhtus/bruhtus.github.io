---
author: Robertus Diawan Chris
title: "Manage Dotfiles With Git Working Repository"
date: 2024-04-08T06:44:55+07:00
tags:
ShowToc: false
---

## A Brief Intro

For those who read this article without knowing what dotfiles are, it's
basically a configuration files. The purpose of backing up dotfiles is so that
we don't need to reconfigure our tools on a new system.

There are a lot of ways to backup and manage dotfiles, and one of them is
using git.

In this article we will explore how to backup and manage dotfiles using git,
the full working git repository rather than the bare one which a lot of people
using.

## How?

What i like to do on my free time is browsing random stuff on internet without
a goal, just mindlessly browsing some topic that i'm interested in and see if
i could find something interesting. Letting your mind wondering, some people
said.

While i'm browsing on the internet about zsh, i found this [github issue
discussion about managing
dotifles](https://github.com/romkatv/zsh4humans/issues/21#issuecomment-629806697).
And when i read through those discussions, i wonder if i can use the full
working git repository, which basically the default git repository, rather
than the bare one. So let's try that, i guess.

Back when i first trying to backup my dotfiles, i only know two method:
- Using git bare repository from [atlassian
article](https://www.atlassian.com/git/tutorials/dotfiles).
- Using GNU stow which i forgot where i learn that from.

The method i pick for my first dotfiles manager was git bare repository. The
reason i go with git bare repository instead of GNU stow is that i don't
really want to mindlessly spend my inodes by creating a bunch of symlinks. I
have experience with running out of inodes, which basically means that you
have some storage left but you cannot create a new file. I know that linux
or maybe unix-like system these day has quite a large number of inodes, but
that does not mean i need to be wasteful of inodes, right?

What bugging me for quite a while when using git bare repository to manage my
dotfiles is that, do i really need to use the bare version of git repository
to manage my dotfiles? And just recently i found that the answer is no.

So, how do we use the full git working directory, the default one instead of
the bare one? It turns out that we can separate the git directory (the `.git`
directory) and the working directory without using the bare repository!

Please keep in mind that my experience with git at the time is less than 6
months, maybe around 3 months. So it took me everything i got to just
following the git bare dotfiles manager guide. Now, fast forward 2 years
later, i made my own dotfiles manager using git. You can check them on my
dotfiles repo
[here](https://github.com/bruhtus/dotfiles/blob/f5e3511c70a82e97aaa19331882fc73bf7292922/.config/zsh/sdfm).

The key point of using git working repository instead of bare repository is
this:
```sh
git --git-dir="..." --work-tree="..." init
```
What the command above do is initialize git repository with the specified git
directory and working directory. If we didn't specified the working directory
but specified the git directory, git will create the bare repository instead
of working repository. The key part of this method is
`--git-dir="..." --work-tree="..."`, so you might want to create an alias like
this:
```sh
alias config='git --git-dir="..." --work-tree="..."'
```

Now you might be wondering, why should we use working repository instead of
bare repository? It's because we will most likely change our dotfiles, which
basically means we're still "working" on the dotfiles. So it make more sense
to use working repository instead of bare repository because we're still
changing something to repository. From what i know, git bare repository is
good for sharing and backup files that we don't really change. For example,
let's say you are afraid that github will suspend your account and you don't
have any backup for your private repository, so you basically lost your
private repository. With git bare repository, you can setup a system that only
save the git objects of the repo (the one inside `.git` directory) and update
it regularly by pulling from the remote repository. And because you only save
the git objects instead of the actual files, your storage usage is lower than
storing the actual files. As far as i know, that's the benefit of git bare
repository. With that in mind, it's make more sense to use a working
repository instead of bare repository in this case.

There are 3 important step when we want to get the existing dotfiles on a new
system:
- Initialize new git repo.
- Doing `git reset`, the default or `--mixed` one.
- Doing `git checkout`.

We already talking about the initialize part for a bit before, which basically
just the usual `git init`. Now let's talk about the `git reset` part.
`git reset` or `git reset --mixed` is basically reset the index or staging
area on current branch with specific commit. That means, if we don't have any
commit on current branch, `git reset` or `git reset --mixed` will fill up our
index or staging area with commit we specify. Now after we fill up our index
or staging area with specific commit, we can generate all or specific files
with `git checkout -- <path>`.

Honestly the most interesting part is the `git checkout` part. With `git
checkout`, we can do full dotfiles init or partial init. What i mean by
partial init is that when we don't need all the files on the new system, let's
say we only our vim config, we can just do `git checkout -- ~/.vim` and git
will only generate the `.vim` directory with all of the files in it. The
benefit of that, is we can put all our config in one git repository and still
able to only generate the files we need rather than all the files in the git
repository. To initialize all the files in our dotfiles repository, we can do
`git checkout -- ~`.

So, to sum up the 3 important command for this to work is:
```sh
git --git-dir="..." --work-tree="..." init
git --git-dir="..." --work-tree="..." reset
git --git-dir="..." --work-tree="..." checkout -- <path>
```

Alright, that's it for this article. I will let you explore the implementation
and the possibilities of this method yourself. See you next time!

## References

- [Romkatv's bootstrap dotfiles](https://github.com/romkatv/dotfiles-public/blob/9d4a44c928e3fbd5d61df9e19919ad5115b3b1bd/bin/bootstrap-dotfiles.sh)
- [List of dotfiles manager](https://dotfiles.github.io/utilities/).

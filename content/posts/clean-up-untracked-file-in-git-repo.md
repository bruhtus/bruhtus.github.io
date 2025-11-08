---
author: Robertus Diawan Chris
title: "Clean Up Untracked File in Git Repo"
date: 2022-08-20T12:33:36+07:00
tags: [Shell, Git]
ShowToc: false
---

Have you ever feels like deleting all the untracked file or directory in git
repository is such a pain?

If you do, then this post might be for you!

Ok, first thing first, there is a git command `clean` which help us delete the
untracked file. We can even use it with interactive interface. To invoke the
interactive interface, we can use this command:
```sh
git clean -i .
```

What that command does is prompt us an interactive interface with all the
untracked file in current directory.

To make it start from the root of git repository, we can use this command:
```sh
git clean -i $(git rev-parse --show-toplevel)
```

The `git rev-parse --show-toplevel` will get us the path of current git
repository root. If you don't know what i mean by git repository root, we can
think of it as the directory or path that has `.git` directory.

*But*, the `clean` command has a downside. The `clean` command does not let us
select specific file under untracked directory. It will only let us delete the
entire untracked directory with all the file in it.

To get around this issue, we can make a shell alias like this:
```sh
alias gurm='git ls-files --others --exclude-standard | fzf --multi | xargs -r rm -v'
```

> You can change `gurm` to anything you want.

The dependencies from shell alias above are:
- git
- fzf
- xargs

`git ls-files --others --exclude-standard` will list all the untracked file and
also exclude the file from `.gitignore`.

`fzf` is a fuzzy finder to select the untracked file we want to remove, we
also give flags `--multi` so that we can select multiple file with `tab` key.

Finally, we execute `rm` command with `xargs -r`. What `xargs -r` do is make
sure that we have some input from standard input (stdin), if there is no input
from stdin, `xargs` will not execute the command. It is useful to prevent an
error from a command that require an argument.

Alright, that's all. Have a nice day!

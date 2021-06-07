---
author: Robertus Diawan Chris
title: "Pyv: Minimalist Python Venv Management Tool"
date: 2021-04-16T11:43:18+07:00
tags: [No one asked, Shell, Python]
ShowToc: true
---

> This is a continuation from my previous post (you can check it [here](../python-venv)). Long story short, this is a minimalist way to manage your python virtual environment. All you need is python and git.

## What Is Pyv?

Here's a brief intro to what is pyv:
> Pyv is a simple shell function that let you manage python virtual environment that decoupled from project directory.

To put it simply, pyv move the virtual environment to `$PYV_dir` which by default is `$HOME/.cache/pyv`

## How Pyv Manage Virtual Environment

Pyv can create, remove, activate, deactivate, and list python virtual environment. Below is the explanation for each action that pyv can do.

### Create Virtual Environment

Pyv create python virtual environment using python default command `python -m venv` to `$PYV_DIR/{given-name}`. The `given-name` can be from git repo name or user input.

If the user give pyv command an argument then pyv gonna create python virtual environment with that argument name. For example:
```sh
pce something-big
```
With the command above, pyv gonna create python virtual environment with the name `something-big`. `pce` is pyv command to to create python virtual environment (why would i made something with long command?).

Pyv also can create python virtual environment with git repo name. If you give no argument and just enter `pce` in a working git repo then pyv gonna create python virtual environment using the git repo name.

"How does pyv do that?" you might ask, well, pyv use the command `git rev-parse --show-toplevel` to get the git root directory name (with a lot of trimming of course). For those who don't know what git root directory, to make simple, git root directory is the directory where you first use `git init` command or the directory that have `.git` directory.

Please do remember tho, you can only use `pce` without any argument in git working directory and not in git bare directory. What i mean by git working directory is the normal git repo that has `.git` directory in it.

Here's an example of `pce` without an argument (in case you're still confused):
```sh
pce
```
Yup, only that. Simple right?

### Remove Virtual Environment

After creating virtual environment, how do you delete the virtual environment?

You can remove the virtual environment using `pre` command with or without argument similar to creating virtual environment.

For example, we already create `something-big` python virtual environment. And now we want to remove those virtual environment. All we need to do is something like this:
```sh
pre something-big
```
Those command gonna invoke the `rm` command to remove `something-big` virtual environment directory in `$PYV_DIR`. So the `pre` command depends on how you setup your `rm` command in your shell.

If you create virtual environment using the git repo name, and you want to remove those environment, you can use `pre` without an argument inside those git repo directory (assumming you haven't changed the directory name). How to use the command is the same as when you create the virtual environment, just type
```sh
pre
```
and you're done.

### Activate Virtual Environment

Ok, now you know how to create and remove the virtual environment using pyv. Now, how do you activate those virtual environment?

Because we use default python `python -m venv` command, we need to know how the activate the virtual environment created using those command.

According to [python venv documentation](https://docs.python.org/3/library/venv.html#creating-virtual-environments), we need to `source` the activate file in `<venv-dir>/bin/activate`.
> Did you know that the activate file is also a shell function? Now you know.

So, the pyv command to activate the virtual environment is `pae`. Similar to previous command, you can use `pae` with or without an argument. `pae` command basically to `source` activate file in virtual environment directory that located in `$PYV_DIR`.

Like previous command, let's say we have `something-big` virtual env and we want to activate those command. All we need to do is
```sh
pae something-big
```
or if you create a virtual environment using git repo name (assuming you didn't change the git repo directory name), then you can just use
```sh
pae
```
without any argument.

### Deactivate Virtual Environment

After you know how to create, remove, and activate virtual environment with pyv. Now it's time for you to know how to deactivate virtual environment.

You can deactivate the virtual environment either using default `deactivate` command or using `pde`. `pde` is just an alias for `deactivate` command provided by `python venv`. You do you.

Please keep in mind that you don't need any argument to deactivate virtual environment.

### List Virtual Environment

The last thing is how to list all the virtual environment that available?

To list all the virtual environment that ever created, you can use `pve` command without an argument or `ls -l $PYV_DIR`. `pve` is just an alias for `ls -l $PYV_DIR`.

## Conclusions

There's always an upside/downside to a project, and this project is no exception.

The upside is that, it's minimal. If you didn't do anything fancy with your python virtual environment then pyv probably gonna fit your need.

The downside is also "it's minimal". If you use default options that come with default python venv command, then you can't do that with pyv. Pyv only able to handle `python -m venv <env-directory>`. That's all.

> At the time of writing this article, i've only tested pyv in bash and zsh. I might provide support for fish, csh/tcsh in the future.

## References

- [A few alternative to manage python virtual environment](https://dev.to/bowmanjd/python-tools-for-managing-virtual-environments-3bko).
- [Python venv documentation](https://docs.python.org/3/library/venv.html).

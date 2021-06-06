---
author: Robertus Diawan Chris
title: "Managing Python Virtual Environment"
date: 2021-03-02T05:36:43+07:00
tags: [No one asked]
ShowToc: true
---

> Have you ever want python virtual environment that decoupled from the project directory like conda but not actually conda (dejavu)? That's what this post is about, a simple way to manage a python virtual environment similar to how conda manage virtual environment without python version management.

## A Brief Into to Python Virtual Environment

To makes thing simpler, python virtual environment is a self-contained directory tree that contains a python installation for a particular version of python plus a number of additional packages.

With virtual environment you can minimize the conflicting requirements for each python script you made. For example, application A can have its own virtual environment with python package at version 1.0 installed and application B can have its own virtual environment with python package at version 2.0. If applicatin B need to upgrade the python package to version 3.0 then this will not affect application A's environment with python package at version 1.0.

## Let's Get Started

In this post we're only going to use the default python `venv` to create a virtual environment, the command is something like this:
```sh
python -m venv <directory-name>
```

In case you're wondering, "if we only gonna use the default python command, then what's so special about it?". Let me tell you this, what special about the default python `venv` command is that we can specify the path of the directory and we can enhance that with a shell script. Please keep in mind that this is a minimalist approach to manage python virtual environment without installing other tools except python and git (we'll get to that later).

### Create Python Virtual Environment

Here's how we make a simple shell script to manage our python environment. First thing first, you should decide where you want all your virtual environment located. If you use miniconda, usually it's in `miniconda3/envs/` directory or something along those path, i forgot. I personally want to place my python virtual environment in `.cache/python-venv` directory because i rarely check my `.cache` directory and the virtual environment not gonna disturb other shell scripts i have.

After that, we decide whether we give the name to virtual environment ourself or just use git repo root name. Like i told you before, we can use git repo to decide the name of our virtual environment. To make things simpler, what i mean git repo root is the directory where you use command `git init` to initialize git repo.

For example, if you use `git init` command in `nganu` directory then you can have your virtual environment named `nganu` without you enter any name, but if you didn't want to use your git repo root name then you can also insert the name you want, similar to `conda create -n <name-env>`. With that brief intro, here's the code:
```sh
#!/bin/sh

gitroot="$(git rev-parse --show-toplevel 2> /dev/null)"
trim="${gitroot%"${gitroot##*[!/]}"}"
projectdir="${trim##*/}"
venvdir=$HOME/.cache/python-venv

mkdir -pv $venvdir

if [ "$1" != "" ]; then
	python -m venv $venvdir/$1 2> /dev/null && echo "Created $1 python venv"

elif [ "$projectdir" != "" ]; then
	python -m venv $venvdir/$projectdir 2> /dev/null && echo "Created $projectdir python venv"

else
	echo "Not git repo, please insert a name for virtual env (for example: pce nganu)"

fi
```

We can get git repo root path using the command `git rev-parse --show-toplevel` but the problem is, those command give the full path to git repo but what i want is only the name of the git repo root so we need to trim the full path and only give the directory name we want. That's what `trim` and `projectdir` in those code did. So the name of the git repo dir is in `projectdir` variable.

After that, `venvdir` is for the path you want to save all your virtual environment and the command `mkdir -pv $venvdir` is to make sure if the directory doesn't exist then it's gonna create the directory.

The `"$1" != ""` to make sure if no argument is given then don't create any virtual environment and skip to next statement, but if there's an argument then make the virtual environment with the argument as the name.

The `"$projectdir" != ""` to make sure if it's not a git repo then don't create any virtual environment and skip to next statement, but if it's a git repo then make the virtual environment with the git repo root as the name.

Let's say we save those script with the name `pce` (python create env), you can choose different name, you do you. And don't forget to place those script into your `PATH` so you can use that without typing the full path. With that in mind, here's a few scenario we can do:
1. Without any argument, if it's git repo then it's create virtual env with git repo root name and if it's not git repo then it's not gonna create any virtual env.
```sh
pce
```
2. With an argument, it's gonna create virtual env with the argument as virtual env name.
```sh
pce nganu #create nganu virtual env
```

### Remove Python Virtual Environment

After creating virtual environment, we can also delete virtual environment using a shell script. The concept is similar to create virtual environment, the difference is if there's no virtual environment with the same name as user input or git repo root, then the script not gonna delete anything. With that in mind, here's the code:
```sh
#!/bin/sh

gitroot="$(git rev-parse --show-toplevel 2> /dev/null)"
trim="${gitroot%"${gitroot##*[!/]}"}"
projectdir="${trim##*/}"
venvdir=$HOME/.cache/python-venv

if [ "$1" != "" ]; then
	rm -r $venvdir/$1 2> /dev/null || \
	echo "No python virtual environment match, nothing deleted"

elif [ "$projectdir" != "" ]; then
	rm -r $venvdir/$projectdir 2> /dev/null || \
	echo "No python virtual environment match, nothing deleted"

else
	echo "No python virtual environment match, nothing deleted"

fi
```

### Activate and Deactivate Python Virtual Environment

For activating the virtual environment, i use shell function and then `source` that function with your shell config, like `.bashrc` or `.zshrc` or something else.

I did that because i can't really `source` inside a shell script (it's just gonna create a subprocess and didn't effect the current shell, so you can't activate the virtual environment).

The concept is similar to create and remove script above, if the virtual environment name not found then give message "Python virtual environment not found, create new one", the difference is that we're sourcing the virtual environment to activate it. With that in mind, here's the code:
```sh
function pae(){
	local gitroot="$(git rev-parse --show-toplevel 2> /dev/null)"
	local trim="${gitroot%"${gitroot##*[!/]}"}"
	local projectdir="${trim##*/}"
	local venvdir=$HOME/.cache/python-venv

	if [ "$1" != "" ]; then
		. $venvdir/$1/bin/activate 2> /dev/null || echo "Python virtual environment not found, create new one"

	else
		. $venvdir/$projectdir/bin/activate 2> /dev/null || echo "Python virtual environment not found, create new one"

	fi
}
```

To deactivate the virtual environment, you just need to use `deactivate` command. And to list all the virtual environment you made, you can use `ls` command. For example, `ls ~/.cache/python-venv` (or the path you choose to hold all your virtual environment).

## The Conclusion

What i mean "simple" earlier is not "easier to setup", what i mean is you only need the tools that you probably already have and make a workflow with only that tools to be similar to how conda handles virtual environment.

This post is only an alternative way to manage python virtual environment because most python virtual environment tools usually make the virtual environment inside the project directory and i don't really like that, i prefer how conda manage virtual environment but i think conda is overkill to only manage virtual environment and that's why i make those shell script and function to behave the same as conda (without python version control yet).

I may make this a standalone project if i ever know how to make the installer. And also please keep in mind that this was based on my needs so maybe there's some glitch that i haven't found yet. Ok that's all, hopefully you gain something from this (probably not, like always).

## References

- [A few python virtual environment tools](https://dev.to/bowmanjd/python-tools-for-managing-virtual-environments-3bko).
- [Python virtual environments documentatton](https://docs.python.org/3/tutorial/venv.html).

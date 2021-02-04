---
author: Robertus Diawan Chris
title: "Python Script vs. Shell Script: Command Line Use Case"
date: 2021-01-14T06:00:07+07:00
tags: [No one asked, Linux, English]
ShowToc: true
---

> In this post i'm comparing performance python script and shell script that i've made. The script objective is to check git status on every git repo directory.

## Skip-able Part

> Just a background story why i made this post

Have you wondered what performance between python script and shell script (or some people might say bash script) to run command on terminal? no? same, just kidding. I'm curious about shell script because i can do command line stuff with python script too which for my past self is easier to read.

For around a month, i've learned shell scripting from youtube (mostly [luke smith videos](https://www.youtube.com/channel/UC2eYFnH61tmytImy1mTYvhA)) and i gotta say that for command line use case, shell script is more efficient than python script. Please keep in mind that i'm not an expert when it comes to python scripting or shell scripting, so there might be some performance enhancement that you can make to my script.

## A Brief Intro to Shell Script

You might be wondering "what is shell script?" well, to put it simpler, it's a script that you can run with shell. Shell, like python, is a programming language and that's why you can run `for` loop in a terminal like a psychopath, just kidding i did that sometimes (maybe i'm a pyschopath? 👀).

There are quite a few ways to run shell script, here's some that i know:
- Make script executable by doing `chmod +x <script-name>` and then run the script by doing `./<script-name>` if you're already in the same directory as the script or `chmod +x <path-to-script>/<script-name>` and then run the script by doing `<path-to-script>/<script-name>` if you're not in the same directory as the script. <br>
For example: <br>
If i want to run my `git-status-checker` script, then what i would do is type `chmod +x git-status-checker` if i already in the same directory as `git-status-checker` script and then run the script by typing `./git-status-checker`. <br> But, if i'm not in the same directory as `git-status-checker`, let's say i'm in Download directory meanwhile the script is in Script directory, then what i would do is type `chmod +x ~/script/git-status-checker` and then run the script by typing `~/script/git-status-checker`.

- Using `sh` or `bash` command. <br>
For example: <br>
If i want to run my `git-status-checker` script, then i run the script by typing `sh git-status-checker` or `bash git-status-checker`, both command basically do the same thing.

## The Test

In this post, i'm gonna test the runtime both python script and shell script that i've made. The script objective is to check git status in each git repo directory on my machine.

### The Environment of The Test

All my git repo directory is located in `all_git` directory on home directory, so i only need to focus on that directory which is gonna make the task of the script a little bit easier and faster than check all other directory too.

The amount of not staged for commit is the same when running both script, which is around 13 in total. So the performance may decrease as the amount of unstaged increase.

I'm gonna run the in two way, the first test is including git status for git submodules and the second test is excluding git status for git submodules.

### The Results

#### First Test

In the first test, the runtime of python script is around 0.08 - 0.12 seconds and the runtime of shell script is around 0.07 - 0.08 seconds. Here's the example of runtime of both script (which at this time coincidentally the same):

![Runtime first test](runtime-first-test.png)

#### Second Test

In the second test, the runtime of python script is around 0.05 - 0.07 seconds and the runtime of shell script is around 0.01 - 0.02 seconds. Here's the example of runtime of both script:

![Runtime second test](runtime-second-test.png)

### The Explanation

The effect of checking git status in git submodules is quite a lot but still less than a second which gonna make people think "it's not that much of a difference", well, in case you forgot that this script objective is just to check git status on each git repo directory, it's just a simple task. If you're going to make a more complicated task with a lot of command, that's where you're gonna see the gap.

I'm gonna explain the first test source code first, below is the python script for the first test:
```python
import os

home = '/home/bruhtus/'
dir_list = [dirname for dirname in os.listdir(f'{home}/all_git') if os.path.isdir(f'{home}/all_git/{dirname}') == True]

for dirname in dir_list:
    path = f'{home}/all_git/{dirname}'
    os.system(f'echo {path}')

    if os.path.exists(f'{path}/.gitmodules') == True:
        os.system(f'git -C {path} status -s')
        os.system(f'git -C {path} submodule foreach git status -s')
    else:
        os.system(f'git -C {path} status -s')
```

And below is the shell script for the first test:
```shell
#!/bin/sh

for d in $(ls -d ~/all_git/*/); do
  echo $d && git -C $d status -s && ls -a $d | grep -q .gitmodules && git -C $d submodule foreach git status -s
done
```

Let's take step by step of the processes. The first process is to list all directory in `all_git` directory, because we already specify that all git repo directory gonna be in `all_git` directory so we don't need to check if it's a git repo or not (one less task). So, here's a comparison of the python script and shell script:

The python part below:
```python
home = '/home/bruhtus/'
dir_list = [dirname for dirname in os.listdir(f'{home}/all_git') if os.path.isdir(f'{home}/all_git/{dirname}') == True]

for dirname in dir_list:
```

is equivalent to this part in shell script:
```shell
for d in $(ls -d ~/all_git/*/); do
```

Both of the script take list of directory in `all_git` directory and do a `for` loop to check each folder. You might be able to optimize my python script but i think that the shell script is much simpler than python script. What i mean simpler is less lines of code, not easier to understand. If you need an easier to understand code then python is the way, but it's not really the topic of this post (everyone knows that python script is human readable, right?).

Let's continue, in the python part below:
```python
    path = f'{home}/all_git/{dirname}'
    os.system(f'echo {path}')

    if os.path.exists(f'{path}/.gitmodules') == True:
        os.system(f'git -C {path} status -s')
        os.system(f'git -C {path} submodule foreach git status -s')
    else:
        os.system(f'git -C {path} status -s')
```

is equivalent to this part in shell script:
```shell
  echo $d && git -C $d status -s && ls -a $d | grep -q .gitmodules && git -C $d submodule foreach git status -s
done
```

That second process is to check the git status and git submodules status of all directories. The part where there's `echo` is basically print out anything that we give, in this case it's gonna print directory name so i'm not gonna go into detail for that part.

Now i'm gonna explain a little bit about `&&` command in shell, it's basically let you run the second command (on the right side) if the first command (on the left side) success. So, it's basically an equivalent of `if-else` statement in most programming language. The shell script part below:
```shell
git -C $d status -s && ls -a $d | grep -q .gitmodules && git -C $d submodule foreach git status -s
```

is equivalent to this part of python script:
```python
    if os.path.exists(f'{path}/.gitmodules') == True:
        os.system(f'git -C {path} status -s')
        os.system(f'git -C {path} submodule foreach git status -s')
    else:
        os.system(f'git -C {path} status -s')
```

The `ls -a $d | grep -q .gitmodules` is equivalent to `if os.path.exists(f'{path}/.gitmodules') == True`, both command check if there's a git submodules in the git repo directory or not. So in the second test, i'm removing those `if-else` statement and then run only git status which makes shell script faster than python script.

## The Conclusion

For command line interface stuff, you should probably use shell script instead of python script. I'm not saying that python is bad, it's just not the right tool for command line interface stuff. Yeah python can do almost everything but that doesn't mean python is the best at everything, at least not at command line interface use case which is shell script clearly better here.

## References

- [Luke smith youtube channel](https://www.youtube.com/channel/UC2eYFnH61tmytImy1mTYvhA).
- [Distrotube youtube channel](https://www.youtube.com/channel/UCVls1GmFKf6WlTraIb_IaJg).
- [Git command without change directory](https://stackoverflow.com/a/27952894).
- [Git submodule documentation](https://git-scm.com/docs/git-submodule).

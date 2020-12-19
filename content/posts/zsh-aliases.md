---
author: Robertus Diawan Chris
title: "Zsh Aliases: Be Lazy!"
date: 2020-12-13T09:40:36+07:00
tags: [Linux, English]
ShowToc: true
---

> Have you ever thought that typing a long command is such a pain? well, here's one of the solution for you.

## Skip-able Part

> Just a background story why i write this in the first place.

At some random time back then i thought "can i increase my speed while using the command line interface?" and there's two option i could think of back then, the first one is to increase my typing speed and the second one is to make everything i type shorter.

Honestly i like to take it easy when typing, i'm not really those rush type who are trying to type fast. I don't really care about the speed of my typing as long as i'm comfortable typing at that speed. It's more like durability rather than speed and that's why i take it easy. So, increasing my typing speed is a no (at least for me).

The second option is where piques my interest. From that point i did some research how i can shorten the command i use on internet (google, youtube, and even pornhub but unfortunately no one uploaded on pornhub yet (maybe i should be the one to upload on pornhub? hmm)).

## A Brief Intro To Shell Alias

Basically shell alias is a key shortcut for the command that you want to use. Here's an example: <br>
If you want to make a new directory, let's say the name of the directory is "anu", then if the directory already exist, you want the command doesn't give an error message as a feedback. So here's the command: `mkdir -p anu`. Everytime you want to make a new directory without an error message if the directory already exist, you need to type `mkdir -p <directory-name>` and further more if there's a typo while you type it, uh that's such a pain.

So, how would you make that simpler? well by using shell alias of course, and in this post i'm gonna use Z shell or zsh so the syntax might be different from bash and fish shell. Please keep that in mind.

## Type of Zsh Aliases

Before we start to how to make a shell aliases or in particular zsh aliases, we need to type the type of aliases that zsh has.

There're four type of zsh aliases:
- Simple aliases
- Suffix aliases
- Function aliases
- Global aliases

### Simple Aliases

Like the name suggest, it's just a simple stuff (or you could say the default?). Here's an example of simple alias: <br>
`alias md='mkdir -p'` <br>
From above example, if you want to make a new directory without error message if the directory already exist then you only need to type `md` rather than `mkdir -p`. So, it's basically telling the terminal that `md` equivalent to command `mkdir -p` and execute `mkdir -p` command immediately.

### Suffix Aliases

Suffix alias is for opening a specific extension in a specific program, it defined using `-s` flag. Here's an example of suffix alias: <br>
`alias -s py=vim` <br>
From above example, it's basically telling the terminal to open every extension `.py` in vim. So if you have a python file, let's say `anu.py`, then you just need to type `anu.py` and it's gonna translate to `vim anu.py`, opening `anu.py` file in vim.

Personally i don't really use this alias because i like to type `vim` and even if you're too lazy to type `vim` you could use simple alias instead, like `alias v='vim'`.

### Function Aliases

Function alias is when you want an input in your command. For example, you want to open a python file and then preview the output using `less` (you use try it yourself, i won't explain it). Here's the function alias for that scenario: <br>
```
function anu(){
  python $@ | less} #the @ symbol is basically saying to take everything after the command as input.
```
So everytime you run a python script with `anu` then it's output gonna be displayed in `less`. Let's say you want to open `itu.py`, then you use `anu itu.py` and you're gonna see the output inside of `less` rather than on terminal.

Here's another example: <br>
```
function nganu(){
  python -c $1} #the 1 number is saying to take the first argument as an input, you could add another input by using $2, $3, and so on.
```

If you type `nganu 'import numpy as np'` in your terminal then you're gonna execute `python -c 'import numpy as np'`. So it's basically take the first argument which is `'import numpy as np'` as an input (please keep in mind that using `''` considered as one argument).

### Global Aliases

Global alias is the command that you could place anywhere in the sequences. For example, you want to `grep` a specific file in your directory and you probably gonna type `ls | grep filename` (honestly i don't care what you type, you do you), if you type `ls | grep filename` every time you want to search for something than it's gonna become a hassle.

So, what should you do? well, you could give a global alias to `grep` like this: `alias -g G='| grep'`. After that you could place `G` in the middle of your command, like this: `ls G filename | less` (it's gonna direct the grep result to `less`) or in the back of your command, like this: `ls G filename` (not using `less`). The `G` alias is only for `| grep` command so you still need to type the pattern to search or in this case a filename.

## Tips To make some Aliases

First, before you make aliases, please check if the command already exist or not using `which` command. For example: i want to name my alias `ls`, before i make alias `ls`, i'm need to type `which ls` to show me if `ls` already assigned to other program or command. If after you type `which <alias-you-want>` and there's an error like `<alias-you-want> not found` then you're ready to go.

Second, don't make to much specific simple aliases. For example, you want to make alias to install packages from your package manager, let's say debian package manager (apt), you need to type `sudo apt install <package-name>`, and you want to make it simple by using alias like `alias sai='sudo apt install'`. So everytime you want to install something you just need to type `sai <package-name>`, simple right? well, honestly it's not really a good practice because over time you're gonna forget what the command behind those alias. If you're really forgot what the command then there's `which` command as a livesaver, but do you really want that? Imagine you're using another machine without your aliases, then you forgot a simple command because you're using to much alias, isn't that kind of sad?

## Conclusion

Shell alias is like a double edge sword, use it carefully and don't be dependent on it. If possible, only make aliases if you're using it a lot and already know the basic or maybe make aliases that represent the executed command.

### Bonus Useful Alias

```
function sd(){cd "$(du ~ | awk '{print $2}' | fzf)"}
```
> That's my first attempt using `fzf`, it basically change directory using `fzf` output as an input.

```
function cs(){find ~ -type f | fzf | xargs -o -r vim}
```
> That's basically find every files in home directory using `find` and `fzf` and then open the file in vim directly (the parameter `-o` for not broke my terminal after the command (for some reason) and the `-r` for if there's no input then it's gonna back to terminal instead opening vim. For more detail please check on `man` page).

## References

- [Types of zsh aliases](https://thorsten-hans.com/5-types-of-zsh-aliases).
- [Luke smith youtube channel](https://www.youtube.com/channel/UC2eYFnH61tmytImy1mTYvhA).
- [Distrotube youtube channel](https://www.youtube.com/channel/UCVls1GmFKf6WlTraIb_IaJg).

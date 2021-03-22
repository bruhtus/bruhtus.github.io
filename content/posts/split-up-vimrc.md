---
author: Robertus Diawan Chris
title: "Split Up Vimrc"
date: 2021-03-22T05:37:41+07:00
tags: [Vim, English]
ShowToc: true
---

> If you're the type of person who like to place all your source code in one file, then this article is not for you. But, if you're the type of person who like to split up your source code into a few sub-modules, then this article is for you.

## Skip-able Part

When i look at my vimrc (vimrc is a vim config file for those who don't know), i always feel confused where should i add new configuration for new plugin. I want to organize my vimrc so that it makes me easier to maintain and adding stuff, and that's where the problem comes in.

Overtime, when you're keep adding configuration to vim, slowly your vimrc gonna become a huge mess and at some point it gonna feels cumbersome to access your vimrc. And that's why i thought "can i split up my vimrc so that i don't feel overwhelm every time i want to add new configuration or mapping to vim?" and it turns out i can.

## Upside and Downside

Before we move on, you need to know the upside and downside for split up your vimrc that i've found. Here we go.

### Upside

- Easier to manage, you can have a dedicated config file for every plugin you have
- Doesn't overwhelm you with the piled up line of text, because it's on separate file
- You can take a part of your config such as defaults setting and mappings, rather than a whole vim config (useful for accessing server with ssh)

### Downside

- You need to use grep or something similar if you want to check if the mapping already exist or not. If you didn't split up your vimrc, you can just use vim built-in search function.
- You need to download a lot of file if you want to have full experience of your vim.

## Split Up Vimrc

If you still one despite the downside, then it's time to split up your vimrc.

First thing first, you need to know that vim has runtime path which gonna be loaded everytime you start vim. And we can use the default runtime path to our advantage. For the full list, you can check [here](https://learnvimscriptthehardway.stevelosh.com/chapters/42.html), but to make it simpler we're only gonna use `~/.vim/plugin` (for vanilla vim) or `~/.config/nvim/plugin` (for neovim) directory. Every file in those directory gonna get loaded every time you open vim, so you can add your config in those directory.

For example, you can move your defaults config such as `set number relativenumber` into file `defaults.vim` and place it in `~/.vim/plugin` (for vanilla vim) or `~/.config/nvim/plugin` (for neovim) and defaults config such as `number` and `relativenumber` gonna get loaded everytime you open vim.

You can also make config file for your plugin and give it the plugin name such as `fugitive.vim` and place it into `~/.vim/plugin` (for vanilla vim) or `~/.config/nvim/plugin`.

## The Conclusion

It's quite easy to manage vim if you split up your vimrc, but it comes back to your personal preference. This article is just to remind vim user that they can split up their vimrc if they want to.

## References

- [Manage plugin in dark ages](https://learnvimscriptthehardway.stevelosh.com/chapters/42.html).

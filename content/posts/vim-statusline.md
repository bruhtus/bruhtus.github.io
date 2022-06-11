---
author: Robertus Diawan Chris
title: "Guide to Make Your Own Vim/Neovim Statusline"
date: 2021-08-20T08:31:29+07:00
tags: [Vim]
ShowToc: true
---

> We don't have to install vim plugin to get a statusline if we don't want to.

## Introduction

Hi everyone! In this post I will talk about making your custom statusline in vim. There are a lot of plugins out there that makes vim statusline way better and works out of the box.

"Why would someone going through all the trouble while there's a plugin for that?", you might ask. Well, for me personally, having one less plugin is a good thing. I don't really want to depend on the plugin for something simple such as statusline. If I can build it myself, then I will build it myself rather than using a plugin. Also, it save me a lot of time to figure out which plugin causes the issue if I have less plugin! Oh, another thing, some vim statusline plugin can slow down your startup time (I'm looking at you `vim-airline`) so be careful about that. Alright then, let's get into it!

## Requirements

Before we start, we need to prepare a few things:
- Vim/Neovim (we will use `vim script` or `VimL` in this post)
- `set laststatus=2` (always display the statusline)
- Patience (don't be scared when you see an error, calm down!)

If all is set, then let's get started!

## Disable Old Statusline

Before we move further, you need to disable or remove your old statusline plugin and the config. If you don't want to remove your old statusline plugin config, you can commented out those line/config in your vimrc or init.vim.

## Different statusline for active and inactive window

You can have different statusline for active and inactive window by using `autocmd` event. "What is `autocmd`?" you might ask, go take a look at `:help autocmd` for more info. To make it simple, `autocmd` is to automatically execute a command on certain event which you can check on `:help autocmd-events` for more info.

So, how can we have different statusline for active and inactive window? First, we need to make a function to define our active or inactive statusline component. You can create the function similar to this:
```vim
" component for active window
function! StatuslineActive()
  " the component goes here
endfunction

" component for inactive window
function! StatuslineInactive()
  " the component goes here
endfunction

" load statusline using `autocmd` event with this function
function! StatuslineLoad(mode)
  if a:mode ==# 'active'
    " to make it simple, %! is to evaluate the current changes in the window
    " it can be useful for evaluate current mode in statusline. For more info:
    " :help statusline.
    setlocal statusline=%!StatuslineActive()
  else
    setlocal statusline=%!StatuslineInactive()
  endif
endfunction
```

and also the `autocmd` similar to this:
```vim
" so that autocmd didn't stack up and slow down vim
augroup statusline_startup
  autocmd!
  " for more info :help WinEnter and :help BufWinEnter
  autocmd WinEnter,BufWinEnter * call StatuslineLoad('active')
  autocmd WinLeave * call StatuslineLoad('inactive')
augroup END
```

Now we can compose our statusline component. We can take a look at `:help statusline` for supported items like for example `f` for relative path to the file in the buffer. You can choose whatever item you like in your statusline, and put it in the previous function similar to this:
```vim
function! StatuslineActive()
  " if we want to add `f` items in our statusline
  let l:filename = '%f'
  " if we want to add 'm' items in our statusline
  let l:mod = '%m'
  " the `.` is basically to ignore whitespace before and put it right after the previous component
  return l:filename.l:mod
endfunction
```

Why we need to do that? well, I'll explain it in next section

### Current Mode in Statusline

If you want to put your current mode in your statusline, you can do it with a function similar to this:
```vim
function! StatuslineMode() abort

    let l:currentmode={
        \ 'n':  'N',
        \ 'v':  'V',
        \ 'V':  'VL',
        \ '^V': 'VB',
        \ 's':  'S',
        \ 'S':  'SL',
        \ '^S': 'SB',
        \ 'i':  'I',
        \ 'R':  'R',
        \ 'c':  'C',
        \ 't':  'T'}

    let l:modecurrent = mode()
    " use get() -> fails safely, since ^V doesn't seem to register
    " 3rd arg is used when return of mode() == 0, which is case with ^V
    " thus, ^V fails -> returns 0 -> replaced with 'VB'
    let l:modelist = toupper(get(l:currentmode, l:modecurrent, 'VB'))
    let l:current_status_mode = l:modelist
    return l:current_status_mode
endfunction
```

and put it inside of your statusline function like this:
```vim
function! StatuslineActive()
  let l:filename = '%f'
  let l:mod = '%m'
  " `w:` is basically local variable to current window
  " and `l:` is basically local variable to function. For more info :help E121
  let w:mode = '%{StatuslineMode()}'
  return w:mode.l:filename.l:mod
endfunction
```

Now, if we want to change the current mode background based on the current mode, we can do something like this:
```vim
" define Normal mode color, Insert mode color, and so on
hi NormalModeColor ctermbg=... ctermfg=... guifg=#... guibg=#...
hi InsertModeColor ctermbg=... ctermfg=... guifg=#... guibg=#...

function! StatuslineActive()
  let l:filename = '%f'
  let l:mod = '%m'
  if mode() ==# 'n'
    let w:mode = '%#NormalModeColor#%{StatuslineMode()}'
  elseif mode() ==# v:insertmode
    let w:mode = '%#InsertModeColor#%{StatuslineMode()}'
  endif
  " %* is basically to restore highlight to StatusLine highlight group
  return w:mode.'%* '.l:filename.l:mod
endfunction
```

### Git Branch in Statusline

If you install `vim-fugitive` plugin, then you can use `fugitive#head()` in your statusline like this:
```vim
function! StatuslineActive()
  let l:filename = '%f'
  let l:mod = '%m'
  if mode() ==# 'n'
    let w:mode = '%#NormalModeColor#%{StatuslineMode()}'
  elseif mode() ==# v:insertmode
    let w:mode = '%#InsertModeColor#%{StatuslineMode()}'
  endif
  " make sure it doesn't throw an error if `vim-fugitive` is not installed
  let l:git = "%{exists('*FugitiveHead') ? fugitive#head() : ''}"
  " to separate left and right side
  let l:sep = '%='
  return w:mode.'%* '.l:filename.l:mod.l:sep.l:git
endfunction
```
alternatively, you can use `system()` command to get the current git branch (for more info `:help system()`) like this:
```vim
function! StatuslineActive()
  let l:filename = '%f'
  let l:mod = '%m'
  if mode() ==# 'n'
    let w:mode = '%#NormalModeColor#%{StatuslineMode()}'
  elseif mode() ==# v:insertmode
    let w:mode = '%#InsertModeColor#%{StatuslineMode()}'
  endif
  " for more info :help E121
  let g:gitbranchcmd = "git branch --show-current 2>/dev/null | tr -d '\n'"
  " use system() if vim-fugitive not installed
  let l:git = "%{exists('*FugitiveHead') ? fugitive#head() : system(g:gitbranchcmd)}"
  let l:sep = '%='
  return w:mode.'%* '.l:filename.l:mod.l:sep.l:git
endfunction
```

## Same Statusline for active and inactive window

Now, if we want our statusline to be the same whether in active or inactive window. We can make simplify it by only make one function and not using `autocmd`. It will look something like this:
```vim
function! StatuslineComponent()
  " your component goes here
endfunction

set statusline=%!StatuslineComponent()
```
and you can use some tips from previous section too!

## Conclusion

This guide is for `do-it-yourself` kind of people, so it's only giving some pointer you can use to make your own statusline. I don't want to tell you what to put in your statusline, it is your OWN statusline after all, so you need to know what you want in it. Also, I'm not a vim script expert so please forgive me if I miss something. If you have any question regarding this post, feel free to hit me up on twitter (@diawanchris)! See you later!

## References

- [Kade Killary blog post](https://kadekillary.work/post/statusline-vim/).
- [Junegunn's statusline](https://github.com/junegunn/dotfiles/blob/057ee47465e43aafbd20f4c8155487ef147e29ea/vimrc#L265-L275).

---
author: Robertus Diawan Chris
title: "Configure Vim as Intro to Software Design"
date: 2025-05-31T04:24:41+07:00
tags: [Vim]
ShowToc: false
---

As vim gaining popularity these days, I've heard some complaint about
configuring vim is hard. Not only learning vim motions is hard, making vim
usable for the user is also hard. Well, in case someone haven't realize,
configuring vim is basically a programming activity which can be a good thing
or a bad thing, depending on your preferences.

Because configuring vim is hard for new user, some people created a
pre-configured vim config that they can use. At the beginning, pre-configured
vim config can help new user to focus more on learning or getting used to vim
motions instead of going the rabbit hole of configuring vim. Pre-configured
vim config can lower the overhead for new user, **but** it's not the final
destination. There are some benefits we can get by configuring vim from
scratch, as configuring vim is basically doing some programming. To use the
programming term, you can think of vim as a "runtime" and your configuration
as a script to run in those "runtime".

Some people like the concept of configuration like vim, which some might say
"configuring by programming", and some people don't like it. If you're
interested in using vim, I suggest you to take a look of what vim has to
offer, like "configuring by programming" concept. And if that's not your cup
of tea, then maybe vim is not for you and maybe you can learn only the vim
motions instead. I treat vim, the text editor, and vim motions separately
because I think both of them have their own benefits independently.

So, what kind of benefits we can get when we try to configure vim? One of the
benefits of configuring vim from scratch is that we learn about "execution
order". You might be thinking, "isn't that the basic of programming?". Well,
you might be surprised that some programmer or software developer didn't even
care or, worse, didn't even know about the execution order. One example from my
workplace is that some developer using `forEach()` in javascript to do some
looping, regardless of *asynchronous* or synchronous operation. And then one of
the developers use `forEach()` to looping through a list of asynchronous
operation.  And for some reason, those developer is moved to another project.
And then I got assigned to those project, and let me tell you, those looping is
causing a race condition in the software because `forEach()` didn't handle
asynchronous operation, and I'm the one who need to fix that. Because of
configuring vim, I make a habit of being conscious about the execution order,
which operation get executed first. And so, that's the first thing I checked,
what order the operation get executed? And that's where I found those "illegal
racing" in the source code.

You might be thinking, "isn't that kind of thinking process will come with
experience?". Yes, you're right, and configuring vim help me to have those kind
of thinking process earlier than my peer, because vim "forced" me to be mindful
of the execution order of my config.  It's like what my senior software
developer always said, we need to hone our "common sense" and configuring vim
helping me honing my "common sense".

Another thing is that because of the concept "configuring by programming", we
can also extend the functionality of vim. For example, if you know about `grep`
tools in unix-like environment, you might already know that vim has those
functionality built-in with `:grep` command (for more info on `:help :grep`).
Personally, I don't really like the default functionality of `:grep` command,
so I make my own implementation that have the same same functionality as
`:grep` command, which is to search any line that have the same words as the
input words. Honestly, that kind of experience, creating my own implementation
of some feature is exciting because I get to design the mechanism from how the
user interact with the feature until how to present the result.

Of course what I did is not as complex as most software out there, but I think
we should try to design a small mechanism first before we go into a complex
mechanism. And that's why I think configuring vim is a nice intro to designing
a software. You get to try to make a simple mechanism that you need or want,
and going up to something like lazy loading mechanism, which can be quite
complex depending on your design and expected results.

Please keep in mind that this is not only limited to vim, we can also get the
same benefits with other software that implement "configuring by programming"
concept. I just use vim as an example because for me, it's the easiest
software to start with. When I start configuring vim back in the day, my vim
config is not as complex as it is now. I only have one file for my vim config
back then, nowadays tho, I need a whole directory to hold all my vim config.
You could say that the more I know about vim, the more my vim config grow.
It's like our vim config is growing with us, so you don't need to know
everything from the start, take your time and enjoy the process of finding
things out in vim.

Alright, that's all from me. Happy vimming!

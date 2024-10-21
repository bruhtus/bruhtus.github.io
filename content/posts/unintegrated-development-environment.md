---
author: Robertus Diawan Chris
title: "Unintegrated Development Environment"
date: 2024-10-20T12:20:19+07:00
tags: [Notes]
ShowToc: false
---

After watching video about acme
[in this article](https://research.swtch.com/acme), which introduces the
concept of Integrating Development Environment instead of Integrated
Development Environment which usually abbreviated to IDE, i thought to myself
"why not having a development environment without integration at all?". This
might sound crazy for some people, but this is my development environment at
the time of writing this post.

Just to be clear, this approach might not be suitable for some software
development, such as game development. The point is, this post is just for
reference about someone else's development environment.

In my time as a linux user, not even as a software developer, i realize that
there's a cost of integration and one of them is *maintenance*. Here's a
little bit of a back story when i found that out. Back then when i use
elementary OS, which is ubuntu-based linux distro, i try to upgrade into
the newer version, and guess what? I can't use the "default" image viewer.
Because i can't use the "default" image viewer, i need another image viewer
which lead me to install another image viewer. Now i have 2 image viewer on my
system which i feel like redundant. So, as a new linux user, i try to
uninstall the "default" image viewer, and guess what? It break the entire
desktop environment. Isn't that nice? Like hell it is!

That incident make me realized that when we tightly integrated our software
with each other, when one software break or missing, our system might falling
apart.

Another example is the "breaking changes" culture of modern software
development. In my observation, software development nowadays does not really
care about improving user experience, it's more like making the changes for the
sake of "there's some new changes". Whether those new changes is useful to the
user or not, that doesn't really matter.

The point is, when something break because of an update in our integration
system, it's kind of hard to find out the problem. Especially if we're using
"modern software" which most likely introduce "breaking changes" on every
release.

My base development environment is `tmux`, and my text editor is `vim`. Other
than that? I use `shell`. The majority of my workflow is using `shell`.
I didn't integrate my text editor or `shell` with `tmux`. I could change my
text editor or shell to something else, and it won't effect my `tmux`
configuration. When i changed my `tmux` configuration or `tmux` introduce
breaking changes, it won't effect my text editor or shell.

Because my development environment is not integrated with each other, i have
the flexibility of changing things. Which can be a good thing or a bad thing,
depends on personal preferences.

So, the point is that we need to approach integration with caution. Not
everything need to be integrated with each other. We also need to realize that
integration has a cost, and one of them is maintenance.

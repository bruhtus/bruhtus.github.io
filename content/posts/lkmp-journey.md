---
author: Robertus Diawan Chris
title: "Linux Kernel Mentorship Spring 2026 Journey"
date: 2026-08-03T05:30:10+07:00
tags: [Linux]
ShowToc: true
---

## A Brief Intro

Before starting the [Linux Kernel Mentorship Program
from Linux Foundation](https://wiki.linuxfoundation.org/lkmp), I used to work
as a web developer. With [how chaotic web development has
become](https://www.baldurbjarnason.com/2024/the-deskilling-of-web-dev-is-harming-us-all/),
I decided to take a career break. I have been interested in the low-level
programming, so I use my time during this career break to learn more about
low-level programming.

With that in mind, this blog post's perspective is from someone who is
completely new to kernel development and also new to C programming language.
Like many web developer out there, I didn't use C programming language at
work so I am not really familiar with C programming language. But before the
mentorship begin, I made a _barely working_ [lisp
interpreter](https://github.com/bruhtus/sclisp) to make myself familiar with C
programming language and [linux kernel coding
style](https://www.kernel.org/doc/html/v4.10/process/coding-style.html)
(using `if` without curly bracket is a new experience for me).

The mentorship session that I joined in was [Linux kernel Spring Unpaid
2026](https://mentorship.lfx.linuxfoundation.org/project/53378ec5-48d7-4c49-a01f-8cbd3948db3d).

## Prerequisite Tasks

There are some prerequisite tasks before we got accepted into the mentorship
program. And now you might be wondering, "how did you manage to get accepted
even though you are a newbie?". Well, in case you haven't noticed, kernel is
also a software and if you have developed software before, you can use some of
your previous software development experience in kernel development too. There
are some differences when developing a kernel and web application for sure,
like the testing mechanism. For example, I can't use the testing framework for
web application to test the kernel so I need to figure out how to test the
kernel. But the basic stuff like "what should we consider when testing a
software?" is the same. I am not sure if this is true, but I feel like the
prerequisite tasks for this mentorship testing our intuition as software
developer rather than specific to kernel development, and that's why I can
pass the prerequisite tasks.

I can't disclose the prerequisite tasks, but I suggest you to take a look at
Linux Foundation's
[A Beginner’s Guide to Linux Kernel Development
course](https://training.linuxfoundation.org/training/a-beginners-guide-to-linux-kernel-development-lfd103/).
That course is helpful to complete the prerequisite tasks and during the
mentorship in general, especially the part to send the patch. I mean, even
after you are doing all of those testing and stuff, and then you found out
that your patch can not be applied by the maintainer, that would be sad,
right? So make sure that your patch can be applied to the linux kernel git tree.

Other than the Linux Foundation's course, you might also want to take a look
at the [linux kernel testing
guide](https://docs.kernel.org/dev-tools/testing-overview.html) to get an
overview of how linux kernel testing is done.

## First Month

After we got accepted into the mentorship program, we have a weekly meeting
with the mentors. During those weekly meeting, we can ask the mentors about
the linux kernel development process, like how to handle `Reviewed-by` tag for
example.

The most shocking thing about this mentorship is that, we only have one task
for the entire mentorship program and that is having a minimum of 5 patches
accepted into the linux kernel. There's no weekly assignments or something
like that. As long as you have 5 or more patches accepted during this
mentorship program and finish your final report, you can graduate from this
mentorship program.

Now, here comes the big problem. As someone who is completely new to kernel
development, I have no idea what I should do. I don't even know which
subsystem I want to work on. In fact, by the time of writing this blog post, I
just learned the concept of virtual memory from book called [Operating System:
Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/). Yeah, I am that
new to this operating system stuff, and the lack of task assignments make this
mentorship even more challenging.

So, what did I do? On the first month, instead of looking at the linux kernel
code, I take a look at the linux kernel mailing list, especially
[linux-kernel-mentees mailing
list](https://lore.kernel.org/linux-kernel-mentees/). I want to learn from the
previous mentees' patches, what kind of mistake they made and what kind of
patch that would be accepted. I am also looking around for resources about
contributing to linux kernel as a newcomer. At first, I found a blog post
about [fixing bug found by syzbot](https://hackerbikepacker.com/syzbot), but
at that time I can't even interpret the stack trace from the crash report
(I don't even know about cpu register until recently, don't judge me) and
that's why I decided that syzbot's report is not for me _yet_. So I keep
looking until I found Gustavo's blog post about [fixing coverity
issues](https://embeddedor.com/blog/2024/09/28/one-simple-and-rewarding-way-to-contribute-to-the-linux-kernel-fix-coverity-issues/),
and that's how I made my first contribution.

## Lesson from First Patch

Before submitting any patches, I suggest you to take a look at submitting
patches documentation on `Documentation/process/submitting-patches.rst` in the
linux kernel git tree.

This is the lesson I got from sending my first patch:
- There is a specific pattern for the commit subject (the one that appear when
we do `git log --oneline`). For example:
```
staging: rtl8723bs: remove unused offset in phase 2 _BlockWrite()
```

> Notice the `staging` and `rtl8723bs` from the example above? The `staging`
> there represent the subsystem, and the `rtl8723bs` represent the device
> driver. This pattern varies for each subsystems, so you need to check that
> first before committing the changes.

- When we mention a specific commit, we don't need to use the full length of
those commit hash. We only need 12 characters of the commit hash (this might
change as the commit in linux kernel git tree increase). After those commit
hash, we need to include oneline summary (or commit subject). For example:
```
c1314fe4d28f ("staging: rtl8723bs: remove all RT_TRACE logs in hal/ and os_dep/")
```

- When dealing with private url (like the url that need an account to view the
content), we need to put the information from those private url instead of the
url itself. For example, because coverity scan need an account to access the
information, instead of putting the coverity scan's report url, we can put the
information from those coverity scan's report like "This is reported by
Coverity Scan as Unused Value". The "unused value" is the information from the
coverity scan's report.

- Don't forget about the `Signed-off-by:` tag too, and use your legal name
with it. For example:
```
Signed-off-by: Robertus Diawan Chris <robertusdchris@gmail.com>
```

This is the first patch that I sent and accepted:<br>
https://lore.kernel.org/linux-kernel-mentees/20260420044651.164450-1-robertusdchris@gmail.com/t/#u

From that first patch, I also learned that when someone replied with
`Reviewed-by:` tag _without_ any feedback, we don't need to do anything and just
wait for the maintainer to respond.

## The Next Lessons

As I sent multiple patches (some of them getting a response, some of them lost
in the void), I will only pick the patch where I learned the most from.

Let's take a look at this patch:<br>
https://lore.kernel.org/linux-kernel-mentees/20260513091031.145826-1-robertusdchris@gmail.com/t/#u

In that patch, I give extra information after the triple dash (`---`) that I
don't have the device and not sure how to test this changes. If we didn't put
that information, the maintainer will assumed that we already test the
changes. Where we put the testing information depends on the maintainer's
preferences. Some maintainer prefer to put the testing information in the
commit message but other maintainers prefer to put the testing information
after the triple dash (`---`), so we need to adjust the patch to each
maintainer's preferences.

Anything after the triple dash (`---`) will be ignored when we apply the patch,
so we can treat that as a way to communicate with the maintainer outside the
commit message, like asking for confirmation or something like that.

> You can take a look at another patch of mine that give extra information how
> I come to the solution after the triple dash (`---`):<br>
> https://lore.kernel.org/linux-kernel-mentees/20260602054133.470293-1-robertusdchris@gmail.com/

The ideal scenario would be to test the changes in a real device, but
sometimes not having the device might not be a blocker (unless explicitly said
so, like in `drivers/staging/fbtft/README`). The point is to be open whether
we can test the changes or not, and let the maintainer decide whether they
want to accept our patch or not.

Even when I don't have the device to test the changes or have no idea how to
test that changes in virtual machine _yet_, I always run the KUnit test to
check whether my changes introduces regression or not (assuming the subsystem
have unit test case). For more information how to run KUnit test, you can
check below:<br>
https://docs.kernel.org/dev-tools/kunit/run_wrapper.html

> If you got an error when running the KUnit python script with the error
> message:<br>
> `The source tree is not clean, please run 'make ARCH=um mrproper'`<br>
> Make sure you save your `.config` file with a different
> name first before running `make ARCH=um mrproper`, because that command
> will remove your `.config` file too.

Other than that, I also add the `Fixes:` tag. We can put the `Fixes:` tag when
we fix some problem, and in this case the _undefined behavior_ which means
anything can happen at that point. The content of the `Fixes:` tag is the
commit which introduces the problem. In this case, the _undefined behavior_
problem appear since commit `d6e290837e50`.

From that patch, I got a feedback from Amirreza Zarrabi (the maintainer) and
after I address those feedback, I can add `Reviewed-by:` tag in my patch. In
this case, I changed my patch to follow the feedback and put the
`Reviewed-by:` tag on the next version of the patch (which is version 2).

Here's another important lesson, when sending the next version of our patch,
we need to send that patch as the **new email**, not as a reply to the
previous version. And inside those _new email_, we need to put what changed
from the previous patch. One of the reason doing this is reducing confusion.
As the patch revision grow, if we put the new patch as the reply to the
previous patch, at some point someone is gonna be replying to the wrong
version. Keep in mind that the maintainers getting _hundreds_ or even
_thousands_ of email per day, so if we send the new version of the patch as
the reply, our new version of the patch might be buried in the maintainer's
email list. And some tools that the maintainer used, like
[b4](https://b4.docs.kernel.org/en/latest/index.html), might break if we put
next version of the patch as the reply.

Make sure to at least wait a few days before sending the new version of the
patch. There's no specific time when we should send the new version of the
patch, but my rule of thumb is to wait for at least a day before sending the
new version of the patch. It depends on how active the discussion is, if
there's an active discussion, we might need to wait for a week or more.

Providing the base commit that we use to make the patch can help maintainer
decide whether the patch can be applied or not, and the base commit can also
be useful for the automated CI that run the test for the changes in the patch.
Make sure that the base commit is in an official maintainer or mainline tree
(linus torvalds' git tree). For example, I usually provide the base commit on
the latest version of linux kernel (whether the rc version or release version)
from mainline tree. If you use `git format-patch`, you can provide the base
commit using `--base` flag like this:
```sh
# Provide commit on tag v7.2-rc7 as base commit.
git format-patch --base='v7.2-rc7' ...
```

Here's the next version of the previous patch:<br>
https://lore.kernel.org/linux-kernel-mentees/20260519020528.133623-1-robertusdchris@gmail.com/t/#u

Here's another person's patch with more revision's version as an example:<br>
https://lore.kernel.org/all/20260609-enable-ice-clock-scaling-v11-0-1cebc8b3275b@oss.qualcomm.com/

## Patchwork

Another lesson that I learned is that some maintainers using
[patchwork](https://patchwork.kernel.org/) to organize the patches they want
to handle. So if the subsystem that you worked on have the patchwork setup,
like [linux kernel
build](https://patchwork.kernel.org/project/linux-kbuild/list/), you can check
if your patch is in there or not. If your patch is not in the patchwork with
"Action Required" state and not archived filter, it's likely either the
maintainer is not interested in your patch or your submission trigger the
patchwork's filter setup by the maintainer (and the maintainer missed your
patch).

Checking the patchwork might resolved some of the anxiety from the newcomer,
as most of the newcomer expect instant reply from the maintainer, which might
not be the case. The maintainer usually respond to the high priority patches
first and respond to the low priority one after the high priority patches has
been resolved. So, be patient.

Some subsystem might have different patchwork address, like [linux-media
subsystem](https://patchwork.linuxtv.org/project/linux-media/list/). So if the
subsystem that you worked on didn't exist in the previous patchwork address,
you can try to find the subsystem's patchwork on another address.

Keep in mind that some of the subsystems did not use the patchwork, so you
might not be able to check whether the maintainer is interested in your patch
or not.

## Summary

What I get from this mentorship is mostly about the process of submitting
patches rather than the technical aspect of the kernel development, and that's
what I expected. Submitting patches for the linux kernel can be confusing, so
this mentorship helped me understand those process better.

For anyone interested in all the patches that I sent during this mentorship
duration, you can check my mentorship report below:<br>
https://github.com/bruhtus/lkmp-report/blob/master/report.pdf

Keep in mind that what matters the most when submitting the patch is
_expressing our intention_. Assume that the maintainer can not read our mind,
so add any information that might help the maintainer decide whether to accept
our patch or not.

I feel like starting the linux kernel contribution from the analysis tool can
be easier rather than just dive into the code directly because that can be
overwhelming for the newcomer. But don't forget that these tools can be
_wrong_ too, so we still need to make a decision whether the report is a bug
or not. Remember, a bug in software is just _unexpected behavior_, so it
depends on the expectation or intention from the software developer. That is
one of the hard part of software development, understanding the context of the
code.

For anyone who is interested in contributing to linux kernel, this mentorship
program is a good starting point. With the mentor guidance, contributing to
upstream linux kernel can be less _intimidating_.

## What's Next?

At the time of writing this blog post, I am learning about
[kdump](https://docs.kernel.org/admin-guide/kdump/kdump.html) by using one of
the [syzbot's report that has been
solved](https://syzkaller.appspot.com/bug?extid=a9a4bedfca6aa9d7fa24) as the
test case. What I am trying to do is tracking how do we get to the solution
by exploring the dump file.

I made a simple shell script to create a debian image with `kdump` setup to
reproduce the syzbot's report. We can use those debian image in the virtual
machine like QEMU and reproduce the syzbot's report in it. With `kdump` setup,
everytime the kernel panic in the virtual machine, we can copy the dump file
into our machine and analyze that using [crash
utility](https://crash-utility.github.io/). The script is in here:<br>
https://gist.github.com/bruhtus/00c2e8567f52e1160b9f1bc111a2959c

I am planning to explore the issue from syzbot's report and hopefully able to
make my own reproducer rather than depending on the bot's reproducer. Let's
see how it goes.

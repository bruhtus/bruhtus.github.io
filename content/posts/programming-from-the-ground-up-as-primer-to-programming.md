---
author: Robertus Diawan Chris
title: "Programming from the Ground Up as Primer to Programming"
date: 2026-10-31T08:42:55+07:00
tags: [Reflections]
ShowToc: false
---

Recently I read the book titled [Programming from the Ground
up](https://download-mirror.savannah.gnu.org/releases/pgubook/ProgrammingGroundUp-1-0-booksize.pdf)
and I think this book can be considered as one of the options for anyone wants
to learn programming for the first time. Keep in mind that this is not the
only book you can use to learn about programming, there other books you can
use too. What I found interesting about this book is that this book give a
nice explanation about a basic concept, such as what is buffer and how
computer view memory, which is not always the case in other introductory
programming book. Some people might think this is not necessary, but I think
this kind of concept can give you a different perspective on how you tackle
programming as you go on.

This book give a basic concept of programming in a simple way, it is so simple
that the book using x86 32-bit variant of assembly programming language. There
is no `for` or `while` loop, it is just jumping operation all over the place.
Keep in mind that simple does not necessarily mean easy, and in this case,
what I mean by simple might not be easy for someone else. Those extensive
operation, such as `for` or `while` loop, exist in a higher level programming
language because that make programming easier than just using jump operation
in assembly programming language. But on the other hand, because of how simple
assembly programming language is, you are forced to think in a simple way.

Now, some people might think that x86 32-bit machine is rare compared to x86
64-bit machine these days, so the code and command example in the book is out
of date and might not work on x86 64-bit machine. And yes, that is correct. We
need to adjust either the code or the command to run the program if we are
using x86 64-bit machine. For example, the book using `as` (GNU assembler)
program to assembling the code, but if we are in x86 64-bit machine, the `as`
program will assembling the code in x86 64-bit format and that will throw an
error if we use x86 32-bit format. So we need to adjust how we use `as`
program to assembling the code in x86 32-bit format. I think this an advantage
rather than disadvantage because you have a lot more to explore, especially
the difference between x86 32-bit and x86 64-bit variant. Some people have
this misconception that *reading equal learning*, which is not the case. Just
because you read a lot of technical book, that does not mean you are learning.
That is why there is a term called "tutorial hell", where someone keep reading
or watching a tutorial but can not apply the concept outside of the tutorial
scenario. And this "out of date" code or command might be beneficial because
it forces the reader to be proactive to solve the problem they encounter when
dealing with the difference between x86 32-bit and x86 64-bit machine, rather
than just copy and paste from the book.

Another highlight about this book is that this book give an example about
making a simple dynamic memory allocator. This example can give you a glimpse
of what is going on with the heap memory, which might be hard to grasp for
newbie. Keep in mind that each library, or even another programming language,
might implement different mechanism for the heap memory, so this book's
example is not the only way to do dynamic memory allocation.

If you are unsure where to start your programming journey, you can consider
this book. If you are struggling to follow along the code example in this
book, you can take a look at my approach on this git repo:<br>
https://github.com/bruhtus/programming-from-the-ground-up

Alright, that's it. See you next time!

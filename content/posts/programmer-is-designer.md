---
author: Robertus Diawan Chris
title: "Programmer is Designer"
date: 2025-11-28T10:15:47+07:00
tags: [Reflections]
ShowToc: false
---

Recently i finished reading Peter Naur's essay called
[Programming as Theory Building](https://pages.cs.wisc.edu/~remzi/Naur.pdf)
and i think that essay describe what i like about programming, that is
"building a theory" or i usually called it "designing a system".

I like to think of myself as "designer" when i'm programming, because in a
way, i'm basically designing some sort of mechanism to meet some specific
needs. The process of finding the right mechanism is what i like about
programming. And like any designer out there, if we don't have any
information about the current design we have, it's gonna be a lot harder to
modify the current design.

From Naur's ideas, we get the insight that the designer's job is __not to pass
along only "the design"__, but __to pass along "the design" and "the theories"
behind the design__. That's why when someone told me to look at the code
without any explanation about the context, i got confused, what should i do
with this code? The code only telling me _how the system works_ but not
telling me _what is the goal of the system_. Some "senior programmers" even
mocking me, "can't you just understand from looking at the code?", and i'm
like, the most i can do is create _a hypothetical theory_ about the goal of
the system which might or might not be correct. I think this is what a lot
programmers forgot, without context, designing a solution for specific needs
become a lot harder. Even with the proper context, designing some mechanism
for the system might still be challenging, let alone without any context.

Here's a little bit story about the "loss context", let's say the management
moved me into another on going project, and it turns out that all members of
the team is new in those project. Every programmers touching the code base for
the first time, even the product manager (a.k.a PM) also new in those project,
and to make things more interesting, there's no documentation at all.

And then one day, there's some changes in the ranking system. We are
changing the scoring mechanism and i'm curious about the sorting order, so i
ask the PM, "Do we sort the ranking ascending or descending? Currently we are
using ascending order for the ranking". And then the PM said "Just use the
same order from current implementation". And i'm like, alright, let get
working.

After we finished those changes, the PM check the new implementation and
surprised that the new implementation is not as the PM expected. So me and PM
have a meeting, and then i try to create a simplified ranking mechanism in
spreadsheet, with the data from staging environment and use that data in the
score equation theory that i understand. And then we confirm that
the result from equation on spreadsheet is the same as the result on the new
ranking system, and the PM confirmed that the score is correct. Alright, at
least we know it's not the equation problem. After that, i tried to change the
sorting order. At first, i use the ascending order on the spreadsheet, and i
said "This is the current implementation, is this correct?". After a little
bit of observation by the PM, the PM said "It's not correct". After that, i
use descending order, and i ask "Is this correct?". After some observation
from the PM, the PM said "Yeah, the ranking should be like that". Okay, we
found the problem and we changed the ordering from ascending to descending.

Now, the example above makes me wonder, is the previous ranking system
implementation incorrect to begin with or there's new requirement about the
order of the ranking system that the PM forgot to tell the team? At this
point, we have no idea. If that is a bug, i guess we are lucky enough that
no user notice that and we get to fix that because of new requirements.

That's why i think the theory or context about the current system is
important, we can create a documentation or explain the theory or context in
the comment. The "clean something" evangelist will be mad at me, they be like
"write an easy to understand code so you don't have to write the comment".
And i'm like, if you're gonna write a comment about __how__ the mechanism
works, then i agree with that take, just write easy to understand code so you
don't have to write those type of comment. But i think comment can be useful
to write __why__ we take that approach. So _mindlessly_ following some advice
to not writing comment in a code base can be bad.

And this is another part of the essay that i like. What i understand from the
essay is that, there's _no right method_ in programming, there's only
_a collection of suggestions_ aiming at stimulating the mental activity of
the programmer, by pointing out _different modes of work_ that may be applied
in any sequence.

By the time of writing this post, i've seen a lot of programmers trying so
hard to find "one great method" for everything. For those kind of programmers,
this quote from the internet represent my thought:<br>
> "One size fit all" fit nothing.

Programming is a design process, and the design process might be different
depending on the situation. So as a programmer, we need to decide which
techniques to use in the current situation. That's why i agree with the essay,
all of those "best practices" are good as "a collection of suggestions", which
means that we don't need to follow that _as is_, we can adjust those "best
practices" to adapt for current situation or even create a new "best practice"
for current situation.

If we think of ourselves as programmer, i think it's a good idea to have a
designer mindset, which means that we are not only writing code but we are
designing something with purpose and the purpose can be anything, it can be
just for fun or something meaningful and if possible, pass along those
purpose too so that anyone else can understand our design choices.

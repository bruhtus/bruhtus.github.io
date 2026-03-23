---
author: Robertus Diawan Chris
title: "Initialize Struct to Zero in C"
date: 2026-03-23T08:39:47+07:00
tags: [C]
ShowToc: false
---

__DISCLAIMER__:<br>
Keep in mind that at the time of writing this blog post, i am still learning
about C programming language and lack experience to validate the information
from the low-level perspective. This blog post act like my public notes, which
might be wrong.

When i read [this
article](https://nimfsoft.art/blog/2025/07/23/c-safe-signal-handling-self-pipe/) about
self-pipe trick, i found this code snippet:
```c
...
struct sigaction sa;
memset(&sa, 0, sizeof(sa));
...
```

That make me wonder, do we need to use `memset()` to initialize the `struct`
to 0?

So let's explore the possibilities to initialize `struct` to 0 without
`memset()` and when `memset()` might be a better choice.

With that in mind, let's get started. I found this
[stackoverflow question](https://stackoverflow.com/q/14031727)
and it looks like we can initialize `struct` to zero with `{0}` like this:
```c
struct data_s data = {0};
```

From this [stackoverflow answer](https://stackoverflow.com/a/14031737), it
looks like the difference between using `{0}` and `memset()` to initialize to
zero is that `memset()` __set the alignment or padding to zero__ and `{0}` did not
do that (?). At the moment of writing this blog, i am still not sure how to
check that.

From [this
reference](https://www.delftstack.com/howto/c/struct-alignment-in-c/#faq), the
struct alignment refers to __how the members of a struct are arranged in
memory to meet specific alignment requirements__, which can improve the data
access speed. At the time of writing this post, i am not sure how much
improvement that the struct alignment or padding give over not adding any
alignment or padding.

The interesting part about struct alignment is that, the struct size can vary
depending on how we arrange the member of the struct. Here's an example:
```c
#include <stdio.h>

struct first {
    char ini;
    int anu;
    short itu;
};

struct second {
    int anu;
    char ini;
    short itu;
};

int main(void)
{
    printf("first struct: %zu\n", sizeof(struct first));
    printf("second struct: %zu\n", sizeof(struct second));

    return 0;
}
```

On a system with `int` type takes 4 bytes, `short` type takes 2 bytes, and
`char` type takes 1 byte, the arrangement of struct's member in the example
above is become something like this:
```
first struct:
- 1 byte for char type.
- 3 bytes of padding to align char type.
- 4 bytes for int type.
- 2 bytes for short type.
- 2 bytes of padding to align short type.

second struct:
- 4 bytes for int type.
- 1 byte for char type.
- 2 bytes for short type.
- 1 byte padding to align struct to 4 bytes.
```

The adding of padding in the example above is so that the struct consist of
multiple bytes of the largest size, which is 4 bytes. Again, at the time of
writing this post, i am still not sure how that works but it's something to
keep in mind.

Other than using `{0}` and `memset()`, we can declare the variable as static
variable, that's because declaring static variable without any value will be
filled by zero values during run time:
```c
static struct first data; // Will be filled by zero values during run time.
```

But often times, we don't want the variable to have static storage duration.
In those case, declaring variable as static is out of the question.

Also, keep in mind that this only initializing the struct variable to zero,
not to another value. If we want to initialize the struct variable to another
value, we better off using the usual struct initialization like this:
```c
#include <stdio.h>

struct first {
    char *msg;
    int num;
};

int main(void)
{
    struct first data = {
        .msg = "nice",
        .num = 69
    };

    printf("message: %s\n", data.msg);

    return 0;
}
```

So, if we want to initialize the struct to zero, we can use either `{0}` (if
we didn't care about the padding for struct alignment?) or `memset()` (if we
care about the padding for struct alignment). If we didn't mind that our
variable have static storage duration, we can declare the variable as static
or global without any _initial value_.

Currently still not sure about the benefit of padding in struct alignment, so
let's save that for another time, when i have enough experience to go even
lower.

Alright, that's it. See you next time!

## References

- http://www.catb.org/esr/structure-packing/
- https://www.delftstack.com/howto/c/struct-alignment-in-c/
- https://en.wikipedia.org/wiki/Data_structure_alignment
- https://stackoverflow.com/a/4306269
- https://stackoverflow.com/a/11152199

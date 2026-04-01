---
author: Robertus Diawan Chris
title: "Alternative to strcpy() and strncpy() in C"
date: 2026-04-01T08:35:47+07:00
tags: [C]
ShowToc: true
---

## Brief Intro

I have seen some people said that `strcpy()` can be dangerous to use. At that
time, I am not sure why `strcpy()` can be dangerous, but now I have found a
simple replication of why `strcpy()` can be dangerous.

This is also the case with strncpy(), which does not guarantee a null
terminated string.

At the time of writing this blog post, I am still getting started learning C
programming language. I created this blog post as a way for me to understand
more about copying the string mechanism in C.

## Why strcpy() Dangerous?

Let's assume we have an array of characters in one variable, and we want to
copy those array of characters into another variable. Here's the code for this
scenario:
```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char ini[2];

    char anu[2] = {'a', 'b'};
    char itu[2] = {'c', '\0'};

    strcpy(ini, anu);

    /*
     * What is inside anu[0]?
     */
    printf("anu[0]: %c\n", anu[0]);

    return 0;
}
```

Can you guess what is inside `anu[0]`? In case you're too lazy to try that
out, let me tell you the answer, the character inside `anu[0]` is `c`, not
`a`.

You might be wondering, "Why is the character inside the first element of `anu`
(`anu[0]`) is different from the initial value we give?". That's because
`strcpy()` overwrite the characters in `anu` array. Keep in mind that
`strcpy()` will copy the characters __until it found null terminator
character (\0)__, no matter how many characters it copies. So in the scenario
above, `strcpy()` will continue copying until it found null terminator
character in array `itu`. And because we didn't provide enough space for the
characters until null terminator, `strcpy()` will _silently_ overwrite the
data on the next memory location, which is `anu` array in this case.

We can think of the initial memory layout like this
(`_` a.k.a underscore in here means unknown):
```
_,      _,      a,      b,      c,      \0
ini[0], ini[1], anu[0], anu[1], itu[0], itu[1]
```

And then, after we use `strcpy()`, the memory layout become like this:
```
a,      b,      c,      \0,      c,      \0
ini[0], ini[1], anu[0], anu[1], itu[0], itu[1]
```

This is one of the reason `strcpy()` can be dangerous, because it can
_silently_ corrupting data on the memory and you won't even realize that.
There might be another reason for why `strcpy()` can be dangerous, but at the
time of writing this blog post, I only know about this.

## Why strncpy() Dangerous?

Unlike `strcpy()`, `strncpy()` did not search until it found null terminator
character (\0). So we need to make sure that the source data we copied have
null terminator character, either by searching the null terminator character
with `strchr()` or replace the last character with null terminator character
if it's not null terminator character.

In case you are curious, in C, string must end with null terminator character.
So if the string does not end with null terminator character, we will have
_undefined behavior_, anything can happen and our program will become
unpredictable.

Here's an example code when we didn't null terminate the source and using
`strncpy()`:
```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char ini[2];

    char anu[2] = {'a', 'b'};

    strncpy(
        ini,
        anu,
        (sizeof(ini) / sizeof(*ini))
    );

    /*
     * What is the result?
     */
    printf("ini: %s\n", ini);

    return 0;
}
```

On my machine (x86_64) and compiled with `gcc -Wall`, the example about give
output:
```
ini: abab
```

I have no idea why that happen, and those result might be different on another
machine, that's why it's called _undefined behavior_ because anything can
happen at that point.

## Alternative 1: strlcpy()

Instead of using `strcpy()` or `strncpy()`, we can use `strlcpy()`. The
problem with `strlcpy()` is that, it's not in C standard library, so we might
not be able to use that. Other than that, it act like `strncpy()` but with
_guarantee_ of null terminated string.

You can check the GNU C library implementation of `strlcpy()` [here](
https://sourceware.org/git/?p=glibc.git;a=blob;f=string/strlcpy.c;h=10de5eb1e964c539c79b0de3449b09e4b16c9c43;hb=HEAD).
From the GNU C library implementation of `strlcpy()`, it looks like we can use
another function to copy the string.

## Alternative 2: memcpy() or memmove()

After reading [this article](https://nullprogram.com/blog/2021/07/30/), titled
"strcpy: a niche function you don't need", and reading Daniel Stenberg
(creator of `curl`) blog post
[here](https://daniel.haxx.se/blog/2025/12/29/no-strcpy-either/), about
replacing `strcpy()` with it's custom function, it looks like we can use
another function to copy the string.

If we provide the size correctly, we can directly copy the memory from
`source` to `destination` using `memcpy()` or `memmove()`, and then, at the
end of the string, we can add the null terminated.

Here's an example wrapper for copying string using `memmove()`:
```c
void stringcpy(
    char *dest,
    size_t dest_len,
    const char *src,
    size_t src_len,
    const char *filename,
    unsigned int line_number
)
{
    if (src == NULL || dest == NULL) {
        printf(
            "Error: dest or src can not be null (%s:%u)\n",
            filename,
            line_number
        );

        exit(1);
    }

    if (dest_len != src_len) {
        printf(
            "Error: dest and src length is not the same (%s:%u)\n",
            filename,
            line_number
        );

        exit(1);
    }

    memmove(dest, src, dest_len);

    if (dest[dest_len - 1] != '\0')
        dest[dest_len - 1] = '\0';
}
```

The reason of using `memmove()` instead of `memcpy()` in the example above is
that we can't be sure that the source and destination of the memory location
did not overlap.

Keep in mind that the code above is just an example of how using the
`memmove()` to copy the string and ensure that we have null terminated string.
You can modify that to fit your need or make your own wrapper.

Alright, that's it. See you next time!

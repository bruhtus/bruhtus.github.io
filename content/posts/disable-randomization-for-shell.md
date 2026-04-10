---
author: Robertus Diawan Chris
title: "Disable Randomization for Shell"
date: 2026-04-10T09:09:30+07:00
tags: [Shell]
ShowToc: false
---

At the time of writing this blog post, i recently learn about virtual memory.
So i am curious, how can we trigger the same memory address on different
process?

After reading this [gcc wiki](https://gcc.gnu.org/wiki/Randomization), it
looks like we can use `setarch` to do that.

First thing first, we need to create a simple program to print the memory
address. For this example, i am going to use C programming language, like this:
```c
#include <stdio.h>

int main(void)
{
    int i;

    printf("i: %p\n", &i);
    return 0;
}
```

You can use another programming language. The reason i use C programming
language is because i learned C programming language recently, so i want to
get familiar with C programming language.

After we compile the C file with `gcc` like this (assuming we named the C file
`example.c` and the executable file `example`):
```sh
gcc -o example example.c
```

We can try out running those executable file on the current shell like this:
```sh
./example & ./example
```

The `./example &` means we put the process in the background so that we can
run another process immediately, which is another `./example`.

You might realize that the value printed out is different, that's because of
the "address space randomization". Now, we can try turning that off with
`setarch` like this:
```sh
setarch "$(uname -m)" -R bash -c './example & ./example'
```

`uname -m` is to get the machine architecture name, in my case it's x86_64.
The `-R` flag is to disable randomization of virtual address space for the
program. The `bash -c './example & ./example'` is the program that we are
running, basically we tell `bash` to run the command `./example & ./example`.

If it works as intended, you will see that the value printed out is the same,
no longer different.

The reason i choose `setarch` is because i want to try this out without
effecting the whole system. I am still not sure about what `setarch` do, other
than disable randomization virtual address space, so i can't really explain
much.

Alright, that's it. See you next time!

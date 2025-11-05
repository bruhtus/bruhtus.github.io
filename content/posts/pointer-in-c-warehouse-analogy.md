---
author: Robertus Diawan Chris
title: "Pointer in C: Warehouse Analogy"
date: 2025-11-05T12:46:52+07:00
tags: [Notes, C]
ShowToc: false
---

At the time of writing this post, i am still learning about C programming
language, and one of the topics about C programming language is "pointer".
I know that there's a lot of analogy about pointers out there, one of the
common one is about house and house address, but i want to share the analogy
that help me understand about pointer.

Imagine there's a company called `RAM` and those company business is renting
out a warehouse. And then we, as the customer, want to rent one of their
warehouse.

When we rent the warehouse, we got the following item:
- The warehouse key.
- The keychain with warehouse information on the key.

Now, before we go further, think of the "warehouse" as data storage (a.k.a
memory), the "warehouse key" as the `pointer` in C, and the "keychain" as
the `address` that we save in those pointer. We can think of the "keychain" as
the value of the "key".

If we want to get "something" inside the warehouse, we need to use the "key"
to open the warehouse. We can think of "opening" the warehouse using the "key"
as `dereferencing a pointer`, and the effect of "opening" the warehouse is
that we got "something" from inside the warehouse.

Now, let's say that we have a "key" but we don't have the "keychain", how do
we know which warehouse we are supposed to open? This situation will cause
confusion for us, and it's also similar for the computer. This situation can
be illustrated with this C code:
```c
int main(void)
{
    int *p;
    *p = 69;

    return 0;
}
```

The above code means that we are trying to put `69` into the warehouse, but we
did not give which warehouse we are supposed to use. If we try running those
code (after we compiled it), we will get `segmentation fault` error, which
basically means that the computer can not access the memory because there's no
information about the memory that the computer supposed to use.

To solve those problem, we can give information about which "warehouse" to use
like this:
```c
int main(void)
{
    int i;
    int *p;

    p = &i;
    *p = 69;

    return 0;
}
```

The above code means that we put the address of warehouse `i` in our
"keychain" so that we know which warehouse to use, and we can put `69` in our
warehouse. This is also the case with computer, now the computer is not
confused anymore and won't throw `segmentation fault` error.

There's also a case with `dangling pointer`, which basically means that the
"key" and the "keychain" is exist but the warehouse is already demolished.
So when we are trying to "open" the warehouse, we can't find the specified
warehouse from the information on the "keychain" and here comes another
confusion. I will leave it up to you to try out `dangling pointer` and other
pointer case.

Alright, that's it. See you next time!

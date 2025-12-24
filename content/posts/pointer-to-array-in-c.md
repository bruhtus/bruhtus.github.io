---
author: Robertus Diawan Chris
title: "Pointer to Array in C: Compression File Analogy"
date: 2025-12-24T08:43:20+07:00
tags: [C]
ShowToc: true
---

## DISCLAIMER

Keep in mind that this is only an analogy to help newcomer understand
pointer to array in C programming language. __This does not mean there's any
sort of compression when we use pointer to array__.

## &arr vs &arr[0]

In case you didn't know, we can get the address of an array using the
address-of operator like `&array`. Here's an example:
```c
#include <stdio.h>

int main(void)
{
    int arr[2];
    printf("&arr: %p\n", &arr); // Address to _whole array_.
    printf("&arr[0]: %p\n", &arr[0]); // Address to _element of array_.

    return 0;
}
```

From the example above, you might be thinking, "isn't `&arr` and `&arr[0]`
print out the same value, what is the difference?". The difference is not in
the value itself, but in the _implication_.

`&arr` and `&arr[0]` (or we can just use `arr`, because array name
_represent_ the index 0 memory address) has the same value, which is the
memory address at index 0 because the _beginning_ of the array is also at
the index 0 of the array. The difference is at the _end_.

When we use `&arr`, the _end_ of that expression is the _end on the last
element of array_. Let's say we store integer value in 4 bytes and each memory
address correspond to 1 byte. The whole array memory address can be
represented like this (the empty line used to give clarity, the actual memory
scheme is _contiguous_):
```
001 <- the beginning of index 0 __and__ the beginning of array
002
003
004 <- the end of index 0

005 <- the beginning of index 1
006
007
008 <- the end of index 1 __and__ the end of array
```

With the representation above, the expression `&arr` means that we got the
_beginning of array_ until the _end of array_. Meanwhile, the expression
`&arr[0]` means that we got the _beginning of index 0_ until the _end of index
0_.

## Pointer to Array

With that out of the way, let's get into pointer to array. We can declare
pointer to array like this:
```c
char (*arr)[4];
```

What the above declaration means is that "a pointer to an array of 4 char".
And then, you might be thinking, "how do we give value to those `arr`
variable?". One of them is like this:
```c
char anu[4] = {'a', 'b', 'c', '\0'};
char (*arr)[4] = &anu; // Assigning memory address of anu array.
```

Another one is like this:
```c
char (*arr)[4] = &"abc"; // Assigning memory address of string literal.
```

Or we can split those initialization with declaration and assignment like
this:
```c
char (*arr)[4]; // Declaration of arr variable.
arr = &"abc"; // Assignment of arr variable.
```

Let's talk about the first example. In the first example, we are using memory
address of `anu` array of 4 characters for the value of `arr`, which is
"a pointer to the array of 4 characters".

Now the question is that, how do we get the character `b` from `arr` variable?
You might be thinking, "can't we just use indexing like `arr[1]`?" Remember
that we got the _whole array_ inside `arr` variable and _square bracket_ or
_array subscript_ is another form of dereference. So when we are trying to
deference the `arr` variable like `*arr` or `arr[0]`, we got the _whole array_
instead of _element of array_. And when we do `arr[1]` for `char (*arr)[4];`,
we are actually trying to access the next memory _after the whole array_ which
is _unallocated memory_ and result in _undefined behavior_. We can't be sure
what is in there.

> Keep in mind that `arr[1]` is equivalent to `*(arr + 1)`, which means that
> we are dereferencing _1 offset_ from the index 0 `arr` memory address.
> _1 offset_ in here depends on _how many bytes_ the variable type stored in
> memory.

As for the second and third example, we need to know that string literal have
a lifetime of _static storage duration_, which means that string literal will
exist the _entire execution_ of the program.

With that in mind, the expression `&"abc"` means that we got the memory
address of the _entire_ string literal, which in a way is an array of
characters with _different storage type_ than `char []`. If i understand
correctly, `char []` has _automatic storage duration_, so it will be
_deallocated_ at some point during the execution, usually when the function
returns or the declaration goes out of scope.

The expression `char (*arr)[4] = &"abc";` means that we are passing the
memory address of the _entire_ string literal to `arr` variable.

So, how do we access the character in `char (*arr)[4]`? Well, we need to
dereferencing the `arr` twice. The first dereference, like `*arr` or `arr[0]`,
is to get the _entire array_ and the second dereference, like `(*arr)[1]` or
`*((*arr) + 1)` or `arr[0][1]`, is to get the _element of array_.

> Why do we use bracket around `arr` variable like `(*arr)[1]`? Because square
> bracket or array subscript have a _higher priority_ than the dereference
> operator. If we write the expression like `*arr[1]`, that expression is
> equal to `*(arr[1])`. We are parsing the square bracket or array subscript
> first and then parsing the dereference operator. By adding bracket, we can
> overwrite the default _priority_. This is called _precedence_.
>
> The _precedence_ only control how expression are _parsed_ and which
> operators are _grouped_ which operands. Precedence _do not control_ the
> order of evaluation. To control the order of evaluation, we need to use
> _sequence point_.

## Compression File Analogy

If you are new to C programming language, like i'm at the time of writing this
blog post, you might get confused by the concept of "pointer to array". If you
are confused, this analogy might help you to understand what is going on.

Imagine we have a ZIP file, which is one of the compression file format.
Inside those ZIP file, we have directory called `001`. Inside those directory,
we have a file called `001` and `005` (see the representation of whole array
at the beginning of the post).

Here's the simple representation:
- The _file_ `001` and `005` represent the array's elements.
- The _directory_ `001` represent the array itself.
- The _ZIP file_ represent the pointer to the array.

Here's the simple illustration:
```
pointer.zip
  |_ 001/
        |_ 001
        |_ 005
```

So, if we want to get the element inside the array `001/`, we need to extract
the `pointer.zip` first and then accessing the element inside the array
`001/`.

## Bonus: accessing character of string literal

Here's another interesting thing about string literal that you might want to
try:
```c
#include <stdio.h>

int main(void)
{
    printf("%c\n", "abc"[0]);

    return 0;
}
```

What is the output? Now, try to analyze what's going on :)

Alright, that's it. See you next time!

## References

- [Stackoverflow answer about precedence and
associativity](https://stackoverflow.com/a/67691327).
- [Example memory address of string
literal](https://stackoverflow.com/questions/9970295/life-time-of-a-string-literal-in-c#comment12737771_9970305).
- [log2base2 explanation about pointer to
array](https://www.log2base2.com/C/pointer/pointer-to-an-array.html).
- [Stackoverflow answer about dereferencing pointer to array](https://stackoverflow.com/a/1083507).
- [C-faq about 2-dimensional array](https://c-faq.com/aryptr/pass2dary.html).

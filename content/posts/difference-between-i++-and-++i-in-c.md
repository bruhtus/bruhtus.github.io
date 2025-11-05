---
author: Robertus Diawan Chris
title: "Difference Between i++ and ++i in C"
date: 2025-11-04T15:05:31+07:00
tags: [Notes, C]
ShowToc: true
---

## A Brief Explanation

Let's say we have `int i = 0;`, and imagine there's a **temporary object**
(rvalue?) to store the result of i + 1 for the ++ operator.

> Object is an area of memory that is used by our program, and temporary object
> in here means that the object has a *temporary duration* and will be deleted
> when the containing full expression ends.
>
> Full expression here means:
> - The complete expression that forms an expression statement (with terminating
> semicolon (;) at the end).
> - One of the controlling expression `if`, `switch`, `while`, `for`, or
> `do-while` statement.
> - The expression of an initializer (like `int i = 0;`).
> - `return` statement.

This is what probably happen if we use i++ without assigned the result
to another variable:
```c
// i++;
tmp = i;
i = i + 1;
```

This is what probably happen if we use ++i without assigned the result
to another variable:
```c
// ++i;
i = i + 1;
tmp = i;
```

This is what probably happen if we use i++ when assigned the result
to variable j:
```c
// int j = i++;
tmp = i;
j = tmp;
i = i + 1;
```

This is what probably happen if we use ++i when assigned the result
to variable j:
```c
// int j = ++i;
i = i + 1;
tmp = i;
j = tmp;
```

The only difference between `i++` and `++i` is when we use the value of the
operation in the same statement, like `int j = ++i`.

P.S:<br>
At the time of writing this post, i am still learning about C,
so this illustration might be wrong. Please let
me know if there are a better illustration about this.

## i++ or ++i in For Loop

When using either i++ or ++i in for-loop like this:
```c
int i;

for (i = 0; i < 5; i++)
    printf("%d\n", i);

for (i = 0; i < 5; i++)
    printf("%d\n", i);
```

Both of them will operate *identically* because the increment of i and the print
statement is in **different line**. It's like we are using `i++` or `++i`
without any assignment like this:
```c
int i = 0;
i++;
++i;
printf("%d\n", i);
```

## Side Note

Please keep in mind that operation `i++` and `++i` is also prone to **integer
overflow**.

Here an overflow example:
```c
#include <stdio.h>
#include <limits.h>

int main(void)
{
    unsigned int i = UINT_MAX;
    printf("before increment i: %d\n", i);

    i++;
    printf("after increment i: %d\n", i);

    return 0;
}
```

In the example above, as we store the maximum value of unsigned integer in `i`
and when we increase the value of `i`, the result would be 0 because the value
is more than the maximum value of unsigned integer, so it result in *overflow*.

## References

- [Stackoverflow about difference between i++ and
++i](https://stackoverflow.com/a/24858).
- [About temporary
object](https://herbsutter.com/2013/05/13/gotw-2-solution-temporary-objects/).
- [About integer
overflow](https://splone.com/blog/2015/3/11/integer-overflow-prevention-in-c/).
- [Unsigned integers
overflow](https://www.gnu.org/software/c-intro-and-ref/manual/html_node/Unsigned-Overflow.html).
- [Cppreference object
lifetime](https://en.cppreference.com/w/c/language/lifetime.html).
- [Cppreference
statements](https://en.cppreference.com/w/c/language/statements.html).

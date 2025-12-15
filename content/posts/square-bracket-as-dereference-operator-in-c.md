---
author: Robertus Diawan Chris
title: "Square Bracket as Dereference Operator in C"
date: 2025-12-15T13:57:31+07:00
tags: [C]
ShowToc: false
---

When we learn another programming language for the first time, we were told
that to access an array, we can use indexing. For example, let's say we have
an array with 5 items and the index array start at 0. With that in mind, we
can access the 5th item in those array like `array[4]` (remember, the index
start from 0, so it's 0, 1, 2, 3, 4).

But in C programming language, array can "decay" into pointer, which means
that in some cases, array can be treated like a pointer. Let's say we have
this array initialization:
```c
int array[2] = {69, 42};
```

And then, when we print the memory address of that array, we found out that
the memory address of the variable `array` is equivalent to the memory address
of the index 0 of `array`. We can check that by printing the memory address
like this:
```c
#include <stdio.h>

int main(void)
{
    int array[2] = {69, 42};

    printf("%p\n", array); // The memory address of array variable.
    printf("%p\n", &array[0]); // The memory address of index 0 or first item.

    return 0;
}
```

Now, you might be thinking "what is the correlation between that and
dereference operator?". Well, the thing is, square bracket is not only for
array but can also be used with pointer in general.

Remember that array stored as a "contiguous" block in the memory, which means
that if we have the array with 2 items and the memory address of index 0
is 001, the memory address of index 1 is _after_ the index 0.

For example, if our machine use 4 bytes to store integer value and we
have 2 integer values in the array, the array take up 8 bytes in memory. With
that in mind, if we have the memory address of the index 0 of that array as
001, then the memory address of index 1 is 005. Here's what's going on
(assuming 1 memory address equal to 1 byte):
- Memory address 001 until 004 is for _index 0_.
- Memory address 005 until 010 is for _index 1_.

When we are dealing with _array_, we are also dealing with _memory address_ as
the value, not the actual value itself. And what is a pointer again? A
variable that have _memory address_ as a value. I think this is where the
misunderstanding come around. As far as i understand, array can store
_more than 1_ memory address but pointer can _only_ store 1 memory address at
a time. That's why even though we are dealing with the same _memory address
as a value_, the treatment for array and pointer is not the same. The
similarity between array and pointer is _dereference operation_ of memory
address as a value.

When we want to get the value stored in the memory address that the pointer
had, we need to _dereference_ those pointer. Here's an example:
```c
int main(void)
{
    int life = 42;
    int *p_life = &life;

    int age = *p_life; // Dereference pointer p_life to get value 42.

    return 0;
}
```

In the previous example, we use asterisk (`*`) as a dereference operator, but
we can also use square bracket like accessing array to dereference the pointer
like this:
```c
int main(void)
{
    int life = 42;
    int *p_life = &life;

    int age = p_life[0]; // Dereference pointer p_life to get value 42.

    return 0;
}
```

When we are using the square bracket, we can think of the number inside those
square bracket as _offset_. From the [cplusplus.com
tutorial](https://cplusplus.com/doc/tutorial/pointers/), it seems like the
square bracket is called __offset operator__. This means that when we do
`p_life[0]`, we are _dereferencing a pointer with offset 0_ and offset 0
means the current memory address. So if we have `p_life[1]`, it means we are
__dereferencing a pointer p_life with offset 1__ and the memory address that
we are trying to dereferencing is 1 block of memory address _after_ the stored
memory address in pointer `p_life`, __which we have or not have access to__.

To make things easier to understand, let's say `p_life` pointer stored memory
address 001 that has integer value in it, and our machine use 4 bytes to store
integer value in memory (similar to the previous array example). With that in
mind, when we do `p_life[0]`, we are dereferencing memory address 001. And
when we do `p_life[1]`, we are dereferencing memory address 005.

If you are still confused why we have 005 for the `p_life[1]`, it's because
we take up 4 bytes of memory address with 001, 002, 003, and 004 (assuming 1
byte equal to 1 memory address) for `p_life` or `p_life[0]`
(`p_life` with offset 0), so the __start__ of the next block of memory is 005.
That's why `p_life[1]` memory address is 005.

Here's a representation in code:
```c
int main(void)
{
    int life = 42; // life has 001 memory address.
    int *p_life = &life; // Saving 001 memory address into p_life pointer.

    int age = p_life[0]; // Getting the value from 001 memory address.

    return 0;
}
```

With that in mind, the expression `&array[0]` can also be written as
`&(*array)` which means get the memory address of "dereferencing `array`
with offset 0" (?), yeah, i'm not sure what that's called.

We can also using offset with asterisk operator too. Let's say we want to
dereference `array` with offset 1, we can do `array[1]` or `*(array + 1)` to
dereference `array` with offset 1.

Maybe indexing of array in another programming language is also dereference
operator under the hood? I am not sure. Anyway, this is something interesting
for me to learn and that's why i write a blog post about it. That's it, see
you next time!

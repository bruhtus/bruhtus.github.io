---
author: Robertus Diawan Chris
title: "Lvalue in C"
date: 2025-12-23T06:00:46+07:00
tags: [C]
ShowToc: true
---

## A Brief Into

I am always confused when trying to understand what is considered an lvalue
in C programming language, so let me try to explain that to make me have a
better understanding of lvalue in C.

## What is Lvalue?

From [C committee draft
definition](https://port70.net/~nsz/c/c11/n1570.html#6.3.2.1p1):
> An lvalue is an expression (with an object type other than
> void) that potentially designates an object; if an lvalue
> does not designate an object when it is evaluated, the
> behavior is undefined.

And from [C committee draft the origin of lvalue term](https://port70.net/~nsz/c/c11/n1570.html#note64):
> The name "lvalue" comes originally from the assignment
> expression E1 = E2, in which the left operand E1 is required
> to be a (modifiable) lvalue. It is perhaps better considered
> as representing an object "locator value".

Here's another explanation about `lvalue` from GNU C intro:
> An expression that identifies a memory space that holds a
> value is called an lvalue, because it is a location that can
> hold a value.

With that in mind, `lvalue` is __an expression that refers to the region of
storage that can hold a value__. Those storage can be memory or something
else.

> The region of storage that can hold a value is called __object__.

I think calling `lvalue` as `locator value` make it easier to
understand than the usual terminology `left value`, because `lvalue` might
not be on the left side of the statement.

## Pointer Dereference is Lvalue

Here's an example of `lvalue` can be on the left side or right side:
```c
int num = 68;
int *ptr = &num;

/*
 * *ptr is an lvalue that's on the left and right of statement.
 * The *ptr on the right undergo conversion from _lvalue to rvalue_.
 */
*ptr = *ptr + 1;
```

The expression `*ptr` from example above is an `lvalue` because it _refers to_
the memory space that can hold a value. Keep in mind that `ptr` is a variable
that _stored memory address_ as its value, that means `ptr` stored the address
of another _region of storage_. And dereferencing `ptr` or `*ptr` means that
we are using those _region of storage_ stored in `ptr`.

Let's say we have memory space in address `001` and `002`, the `num` variable
in previous example using the memory space at address `001` and the `ptr`
variable using the memory space at address `002` which stored `001` inside it.
Here's a simple representation:
- `num` _used memory space at_ `001` _and stored value_ `68` _inside of memory_.
- `ptr` _used memory space at_ `002` _and stored value_ `001` _inside of memory_.

## Variable is Lvalue

When we define a variable like this:
```c
int num = 69;
```

`num` is an `lvalue` because `num` _refers to_ the memory space that hold
value `69`.

## Non-modifiable Lvalue

Apparently `lvalue` is not always modifiable, there's some _region of storage_
we can not modify.

From [C committee draft about
lvalue](https://port70.net/~nsz/c/c11/n1570.html#6.3.2.1p1):
> A modifiable lvalue is an lvalue that does not have array type, does not
> have an incomplete type, does not have a const-qualified type, and if it is a
> structure or union, does not have any member (including, recursively, any
> member or element of all contained aggregates or unions) with a
> const-qualified type.

Basically we can not modify `lvalue` if it has:
- Array type.
- Incomplete type.
- Constant type.
- A structure or unions with one of its members as constant type.

Other than that, i assume we can modify the `lvalue` (?). I'm still not sure
yet, at least by the time of writing this post. But the point is that,
we can change the value in `lvalue` depending on `lvalue` modifiable or not.
If the `lvalue` modifiable, we can change the value, if not, then we can't
change the value.

Anyway, let's check the constraint one by one.

### Array Type

When we declare array like this:
```c
int array[2];
```

From what i understand, `array` is an `lvalue` because it _refers to_ the
memory space that can hold a value but, _by the C standard_, we can't modify
those `lvalue` with array type like this:
```c
int array[2];
array = 69; // This is invalid!
```

I still have no idea why that's the case, but if you are curious, you can read
[the development of the C language](https://www.nokia.com/bell-labs/about/dennis-m-ritchie/chist.html).

### Incomplete Type

Incomplete type in here means that the type lacks size information, for
example:
```c
struct anu;
```

`anu` is incomplete type because there _is not enough information_ for the
compiler to determine _how much storage_ to set aside for `struct anu` type.

Here's another example of incomplete type:
```c
int itu[];
union ini;
```

We cannot use the incomplete type like this:
```c
struct anu nganu;
```
_unless_ we complete the type.

But, we can create a pointer to incomplete type like this:
```c
struct anu *nganu;
```
_without_ completing the definition of `struct anu`. That is because a pointer
just storing the memory address and we only need to know the _pointer size_,
we don't need to know the type size.

### Constant Type

Well, as far as i understand, constant in C is read-only, which means that we
can't really change that.

### Constant Type in Struct or Union

I'm still not sure about this one, but i kind of found an example. Here's an
example that will throw an error because of constant type in struct
(using `gcc`):
```c
struct something {
    const int size;
};

int main(void)
{
    struct something very_big;
    very_big.size = 69;

    return 0;
}
```

For some reason, the example above, which separate the declaration and
assignment, trigger an error `assignment of read-only member 'size'`, but if
we use the initialization like this:
```c
struct something {
    const int size;
};

int main(void)
{
    struct something very_big = {.size = 69};

    return 0;
}
```

The example above didn't give any error. So maybe the non-modifiable is during
the assignment (?). Well, i'm still not sure.

## Bonus: brief intro to rvalue

This post is mainly exploring about lvalue because lvalue make me confused the
most. Especially the dereference pointer statement, when i see some reference
write about "\*ptr is lvalue", i'm like "but why can i put it on the right
side like `*ptr = *ptr + 1;`?". That is the primary reason i'm creating this
post.

But other people usually also talking about `rvalue`. Here's a footnotes from
[C committee draft](https://port70.net/~nsz/c/c11/n1570.html#note64):
> What is sometimes called "rvalue" is in this International Standard
> described as the "value of an expression"

And that's what i think about `rvalue` at the time of writing this post,
_a value of an expression_. At the moment, i don't really want to dive deep
into what is considered an `rvalue`. One step at a time.

That's it from me, see you next time!

## References

- [The Development of the C
Language](https://www.nokia.com/bell-labs/about/dennis-m-ritchie/chist.html)
- [Stackoverflow answer about lvalue in
C](https://stackoverflow.com/a/57664139).
- [GNU C intro definition about lvalue in
C](https://www.gnu.org/software/c-intro-and-ref/manual/html_node/Lvalues.html).
- [C committee draft about lvalue
term origin](https://port70.net/~nsz/c/c11/n1570.html#note64).
- [C committee draft about
object](https://port70.net/~nsz/c/c11/n1570.html#3.15p1).
- [C committee draft lvalue
definition](https://port70.net/~nsz/c/c11/n1570.html#6.3.2.1p1).
- [Stackoverflow answer why array type is a non-modifiable
lvalue](https://stackoverflow.com/a/45683469).
- [Stackoverflow answer array type object is not
modifiable](https://stackoverflow.com/a/17691191).
- [Eli's post about lvalue and rvalue in C and
C++](https://eli.thegreenplace.net/2011/12/15/understanding-lvalues-and-rvalues-in-c-and-c/).
- [cppreference about value
categories](https://en.cppreference.com/w/c/language/value_category.html).

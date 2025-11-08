---
author: Robertus Diawan Chris
title: "Remove Specific Line in Vim"
date: 2022-09-03T09:03:52+07:00
tags: [Vim]
ShowToc: true
---

## A Brief Intro

In this post, we will talk about how to remove specific line in vim. We will
be using this javascript snippet code as an example:
```javascript
const app = require('./jest-example');
const math = require('./math');

describe('app operation', () => {
  const multiplyMock = jest.spyOn(math, 'multiply');
  multiplyMock.mockReturnValue('itu');

  test('call math.add()', () => {
    const add = jest.spyOn(math, 'add');

    add.mockImplementation(() => 'anu');
    console.log(app.doAdd(1, 2));
    expect(app.doAdd(1, 2)).toBe('anu');

    add.mockRestore();
    console.log(app.doAdd(1, 2));
    expect(app.doAdd(1, 2)).toBe(3);
  });

  test('call math.subtract()', () => {
    const subtract = jest.spyOn(math, 'subtract');

    subtract.mockImplementation(() => 'nganu');
    console.log(app.doSubtract(1, 2));
    expect(app.doSubtract(1, 2)).toBe('nganu');
  });

  test('call math.subtract() again', () => {
    console.log(app.doSubtract(1, 2));
  });

  test('call math.multiply()', () => {
    console.log(app.doMultiply(1, 2));
  });
});
```

And the goal is to remove line that has `console.log()` in it. Let's get
started!

## Global Command

The first solution is that, we can use global command to delete the line that
has `console.log()` like this:
```vim
:g/console.log/d
```

Please keep in mind that the term *delete* in vim, means *cut*. So, rather
than deleting the content, vim will put that into the unnamed register. For
more info about unnamed register, we can check on `:help registers`.

So, if we didn't want to clutter our unnamed register, we can use black hole
register (yup, that's a thing) which actually *delete* rather than *cut* like
this:
```vim
:g/console.log/d_
```

On a side note, we don't need to use `/` as the separator between command and
pattern, we can use most characters except `\`, `"`, or `|`. If we use vim9
script, we can use `#` as a separator either, so please keep that in mind. For
more info, we can check on `:help pattern-delimiter`.

In other words, we can write the previous global command, like this:
```vim
:g;console.log;d_
```

The global command that we just gone through will delete all the `console.log`
in current file. If we want to specify the area of `console.log` we want to
delete, we can add range to global command.

Let's take a look at the snippet we are using:
```javascript
const app = require('./jest-example');
const math = require('./math');

describe('app operation', () => {
  const multiplyMock = jest.spyOn(math, 'multiply');
  multiplyMock.mockReturnValue('itu');

  test('call math.add()', () => {
    const add = jest.spyOn(math, 'add');

    add.mockImplementation(() => 'anu');
    console.log(app.doAdd(1, 2));
    expect(app.doAdd(1, 2)).toBe('anu');

    add.mockRestore();
    console.log(app.doAdd(1, 2));
    expect(app.doAdd(1, 2)).toBe(3);
  });

  test('call math.subtract()', () => {
    const subtract = jest.spyOn(math, 'subtract');

    subtract.mockImplementation(() => 'nganu');
    console.log(app.doSubtract(1, 2));
    expect(app.doSubtract(1, 2)).toBe('nganu');
  });

  test('call math.subtract() again', () => {
    console.log(app.doSubtract(1, 2));
  });

  test('call math.multiply()', () => {
    console.log(app.doMultiply(1, 2));
  });
});
```

And let's say that our cursor in the line
`test('call math.subtract()', () => {` and we want to delete the `console.log`
in `test('call math.add()', () => {` which is before the line we're currently
in.

With that in mind, we can do something like this:
```vim
:?test?;/});/g;console.log;d_
```

Here's the breakdown of those command:
- `?test?` will search word `test` on previous line.
- `;` after `?test?` is a special offset which tell vim that we will use
another search command. For more info, we can check `:help //;`.
- `/});/` will search after the result of previous search.
- `g;console.log;d_` is a global command to delete `console.log` in the scope
of previous search.

## Substitute Command

The downside of global command is that there's no confirm option, so we might
delete something we don't want to. If we want a confirm option, we can use
substitute command.

If we want to delete all the `console.log` in current file, we can do it like
this:
```vim
:%s;.*console.log.*\n;;c
```

Here's the breakdown of those command:
- `%` symbol to tell vim that the range is the current file or all the line in
the file.
- `s` is the abbreviation of substitute command.
- `.*console.log.*\n` is the pattern that select the line that has
`console.log` in it, which include the newline character at the end.
- `;;` is basically telling vim to replace it with nothing.
- `c` is a confirm option.

We can also combine substitute command with global command, like this:
```vim
:g;console.log;s;.*\n;;c
```

## Conclusion

There's a lot of possibility with global and substitute command and we won't
be able to cover all of those in one blog post. So, i will leave it to you to
explore those possibility.

You might want to learn regular expression to enhance the global and
substitute command experience.

Alright, that's it for this post. Thank you for reading!

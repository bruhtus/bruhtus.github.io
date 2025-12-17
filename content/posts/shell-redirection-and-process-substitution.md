---
author: Robertus Diawan Chris
title: "Shell Redirection and Process Substitution Combination"
date: 2025-12-17T05:54:28+07:00
tags: [Shell]
ShowToc: true
---

## A Brief Intro

Recently i watch "Bread on Penguins" video about [shell process
substitution](https://youtu.be/2A4bs40scSo) on youtube. I already know about
shell process substitution and write the blog post about [shell process
substitution as temp file](../shell-process-substitution-as-temp-file.md), but
what is interesting from the video is the combination of shell process
substitution and shell redirection that i didn't think about previously.

## Shell Redirection

If you didn't know about shell redirection, it's basically redirecting the
output of a program to a file. Here's an example of shell redirection:
```sh
echo 'something' > big.txt
```

The example above means that we write `something` to file `big.txt`. Keep in
mind that the example about will _replace_ any contents in the `big.txt` file,
so be careful.

If we want to _append_, we can use `>>` operator like this:
```sh
echo 'something' >> big.txt
```

For more info, you can check on the posix specification about redirection in
then references section below.

## Shell Process Substitution

Shell process substitution is basically a mechanism to save the output of a
process inside a "file descriptor" and we can use those "file descriptor" like
a temporary file, which means those "file descriptor" will disappear after the
current command that _use_ process substitution is done.

If you are still confused, think of the process like _primary process_ and
_subprocess_. The current command that _use_ process substitution is the
_primary process_ and the process inside the process substitution is the
_subprocess_.

Here's an example:
```sh
diff <(sort file1) <(sort file2)
```

In the example about, we are sorting `file1` and `file2` first and the save
the result in 2 "file descriptor". And then we run `diff` command with those
"file descriptor" like a regular file, and after `diff` command done, those
"file descriptor" is gone.

In case you are wondering "what is file descriptor?", let me try explaining
that. I mean, by the time of writing this blog post, i'm still not sure yet
what is "file descriptor" but i know that it's not a regular file.

When we open an existing file or create a new file, the kernel returns a "file
descriptor" to the process, and as far as i understand, "file descriptor" is
an information about _opened_ file.

So what shell process substitution does is using those "file descriptor"
mechanism as a temporary file to store the output of another process, which is
quite neat.

But, keep in mind that __not all shell have process substitution__, the one
that i know have process substitution is `bash` and `zsh`. So before you try
it, please check if your shell have process substitution or not.

## Combination

This is the example from the youtube video that is interesting, we can combine
the shell redirection and shell process substitution to separate the output
and error from a command.

Let's say we have a command called `something` and we want to check the output
of those command in `less` command and also save the error messages in a file
called `errors.txt`. We can do that like this:
```sh
something > >(less) 2> >(tee errors.txt)
```

The symbol `>` is the same as `1>` which means redirect the output of the
program, and symbol `2>` means redirect the error messages of the program. If
you want to know more about it, look up `stdout` and `stderr`.

With that in mind, with `> >(less)` means we are redirecting the output of
`something` to process substitution with command `less` which i assume passing
the output to `less` _stdin_ (or the input) and similar to piping like
`something | less` (?), i'm still not sure how it works. But the difference
between piping and process substitution is that piping only able to
run _one_ process at time, meanwhile process substitution can run multiple
process _simultaneously_, at least that is from my observation. Again, i'm
still not sure how that work yet.

And with `2> >(tee errors.txt)` means we are redirecting the error messages of
`something` to process substitution with command `tee errors.txt`. I guess
it's like we are passing `something` error message to `tee` _stdin_ and save
those input in `errors.txt` file.

Here's another example:
```sh
sort file > >(diff file -)
```

The command above is basically check the difference between unsorted and
sorted `file`. We provide `-` in `diff file -` to take the _stdin_ as the
input for the second argument.

At first glance, that might look the same as `sort file | diff file -`, but in
my observation, there's a slight difference. When using `sort file > >(diff
file -)`, my shell prompt does not appear but i can execute command like
normal. And when using `sort file | diff file -` or `diff file <(sort file)`,
my shell prompt appear like normal. Still not sure what is the difference.

Alright, that's it for now. See you next time!

## References

- [Bread on Penguins youtube video](https://youtu.be/2A4bs40scSo).
- [POSIX specification about
redirection](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/V3_chap02.html#tag_18_07).
- [POSIX specification about file
descriptor](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap03.html#tag_03_166).
- [Medium post about
/proc/self/fd](https://medium.com/@zoningxtr/unlocking-the-power-of-proc-self-fd-in-linux-from-basics-to-exploits-and-prevention-f58d2202b129).
- [Stackoverflow answer about file
descriptor](https://stackoverflow.com/a/17741176).

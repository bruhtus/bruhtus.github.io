---
author: Robertus Diawan Chris
title: "Git Rebase Chained Branch"
date: 2025-04-18T06:31:02+07:00
tags: [Git]
ShowToc: false
---

Let's say we try to develop a feature B, but those feature need feature A
which haven't merged already. So, to prevent the diff changes getting larger,
we create new branch to develop feature B. And then, in the middle of
development of feature B, the branch that contains feature A got merged into
main branch and all the commit from feature A got squashed into one commit.
And when we try to rebase the feature B branch with main branch, we got a
conflict from a commit which previously in feature A branch.

To make the situation clear, let's take a look at this:
```
Before:
A---B (main branch)
     \
      C---D (feature-A branch)
           \
            E---F (feature-B branch)

After:
A---B---G (main branch, with G commit as squashed C and D commit)
         \
          E---F (feature-B branch)
```

The steps to make the conflict scenario are:
```sh
mkdir git-test
cd git-test
git init -b main
echo 'first' > something
git add something
git commit -m 'first'
echo 'second' >> something
git commit -am 'second'
git checkout -b feature-A
echo 'third' >> something
git commit -am 'third'
echo 'fourth' >> something
git commit -am 'fourth'
git checkout -b feature-B
echo 'fifth' >> something
git commit -am 'fifth'
echo 'sixth' >> something
git commit -am 'sixth'
git checkout main
git merge --squash feature-A
git commit --no-edit
git checkout feature-B
git rebase main
```

> To get out from the conflict, we can use `git rebase --abort`.

Now, to prevent the conflict, instead of using `git rebase`, we can use
`git rebase --fork-point --onto` like this:
```sh
git rebase --fork-point --onto main feature-A feature-B
```

Flag `--fork-point` is to find the common commit point from main branch with
feature-A branch and flag `--onto` is to change the parent commit from
feature-A branch to main branch and apply the commit that exist in feature-B
branch but not in feature-A branch "onto" main branch commit.

Alright, that's it. See you next time!

## References

- [Stackoverflow git rebase --onto answer](https://stackoverflow.com/a/29916361).
- [Women on rails git rebase --onto an
overview](https://womanonrails.com/git-rebase-onto).
- [Stackoverflow git rebase --fork-point answer](https://stackoverflow.com/a/77800408).

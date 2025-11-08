---
author: Robertus Diawan Chris
title: "Git Rebase Chained Branch"
date: 2025-04-18T06:31:02+07:00
tags: [Notes, Git]
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

Now, let's say we have more than one branch that depends on feature A branch.
Do we need to do `git rebase --fork-point --onto ...` multiple time? Well,
fortunately there's a flag `--update-refs` that will automatically do that for
us.

To make the situation clear, let's take a look at this:
```
Before:
A---B (main branch)
     \
      C---D (feature-A branch)
           \
            E---F (feature-B branch)
                 \
                  G---H (feature-C branch)
                       \
                        I---J (feature-D branch)

After:
A---B---K (main branch, with K commit as squashed C and D commit)
         \
          E---F (feature-B branch)
               \
                G---H (feature-C branch)
                     \
                      I---J (feature-D branch)
```

To use `--update-refs`, we need to rebase the top of the chained branch, which
in the case above is `feature-D` branch. With that in mind, we can use this
command:
```sh
git rebase --update-refs --onto main feature-A feature-D
```

`--onto main feature-A feature-D` means rebase the range of commits in
`feature-A` branch until `feature-D` branch on top of commits from `main`
branch. We use `feature-A` because it's the base branch for our
chained branch and `feature-D` because it's the top of the chained branch.

> We don't need to use `--fork-point` to guess which common commit in all
> those branch because `--update-refs` already do that for us.

Now, what should we do if we merge `feature-B` branch? The situation would be
like this:
```
Before:
A---B---K (main branch, with K commit as squashed C and D commit)
         \
          E---F (feature-B branch)
               \
                G---H (feature-C branch)
                     \
                      I---J (feature-D branch)

After:
A---B---K---L (main branch, with L commit as squashed E and F commit)
             \
              G---H (feature-C branch)
                   \
                    I---J (feature-D branch)
```

We can use the same command as above but, instead of using `feature-A`, we use
`feature-B` instead, like this:
```sh
git rebase --update-refs --onto main feature-B feature-D
```

Next scenario, let's say we create new branch `feature-E` from `feature-D`
branch and add new commit in it. And then, we got some feedback for `feature-C`
branch so we need to add new commit. The situation would be like this:
```
Before:
A---B---K---L (main branch, with L commit as squashed E and F commit)
             \
              G---H---O (feature-C branch)
                   \
                    I---J (feature-D branch)
                         \
                          M---N (feature-E branch)
```

With the situation above, we can checkout into `feature-E` branch or the top
of the chained branch and use `--update-refs` like this:
```sh
git rebase --update-refs feature-C
```

Alright, that's it. See you next time!

## Side Note

We can push all those branch as an atomic operation, which means, if one of
the branch failed to push into a remote branch, the others branch won't update
the remote branch too. To do that, we can use flag `--atomic` in git push like
this:
```sh
git push --atomic origin feature-C feature-D feature-E
```

Please keep in mind that if you're rebasing the git branch you want to push,
you might need flag `--force-with-lease` in git push, like this:
```sh
git push --force-with-lease --atomic origin feature-C feature-D feature-E
```

## References

- [Stackoverflow git rebase --onto answer](https://stackoverflow.com/a/29916361).
- [Women on rails git rebase --onto an
overview](https://womanonrails.com/git-rebase-onto).
- [Stackoverflow git rebase --fork-point answer](https://stackoverflow.com/a/77800408).
- [Git rebase --update-refs blog
post](https://andrewlock.net/working-with-stacked-branches-in-git-is-easier-with-update-refs/)

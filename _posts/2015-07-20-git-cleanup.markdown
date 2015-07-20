---
layout: post
title:  "Cleaning up your git branches."
date:   2015-07-20 00:00:00
categories:
  - git
---

I recently added the following to my `~/.gitconfig` and I feel the need to share.

```
[alias]
  cleanup = !git branch --merged | grep -v master | xargs -n 1 git branch -d
```

And then I can run `git cleanup` to cleanup my git branches.

On any given week, I can generate anywhere from 5-20 new branches, and over
time, they start getting in the way of my productivity.  I rely on
tab-completion for most things in my shell, as well as creating quite verbose
branch names.  Whenever I can't tab complete a branch name, it can result in an
annoying amount of typing.

Now, I can run one command to nuke everything that has already been merged into
`master`.

Piece by piece breakdown.

- `git branch --merged` - lists all of the git branches that have been merged
  into the current branch.
- `grep -v master` - remove any lines that are `master` because I **never**
  want to delete master. 
- `xargs -n 1 git branch -d` - run `git branch -d <branch_name>` for all of the
  branches listed. 

One of the nice things about the last command which actually does the deletion
is that using `-d` instead of `-D` makes it pretty safe in that it will throw
errors on attempts to delete any unmerged branches.

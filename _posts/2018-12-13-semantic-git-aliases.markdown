---
layout: post
title: "Semantic Git Aliases"
tags:
  - Git
---

I've seen a lot of posts about configuring `.gitconfig` files with aliases like
`co = checkout`. I can see the value in saving some keystrokes (and
misspellings), but I personally don't use those aliases because the number of
letters in a command is usually not the bottleneck I encounter with Git. I do,
however, use a handful of aliases that I find very helpful. If I were to
classify them, I'd say they're semantic aliases.

## TL;DR

```bash
[alias]
  uncommit = reset --soft HEAD^
  unstage = reset
  staged = diff --cached
  ctags = "!.git/hooks/ctags"
```

### uncommit
As of the time of writing, a question on StackOverflow named ["How to
uncommit my last commit in Git"](https://stackoverflow.com/questions/2845731/how-to-uncommit-my-last-commit-in-git)
had been upvoted 666 times, and its accepted answer had been upvoted 1068 times.
The title alone suggests what users are _trying_ to do. If you think about it,
`git reset --soft HEAD^` makes sense, but [don't make me think](https://en.wikipedia.org/wiki/Don't_Make_Me_Think).

Aliasing this command has taken that mental (and since I end up searching for
the syntax every time, time) overhead out of the equation. It's been all upside.

### unstage
Here's what I see every time I issue `git status`:
```bash
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
```

Use this command to **unstage**. While I don't need to open up my browser to
look up the syntax, I still have to translate a command to its purpose. This
alias ends up adding a keystroke, but I find it reduces the mental energy spent
on the task. Thanks to [Thoughtbot](https://thoughtbot.com/upcase/videos/git-customizing#aliases)
for bringing this to my attention.

### staged
I try to be very intentional about my commits, grouping alike changes to ensure
that the story I'm telling to the reviewer (and future maintainers) is coherent.
In the process of preparing a commit I often look at the current diff, as well
as what has already been **staged**. I used to have `git diff --cached`
memorized for this purpose, but aliasing that command has made it so much
friendlier to use.

While `uncommit` and `unstage` are used occasionally, I use `staged` all the
time.

### ctags
This one sheds less light on what Git itself is doing, but I find it useful and
hope others will as well. It relies on Tim Pope's [Effortless Ctag with Git](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html)
setup, which I highly recommend if you're a Vim user. With this alias, you can
re-index your codebase without relying on a Git hook.

I hope you find these aliases as helpful as I have, not for their ability to
save keystrokes, but for how accessible they make some of Git's powerful
features.

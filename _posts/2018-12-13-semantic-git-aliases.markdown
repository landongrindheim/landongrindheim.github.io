---
layout: post
title: "Semantic Git Aliases"
tags:
  - Git
---

I've seen quite a few posts about setting up `.gitconfig` files with aliases
like `co = checkout`. I see the value in saving some keystrokes (and
misspellings), but I personally don't use those aliases. Command length is
rarely the pain point I experience with Git. I do, however, use a handful of
aliases that I find very helpful. I like to classify them as semantic aliases.

## TL;DR
```bash
[alias]
  uncommit = reset --soft HEAD^
  unstage = reset
  staged = diff --cached
  ctags = "!.git/hooks/ctags"
```

### uncommit
As of the time of writing, a question on StackOverflow named ["How to uncommit
my last commit in Git"](https://stackoverflow.com/questions/2845731/how-to-uncommit-my-last-commit-in-git)
had been upvoted 666 times, and its accepted answer had been upvoted 1068 times.
The title alone suggests what users are _trying_ to do. They're just not sure
how to accomplish it. The answer is to use `git reset --soft HEAD^`, which, if
you spend some time thinking about it, makes sense. But [don't make me think](https://en.wikipedia.org/wiki/Don't_Make_Me_Think).

Aliasing the command to `uncommit` has taken the mental overhead out of the
equation. And since I'm not searching for the correct syntax every time, it also
saves me time. It's been all upside.

### unstage
Here's what I see every time I issue `git status`:
```bash
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
```

"Use ... to **unstage**". While I don't need to open up my browser to look up
the syntax, I still have to translate the command and arguments to understand
its purpose. Aliasing to `unstage` ends up adding a keystroke, but I find it
reduces the mental energy spent on the task. Thanks to [Thoughtbot](https://thoughtbot.com/upcase/videos/git-customizing#aliases)
for sharing and making my Git experience better!

### staged
I try to be very intentional about my commits, grouping alike changes to ensure
that the story I'm telling to the reviewer (and future maintainers) is coherent.
In the process of preparing a commit I often look at the current diff, as well
as what has already been **staged**. I used to have `git diff --cached`
memorized for this purpose, but aliasing the command has made it so much
more friendly to use.

While `uncommit` and `unstage` are used occasionally, I use `staged` all the
time.

### ctags
This alias sheds less light on what Git itself is doing, but I find it useful
and hope others will as well. It relies on Tim Pope's [Effortless Ctags with
Git](https://tbaggery.com/2011/08/08/effortless-ctags-with-git.html) setup,
which I highly recommend if you're a Vim user. With this alias, you can
re-index your codebase without relying on a Git hook.

I hope you find these aliases as helpful as I do, not for their ability to save
keystrokes, but for how accessible they make some of Git's powerful features.

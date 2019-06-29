---
layout: post
title: "Git Bisect by Example"
tags:
  - Git
---

### TL;DR
```bash
git bisect start
git bisect bad <bad-commit-sha>
git bisect good <safe-commit-sha>
git bisect run <command-proving-existence-of-bug>
...
git bisect reset
```
### Introduction
[`git bisect`](https://git-scm.com/docs/git-bisect) can be used to find the
commit that introduced a bug. It's a multi-step process, and, like a lot of
Git's user interface, can appear intimidating. However, I think it's actually
pretty user-friendly, and hope that by the end of this walk-through that you'll
feel comfortable enough to add it to your debugging arsenal.

### When to reach for `git bisect`

If you've got a bug in your codebase, but you're not sure when it was
introduced, `git bisect` just might be your new best friend. Let's pretend we've
got the following set of commits.

```bash
$ git log --oneline --reverse
c8e67bd We know everything is great here 😄
3be3992 Everything is still 👌
a02fd55 Oh no! A bug was introduced here 😬
0d328c9 (HEAD -> master) The bug still exists 🙁
```

We know that the world was once okay, and that it isn't now. Something changed.
If only we could find out when things broke. `git bisect` to the rescue!

### Let's do this!

First things first, we need to tell Git that we're ready to start.

```bash
git bisect start
```

Next, we'll give Git the sha of a commit which we know is in a bad state. Most
of the time this will be the latest commit (`HEAD`). In the example above, we
know there's a bug as recently as `0d328c9`. Let's use that.

```bash
git bisect bad 0d328c9
```

Next, we need to tell Git the last known good state. Since we know our bug
wasn't around in `c8e67bd`, we'll use that.

```bash
git bisect good c8e67bd
```

Now comes the exciting part! We'll provide a command which will be used by Git
to find the commit which introduced the bug. For simplicity's sake, I'll use
`rspec`, but it can be anything so long as it returns a non-zero exit code when
there is a problem, and a zero exit code when everything is okay.

```bash
git bisect run bundle exec rspec
```

`git bisect` will do its thing, performing a [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm)
to find the offending commit. When it's done, it'll return something like this:

```bash
a02fd559f5e67e591995ea29ec6b59cee533e7bb is the first bad commit
commit a02fd559f5e67e591995ea29ec6b59cee533e7bb
Author: Landon Grindheim <landon@please_dont_email_me.com>
Date:   Wed Jun 26 11:18:28 2019 -0400

Oh no! A bug was introduced here 😬

:040000 040000 6f3ec5c74c92f63257ee3739af6fb3b5fa66e1ed 3e54a644bb352f01f5b6891b898c8b4446a2b5e9 M    spec
bisect run success
```

Success!

What if you have a bug, but no test to surface it? Git still has you covered! If
you can't write a test, you can walk Git through the process by performing
manual tests then feeding Git the result by issuing `git bisect bad` or
`git bisect good` (no sha). It's a more manual process, but the result will be
the same!


### Important!

It's important that you run `git bisect reset` when you're done, or you'll find
yourself without a branch.

----

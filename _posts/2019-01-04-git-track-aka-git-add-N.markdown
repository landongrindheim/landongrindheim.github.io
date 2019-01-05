---
layout: post
title: "git track (aka git add -N)"
tags:
  - Git
---

### Tracking new files
I learned a new bit of Git functionality this week while pairing with a
co-worker, namely the `-N` flag for `git add`. Whenever you create a new file in
a Git repository, it is considered "untracked" until you make Git aware of it.

```bash
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        file.ext
```

`git add -N`[¹](#from-the-git-add-man-page) effectively tells Git to track the
file. After issuing the command, the contents of `file.txt` will be available
to Git as if they were introduced in a previous commit. You'll be able to diff,
grep and mv `file.txt` (and more!) without actually having to stage the file.

### A new alias
To follow up on my last post, [Semantic Git Aliases]({{ site.baseurl }}{% post_url 2018-12-13-semantic-git-aliases %}),
I created an alias that I feel more aptly describes this operation, `git track`.
I've been using the alias for a couple of days now and have found it both useful
and intuitive. I hope you'll agree.

```bash
[alias]
  track = add -N
```

----
----
#### From the `git add` man page:
```bash
 -N, --intent-to-add
   Record only the fact that the path will be added later. An entry
   for the path is placed in the index with no content. This is useful
   for, among other things, showing the unstaged content of such files
   with git diff and committing them with git commit -a.
```

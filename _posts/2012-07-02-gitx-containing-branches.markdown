---
author: zaki
date: '2012-07-02 19:55:18'
layout: post
type: blog
slug: gitx-containing-branches
status: publish
title: GitX branches containing a commit
comments: true
tags:
  - gitx
---

Our team usually use [laullon's gitx fork](http://gitx.laullon.com) as a git GUI on OSX and so far it has worked
brilliantly. However as we started to use many branches recently, after a while it got hard for everyone
to see which commit belonged to which branch and whether/where it was merged into.

<img src="/images/gitx-branches.png" alt="GitX branches - The next train to origin/master departs from platform 5" />

So I went ahead and added a simple function to get the branches that contain a selected commit.
Just right click on any commit, select "Containing Branches" and you'll get a list of branches
that contain that commit.

In the background it just calls ``` git branch -r --containing abcd1234 ```

<img src="/images/gitx-containing-branches.png" alt="GitX containing branches" />

<img src="/images/gitx-branch-list.png" alt="GitX branch list" />

Get the source and binary build (OSX 10.6+) from github:

<a href="http://github.com/zaki/gitx" class="btn btn-primary">Github:zaki/gitx</a>


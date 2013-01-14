---
layout: post
title: "Why I Love Git"
date: 2012-12-10 11:05
comments: true
categories: ["Git","SVN","DVCS"] 
---

Recently I was in a discussion with couple of friends who still have to migrate on to the Git version control system. My friends had a lot of questions and showed hesitation in moving from SVN to Git. This post articulates my thoughts on why you should use Git and contains links to some excellent resources on Git that you can use to understand Git quickly.

<!-- more -->

## What is Git?

Git is a distributed version control(DVCS) system. In Git you are not required to connect to a central server for tracking changes. Changes or revisions to files are tracked locally in Git workspace, `.git` folder, of the project. Every Git workspace is a complete version control system that maintains complete version history. In other words, Git does not need to connect to any remote server for tracking changes. 

Git allows easy collaboration to remote respoitories by using `push` and `pull` operations. Since each Git workspace is a complete version control system, developers can also `push` and `pull` changes with each other. This is fundamentally different when compared to SVN/CVS which requires a central repository (repo) for tracking changes. To simplify collaboration most project setups have a remote repo where everybody pushes and pulls from. This remote repo is commonly referred as "origin". It is incorrect to think of the "origin" as a central repo equivalent of SVN/CVS as Git allows you to have multiple "origin" repos. With all this in mind you can also think of Git as "Decentralized" version control system.

## Why should you use Git? 

Git is fast as all chnages are first committed to local repo and then later pushed to a remote repository.

In git branching and merging is a first class operation i.e. fast and easy. Compared to SVN it does not involve figuring out change sets that should be merged. Anyone who has created and merged branches in SVN would say you need a whole day to merge branches and resolve conflcts. But, since Git maintains complete revision history merging is simple, does not require figuring out change sets and results in no conflicts. In fact once you start working with local branches in Git, it quickly becomes your favorite and essential feature of Git. 

Git also maintains complete SHA1 checkums for each commit to the repository. These checksums help it in maintaning the repo integrity by rejecting malicious code merges from downstream repos that fail checksum verifications. 

If you still have doubts on why you should use Git watch the Tech Talk from the master "Linus Torvalds" himself on why you should use Git. 

{% youtube 4XpnKHJAok8 %}


## Learning Git

1. Git Basics
    * [git - the simple guide](http://rogerdudler.github.com/git-guide/)
    * [Git In Five Minutes](http://scottr.org/presentations/git-in-5-minutes/)
2. Beyond Basics
    * [Pro Git book](http://git-scm.com/book)  
    * [Git User Manual](http://www.kernel.org/pub/software/scm/git/docs/user-manual.html)

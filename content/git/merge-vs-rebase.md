---
layout: default
title:  "Merge vs Rebase"
---

# Merge vs Rebase
{:.no_toc}

* TOC
{:toc}

## Initial State
Imagine We have a long running branch called feature that is behind master, which we want to merge.

```bash
git log --graph --decorate --oneline --all
# * 5e59f79 (HEAD -> master) Master moving even more
# * cd5ca21 Master moving forward
# | * 64488c1 (feature) Feature - 2
# | * 6d10413 Feature - 1
# |/  
# * f38dfc2 Initial
```

There are several ways we can do it.

## Option 1 - Merge `feature` to `master`

```bash
git checkout master
git merge feature -m "merge feature"
git branch delete feature

git log --graph --decorate --oneline
# *   16bd684 (HEAD -> master) merge feature
# |\  
# | * 64488c1 (feature, delete) Feature - 2
# | * 6d10413 Feature - 1
# * | 5e59f79 Master moving even more
# * | cd5ca21 Master moving forward
# |/  
# * f38dfc2 Initial
```
- We did __not__ need to `commit`
- History of `master` is not the prettiest
- We have multiple commits on `master` incoming from `feature`

## Option 2 - Squash feature to master

```bash
git checkout master
git merge feature --squash
git commit -m "squashed"
git branch delete feature

git log --graph --decorate --oneline
# * 9741a37 (HEAD -> master) squashed
# * 5e59f79 Master moving even more
# * cd5ca21 Master moving forward
# * f38dfc2 Initial
```

- We __did__ need to `commit`
  - We entered a new commit message
- History of `master` is prettier
  - We lost our commit messages in the feature branch
- We have a single commit on `master` incoming from `feature`

## Option 3 - Rebase and Merge

```bash
git checkout feature

git rebase master
# First, rewinding head to replay your work on top of it...
# Applying: Feature - 1
# Applying: Feature - 2

git checkout master
git merge feature
git branch delete feature

git log --graph --decorate --oneline
# * e6ae076 (HEAD -> master, feature, delete) Feature - 2
# * 921c475 Feature - 1
# * 5e59f79 Master moving even more
# * cd5ca21 Master moving forward
# * f38dfc2 Initial

```

- We did __not__ need to `commit`
- We did not lose our commit messages from the `feature` branch
- We have multiple commits on `master` incoming from `feature`
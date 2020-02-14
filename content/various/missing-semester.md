---
layout: default
title:  "The Missing Semester of Your CS Education"
---

# The Missing Semester of Your CS Education
{:.no_toc}

* TOC
{:toc}

## Overview
Notes to my self from [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/).

## Lecture 1: The Shell
- Most platforms provide some kind of shell.
  - [GNU bash](https://www.gnu.org/software/bash/) is a very famous one.
- Shell searches locations of programs to execute by looking at the paths defined in the `$PATH` <strong>environmental variable</strong>.

```bash
# Calling programs with arguments that have white space
echo Hello\ World  # Escaping
echo "Hello World" # Surrounding in quotes

# Printing all environmental variables
env

# Printing a single environmental variable
echo $PATH

# Finding the location of a program in case an executable would be called
which mvn
# /usr/local/bin/mvn

# Printing the current directory
pwd

# Switching between previous folders
cd -
```
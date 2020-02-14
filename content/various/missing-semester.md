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
- Most programs take arguments, and mostly they start with a dash (`-`).

### Rewiring Input/Output Streams
Most programs have 2 default streams: input and output. By default the input is the keyboard and the output is the screen. These streams can be re-wired as follows:

```bash
# Instead of outputting to console, output to this file
echo hello > hello.txt # Rewire output to hello.txt

# Rewire the input for a program as follows
cat < hello.txt # Rewire input to hello.txt

# Rewire both
cat < hello.txt > goodbye.txt # Input from hello.txt, output to goodbye.txt
```

### Piping
Pipe is just `|` and what it says is: _Take the output of the program to the left and make it the input to the program to the right_.

```bash
# The following first lists all files and then pipes the output to grep zaa
ls -l | grep zaa
# -rw-r--r--   1 kt  staff     4 13 Feb 20:36 zaa.txt

# Finding content type of google.com
curl --head --silent http://www.google.com | grep --ignore-case content-type
# Content-Type: text/html; charset=ISO-8859-1
```

### Cheat Sheet from Lecture 1
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

# Listing invisible files with ls
ls -f

# Switching to root user in shell
sudo -i
# When shell is running as root, it starts with a #
# When shell is not running as root, it starts with a $

```
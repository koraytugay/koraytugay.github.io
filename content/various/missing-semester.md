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
Programs (by default?) have 2 default streams: __input__ and __output__. By default the input is the __keyboard__ and the output is the __console__. These streams can be re-wired as follows:

```bash
# Instead of outputting to console, output to this file
echo hello > hello.txt # Rewire output to hello.txt

# Rewire the input for a program as follows
cat < hello.txt # Rewire input to hello.txt

# Rewire both
cat < hello.txt > goodbye.txt # Input from hello.txt, output to goodbye.txt
```

### Piping
Piping between programs can be achieved by the pipe character, `|`, and what it says is: _Take the output of the program to the left and make it the input to the program to the right_.

```bash
# The following first lists all files and then pipes the output to grep zaa
ls -l | grep zaa
# -rw-r--r--   1 kt  staff     4 13 Feb 20:36 zaa.txt

# Finding content type of google.com
curl --head --silent http://www.google.com | grep --ignore-case content-type
# Content-Type: text/html; charset=ISO-8859-1
```

### Cheat Sheet
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
<<<<<<< HEAD

```

## Lecture 2: Shell Tools and Scripting

### Strings in Bash
Strings in bash can be confusing since double quotes and single quotes are more or less the same but not exactly the same. Follow the example:

```bash
# Speaking of how they are same, all below prints the same thing, hello
echo hello
echo "hello"
echo 'hello'

# Speaking of differences, declare a variable
myName=Koray

# The following will print: My name is: Koray
echo My\ Name\ is:$myName
echo "My Name is: $myName"

# The following will print: My name is: $myName
echo 'My Name is: $myName' # Variables in single quotes are not processedp
```

### Variables
Defining a variable is as easy as:

```bash
# Declaring a variable
foo=bar
```

Note, this is very different to the following, where a program `foo` is being called with two arguments: `=` and `bar`.

```bash
foo = bar
```

And accessing a variable would be as follows:

```bash
# Accessing a variable
echo $foo # Prints bar
```

Result from any program can also be stored in a variable:

```bash
myFavoriteDirectory=$(pwd)  # pwd prints the current (working) directory
```

### Functions
Functions defined in files can be imported to shell using the `source` command and can be invoked afterwards. Here is an example.. Create the following file named `myScript.sh`:

```bash
mcd() {
    mkdir -p "$1"  # Create a directory
    cd "$1"        # Change directory
}
```

Import this file in bash via:

```bash
source myScript.sh
```

Now you can automate creating and entering the directory as follows:

```bash
mcd my-new-folder
```

### Cheat Sheet

```bash
# Last exit code: $?
test -d Applications # Is Applications a directory? For more try: man test
echo $? # Prints 0 in case true, 1 in case false

# Chaining conditions
test -d Applications && echo "Applications is a directory" 
```

### Examples
#### For-Each with Conditional

```bash
#!/bin/bash

echo "Executing program at: $(date)."
echo "Running program: $0 with $# number of arguments." # $0 is program itself
echo "Process ID is: $$" # $$ is the process id

for name in "$@"; do  # $@ is all arguments passed in
    if [[ $name == Koray ]]; 
    then
        echo "Hello, you.."
    else
        echo "Hello $name!"
    fi
done
```

Sample run:

```bash
kt$ ./bashScript.sh foo bar baz Koray
# Executing program at: Sat 15 Feb 2020 10:53:56 EST.
# Running program: ./bashScript.sh with 4 number of arguments.
# Process ID is: 48732
# Hello foo!
# Hello bar!
# Hello baz!
# Hello, you..
```

## Lecture 2: Shell Tools and Scripting

### Strings in Bash
Strings in bash can be confusing since double quotes and single quotes are more or less the same but not exactly the same. Follow the example:

```bash
# Speaking of how they are same, all below prints the same thing, hello
echo hello
echo "hello"
echo 'hello'

# Speaking of differences, declare a variable
myName=Koray

# The following will print: My name is: Koray
echo My\ Name\ is:$myName
echo "My Name is: $myName"

# The following will print: My name is: $myName
echo 'My Name is: $myName' # Variables in single quotes are not processedp
```

### Variables
Defining a variable is as easy as:

```bash
# Declaring a variable
foo=bar
```

Note, this is very different to the following, where a program `foo` is being called with two arguments: `=` and `bar`.

```bash
foo = bar
```

And accessing a variable would be as follows:

```bash
# Accessing a variable
echo $foo # Prints bar
```

Result from any program can also be stored in a variable:

```bash
myFavoriteDirectory=$(pwd)  # pwd prints the current (working) directory
```

### Functions
Functions defined in files can be imported to shell using the `source` command and can be invoked afterwards. Here is an example.. Create the following file named `myScript.sh`:

```bash
mcd() {
    mkdir -p "$1"  # Create a directory
    cd "$1"        # Change directory
}
```

Import this file in bash via:

```bash
source myScript.sh
```

Now you can automate creating and entering the directory as follows:

```bash
mcd my-new-folder
```

### Cheat Sheet

```bash
# Last exit code: $?
test -d Applications # Is Applications a directory? For more try: man test
echo $? # Prints 0 in case true, 1 in case false

# Chaining conditions
test -d Applications && echo "Applications is a directory" 
```

### Examples
#### For-Each with Conditional

```bash
#!/bin/bash

echo "Executing program at: $(date)."
echo "Running program: $0 with $# number of arguments." # $0 is program itself
echo "Process ID is: $$" # $$ is the process id

for name in "$@"; do  # $@ is all arguments passed in
    if [[ $name == Koray ]]; 
    then
        echo "Hello, you.."
    else
        echo "Hello $name!"
    fi
done
```

Sample run:

```bash
kt$ ./bashScript.sh foo bar baz Koray
# Executing program at: Sat 15 Feb 2020 10:53:56 EST.
# Running program: ./bashScript.sh with 4 number of arguments.
# Process ID is: 48732
# Hello foo!
# Hello bar!
# Hello baz!
# Hello, you..
```

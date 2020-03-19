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

## The Shell
Most platforms provide some kind of shell. [GNU bash](https://www.gnu.org/software/bash/) is a very famous one.

### Environmental Variables
Shell searches locations of programs to execute by looking at the paths defined in the `$PATH` <strong>environmental variable</strong>. To print all environmental variables, use the `env` command:

```bash
env
# SHELL=/bin/bash
# USER=kt
# PATH=/usr/local/opt/node@10/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
# PWD=/Users/kt
# JAVA_HOME=/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
# LANG=en_CA.UTF-8
# HOME=/Users/kt
```

`env` itself lives in `/usr/bin/env`, which can be verified by:

```bash
which env
# /usr/bin/env
```

A single enviromental variable can be accessed via:

```bash
echo $PATH
# /usr/local/opt/node@10/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

### Redirection
Programs have 3 default streams: __input__, __output__ and __error__. By default the input is the __keyboard__ and output and error is the __console__. These streams can be redirected to other files or streams as follows:

```bash
# Instead of outputting to console, output to this file
echo hello > hello.txt # Redirect standard output to hello.txt

# Redirect the input for a program as follows
cat < hello.txt # Rewire input to hello.txt

# Redirect both
cat < hello.txt > goodbye.txt # Input from hello.txt, output to goodbye.txt
```

The outputs can also be disgarded by redirecting them to `/dev/null`. See [this](https://askubuntu.com/a/350216) answer for details.

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
```

## Shell Tools and Scripting

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
echo 'My Name is: $myName' # Variables in single quotes are not processed
```

### Variables
Defining a variable is as easy as:

```bash
# Declaring a variable
foo=bar
```

Note, the following example with spaces is very different. In the following example a program `foo` is being called with two arguments: `=` and `bar`.

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
    mkdir -p "$1"  # Create directory
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
# Last exit code can be access via: $?
# Example with test
test -d Applications # Is Applications a directory? 
echo $? # Prints 0 in case true, 1 in case false

# Example with grep
echo Hello | grep Hello > /dev/null; echo $?
# 0
echo Hello | grep Yello > /dev/null; echo $?
# 1

# Chaining conditions with boolean
# The following echos only if test returns 0
test -d Applications && echo "Applications is a directory"

# Using tldr instead of man
tldr grep
# grep
# 
# Matches patterns in input text.
# Supports simple patterns and regular expressions.
# 
# - Search for an exact string:
#     grep search_string path/to/file
# 
# - Search in case-insensitive mode:
#     grep -i search_string path/to/file
# 
# - Search recursively in current directory for an # exact string:
#     grep -RI search_string .
# 
# - Use extended regular expressions (supporting `?`, `+`, `{}`, `()` and `|`):
#     grep -E ^regex$ path/to/file
# 
# - Print 3 lines of [C]ontext around, [B]efore, or [A]fter each match:
#     grep -C|B|A 3 search_string path/to/file
# 
# - Print file name with the corresponding line number for each match:
#     grep -Hn search_string path/to/file
# 
# - Use the standard input instead of a file:
#     cat path/to/file | grep search_string
# 
# - Invert match for excluding specific strings:
#     grep -v search_string
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
./bashScript.sh foo bar baz Koray
# Executing program at: Sat 15 Feb 2020 10:53:56 EST.
# Running program: ./bashScript.sh with 4 number of arguments.
# Process ID is: 48732
# Hello foo!
# Hello bar!
# Hello baz!
# Hello, you..
```

## Command Line Environment
The shell sends a __SIGINT__ to the running program when you hit `CTRL+C` while a program is running. There are several signals which can be listed via `man signal`. Here are few:

```
No    Name         Default Action       Description                    Shortcut
2     SIGINT       terminate process    interrupt program              CTRL + C
3     SIGQUIT      create core image    quit program                   CTRL + \
9     SIGKILL      terminate process    kill program
15    SIGTERM      terminate process    software termination signal
17    SIGSTOP      stop process         stop (cannot be caught)        CTRL + Z
```

### Using Kill Comamand
Signals can be sent to processes running using the `kill` command. Here is the cheat sheat:

```bash
# Sends a signal to a process, usually related to stopping the process.
# All signals except for SIGKILL and SIGSTOP can be intercepted by the process 
# to perform a clean exit.

# Terminate a program using the default SIGTERM (terminate) signal:
kill process_id

# List available signal names (to be used without the `SIG` prefix):
kill -l

# Terminate a background job:
kill %job_id

# Terminate a program using the SIGHUP (hang up) signal. 
# Many daemons will reload instead of terminating:
kill -1|HUP process_id

# Terminate a program using the SIGINT (interrupt) signal. 
# This is typically initiated by the user pressing `Ctrl + C`:
kill -2|INT process_id

# Signal the operating system to immediately terminate a program 
# (which gets no chance to capture the signal):
kill -9|KILL process_id

# Signal the operating system to pause a program until a SIGCONT ("continue") 
# signal is received:
kill -17|STOP process_id

# Send a `SIGUSR1` signal to all processes with the given GID (group id):
kill -SIGUSR1 -group_id
```

Here is an example for a Java process. Remember `&` sends the execution to background. The following is a simple Java program that handles __SIGINT__ signals:

```java
import sun.misc.Signal;
import sun.misc.SignalHandler;

public class Main {
    public static void main(String[] args) throws Exception {
        Signal.handle(new Signal("INT"), new DebugSignalHandler());
        Thread.sleep(60 * 1000 * 10); // Sleep 10 minutes
    }
}

class DebugSignalHandler implements SignalHandler {
    @Override
    public void handle(Signal signal) {
        System.out.println("Received: " + signal);
    }
}
```

And here is an example of sending __SIGINT__ and __SIGKILL__ signals 

```bash
$ java Main &
# [1] 79925
$ javapid=$!  # $! returns the last background process's pid
$ echo $javapid
# 79925
$ kill -2 $javapid
# Received: SIGINT
$ kill -9 $javapid
```

## Security and Cryptography
### Symmetrical Cryptography with OpenSSL Example
The following command asks for a password from the user, from which it creates a key (so that you do not need to remember your key, but rather your password) and then uses the key to cipher the contents of the passed in file:

```bash
openssl aes-256-cbc -salt -in hello-world.txt -out hello-world.enc.txt
```

The very same encrypted file can be decrypted back (which of course also will ask for your password again) so that it can generate the same key and proceed with the operation:

```bash
openssl aes-256-cbc -d -in hello-world.enc.txt -out hello-world.dec.txt
```

## Things to Remember
```markdown
- Tool Long Did Not Read, alternative to man: `brew install tldr`.
  - Try: `tldr ls` in your shell.
- Using `htop`..
  - `brew install htop` then `htop`. 
    - You should be able to figure it out afterwards.
- Configuring `.bash_profile` to show `$` sign only..
  - Add the following in `~/.bash_profile`: `PS1='\$ '`.
```
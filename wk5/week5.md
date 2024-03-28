# Week 5 2420

## Learning Outcomes and Topics

This Week:

- Discuss how the shell executes a command
- Edit the .bashrc to create an alias
- Use the `ps` utility to view running processes
- Use redirects to redirect streams
- use a shebang in a shell script to specify an interpreter from within a file
- Write hello world in bash
- Write a shell script to create a new user on a server

Next Week:

- Midterm Exam

After the Midterm:

- Scripting
- Assignment 1
- WSL
- DigitalOcean

## Executing commands in the shell

We have been running commands in the shell since week 1. This week, we are going to dive a little deeper into what actually happens when we run a command.

The following is a brief description of the shell’s operation when it
reads and executes a command.  Basically, the shell does the
following:

1. Reads its input from a file (see [Shell Scripts](https://www.gnu.org/software/bash/manual/html_node/Shell-Scripts.html)), from a string
supplied as an argument to the `-c`  invocation option
(see [Invoking Bash](https://www.gnu.org/software/bash/manual/html_node/Invoking-Bash.html)), or from the user’s terminal.
2. Breaks the input into words and operators, obeying the quoting rules
described in [Quoting](https://www.gnu.org/software/bash/manual/html_node/Quoting.html). These tokens are separated by
`metacharacters`. Alias expansion is performed by this step
(see [Aliases](https://www.gnu.org/software/bash/manual/html_node/Aliases.html)).
3. Parses the tokens into simple and compound commands
(see [Shell Commands](https://www.gnu.org/software/bash/manual/html_node/Shell-Commands.html)).
4. Performs the various shell expansions (see [Shell Expansions](https://www.gnu.org/software/bash/manual/html_node/Shell-Expansions.html)), breaking
the expanded tokens into lists of filenames (see [Filename Expansion](https://www.gnu.org/software/bash/manual/html_node/Filename-Expansion.html))
and commands and arguments.
5. Performs any necessary redirections (see [Redirections](https://www.gnu.org/software/bash/manual/html_node/Redirections.html)) and removes
the redirection operators and their operands from the argument list.
6. Executes the command (see [Executing Commands](https://www.gnu.org/software/bash/manual/html_node/Executing-Commands.html)).
7. Optionally waits for the command to complete and collects its exit
status (see [Exit Status](https://www.gnu.org/software/bash/manual/html_node/Exit-Status.html)).

[Shell Operation (Bash Reference Manual)](https://www.gnu.org/software/bash/manual/html_node/Shell-Operation.html)

## A process is a running instance of a program

When you run a command, like `ls` or `git push` you are starting a new process.

## Child and parent processes

> Like the file structure, the process structure is hierarchical, with parents, children, and a *root.*
 A parent process *forks* (or *spawns* ) a child process, which in turn can fork other processes. The term *fork* indicates that, as with a fork in the road, one process turns into two. Initially the two forks are identical except that one is identified as the parent and one as the child. The operating system routine, or *system call,* that creates a new process is named **fork**().--A Practical Guide to Linux Commands, Editors, and Shell Programming, Fourth Edition
> 

When you start a new process, that process is a child process of the command that started it.

Using the above example, `git push` that would look like this:

- bash
    - git push

A child process of a command might start another process, which would be a child of the second process.

- bash
    - vim
        - grep

## Using the `ps` and `pstree` utilitities

There are a number of commands that we can use to help us visualize this. The `ps` command is a good place to start.

The `ps` command by itself will show you something like this🔻

```bash
PID TTY          TIME CMD
   6869 ?        00:00:00 bash
  10797 ?        00:00:00 ps
```

This is just the ps command itself and the parent process, bash. Try running the command `ps --forest` This will help you visualize the relationship between these commands.

If you want to see all of the running processes, there are two options

The BSD version:

- ps -aux
    - The `a` option tells `ps` to display the
    processes of all users. Only the processes that not associated with a
    terminal and processes of group leaders are not shown.
    - `u` stands for a user-oriented format that provides detailed information about the processes.
    - The `x` option instructs `ps` to list the processes without a controlling terminal. Those are mainly processes that are started on boot time and running in the background.

And the Unix version:

- ps -ef
    - The `e` option instructs `ps` to display all processes.
    - The `f` stands full-format listing, which provides detailed information about the processes.

The `ps` command also includes a formatting option, `-o` which lets you select the output.

`ps -eo comm,rss` will show you the command and ram used by a process.

You can combine this with a command like `grep` to filter the output 

`ps -eo comm,rss | grep ssh`

`pstree` is similar, only as the name might suggest it shows things in a tree like structure by default.

`pstree -p` show PIDs

## Streams and redirects

There are three streams in Linux shells

- 0: stdin
- 1: stdout
- 2: stderr

- 0 representing standard in are commands be written to the shell, usually these come from a keyboard
- 1 representing standard out are messages printed to the shell. The output of commands
- 2 representing standard error are error messages printed to the shell.

Sometimes you want to redirect streams from one location to another.

One of the most common examples of this is writing the output of a command to a file.

What is the difference between the two examples below?

```bash
ls -l > file
ls -l >> file
```

We can also redirect from a file to a command. 

Imagine I have a file containing names, and I want to sort them alphabetically.

```
Louise
Akiyo
Hannah
Jernej
```

Save the above in a file named ‘names’ and run this command `sort < names` 

Now try this `sort < names > sorted-names` This command will created a new file with the names in alphabetical order.

We can also redirect error messages, a common use case for this is to not show error message.

For example, `find /usr -name ls` I know this is going to include some Permission denied errors. I can redirect those errors to /dev/null (this is a file that doesn’t hold any values, so everything written to /dev/null is disgarded) `find /usr -name ls 2>/dev/null` 

[Redirections (Bash Reference Manual)](https://www.gnu.org/software/bash/manual/html_node/Redirections.html)

[How to manipulate files with shell redirection and pipelines in Linux](https://www.redhat.com/sysadmin/linux-shell-redirection-pipelining)

## Shell configuration files

Shell configuration files, or startup files, allow a user to customize their shell.

| System Wide | Personal | sourced |
| --- | --- | --- |
| /etc/profile | $HOME/.bash_profile | login |
| /etc/bashrc | $HOME/.bashrc | interactive |

There is also a $HOME/.bash_history file, this contains your command history. And a $HOME/.bash_logout.  

### Configuring your shell

There are a lot of things that you can do to configure your working environment by editing your .profile/.bash_profile and .bashrc files.

You can add a new directory to your path. I have the following in my .bash_profile, which adds the directory where programs written in rust are stored. This includes my text editor [Helix](https://helix-editor.com/)

```bash
if [ -d $HOME/.cargo/bin ]; then
	PATH="$HOME/.cargo/bin:$PATH"
fi
```

Why should the above be in .profile and not .bashrc?

You can add shortcuts or change the default behaviour of commands with aliases and functions.

```bash
# aliases
alias ls="ls --color=auto --group-directories-first"

#functions

# create a new directory and enter it
mkd() {
  mkdir -p "$1" && cd "$1"
}
```

You can configure the look of your prompt to add information about projects that you are working on. Things like Git status, the version of python a project uses…

**Reading shell configuration files**

After changing your shell configuration files, you need to source them so that the changes are applied. There are two common ways to do this when you are using the bash shell.

```bash
source ~/.profile             # Uses the builtin "source" command
. ~/.bash_profile             # Uses a dot
```

[Bash Startup Files (Bash Reference Manual)](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html)

## Intro to shell scripting

### The shebang

The shebang is the first line of a shell script, it tells the shell which interpreter to use when the script is executed. Another way to think of this is instead of running python scripts like `python main.py` you could just do `main.py` Instead of you specifying the interpreter in the command you are specifying the interpreter in the file.

There are two ways that we can write a shebang:

using an absolute path

```bash
#!/bin/bash
printf "hello world\n"
```

using the env utility

```python
#!/usr/bin/env python3
print("hello world")
```

- The shebang isn’t just for bash, you can use it to specify any interpreter.
- The shebang **must** be the first line in a file. In Bash and Python hashes ‘#’ represent a comment, so on subsequent lines this will be a comment, instead of a shebang.

### Hello World in Bash

> The only way to learn a new programming language is by writing programs in it. The first program to write is the same for all languages:  *print the words `hello world`*
 -- Brian Kernighan and Dennis Ritchie, *The C Programming Language*.
> 

To get started, created a new ‘Documents’ directory with a new ‘week5’ directory inside of it.

`mkdir -p Documents/week5` and cd into the new week5 directory.

Save the code below into a new file “hw” and make it executable `chmod u+x hw`

Now run the file like this `./hw` You need to include the `./` because this file is not in your path, so you need to provide a path. `./` is the relative path to the file, if you are in the same directory as the hw file.

```bash
#!/bin/bash
#: Title       : hw
#: Date        : Oct 20 2022
#: Author      : Nathan McNinch
#: Version     : 1.0
#: Description : print Hello, World!
#: Options     : None

printf "%s\n" "Hello, World!"
```

### Hello <variable>

Let’s change the above example so that we can pass a name to our script when we run it and say hello to that person.

```bash
#!/bin/bash
#: Title       : hw
#: Date        : Oct 20 2022
#: Author      : Nathan McNinch
#: Version     : 1.0
#: Description : print Hello, World!
#: Options     : None

NAME=$1

printf "%s\n" "Hello, ${NAME}!"
```

**Variables**

Variables can be declared in bash like this: 🔻

```bash
ANIMAL=cat
```

Notice how there is no white space between the variable name “ANIMAL” and variable value “cat”.

Why do you think this is?

Using uppercase is a convention, but not required. 

**Positional parameters**

`$1` is a “positional parameter”  These access arguments passed into the script when you run it.

If we save the code below into a new file, “print-params”  make that file executable and run it like this `./print-params apple two` What is printed? What is $0?

```bash
#!/bin/bash

echo $0 $1 $2
```

| Special Variable | Description |
| --- | --- |
| $0 | The name of the bash script. |
| $1, $2...$n | The bash script arguments. |
| $$ | The process id of the current shell. |
| $# | The total number of arguments passed to the script. |
| $@ | The value of all the arguments passed to the script. |
| $? | The exit status of the last executed command. |
| $! | The process id of the last executed command. |

[Bash Variables (Bash Reference Manual)](https://www.gnu.org/software/bash/manual/html_node/Bash-Variables.html)

[Bash Reference Manual](https://www.gnu.org/software/bash/manual/html_node/index.html#SEC_Contents)

The Google Bash style guide. 

[styleguide](https://google.github.io/styleguide/shellguide.html)

## Midterm Exam Notes

**Exam Time** 

2 hours

**Exam Breakdown**

- Quiz 20%
- Lab 80%

**What should you study**

Everything. Anything that has appeared in the notes, readings… could be on the midterm.

**How should you study**

- Review your notes
- Make up quiz questions based on the labs and quiz each other. (you can do this online too)
- Review the readings
- Review the labs (the lab component will be similar to a class lab
    - If you missed a lab question, don’t feel like you fully understand something, ask questions.
- I will provide you with a sample quiz (at least one of these questions will be on the exam)

**What do you need**

- A VM running Ubuntu 22.10
    - Vagrant or DigitalOcean, unless you have discussed it with me first, and I have approved your plan C (this is just to limit troubleshooting for multiple operating systems/virtualization tools/…)
    - You may want to have a backup VM. In the event that something goes wrong, (it probably won’t).
- A pen

**Good luck!**

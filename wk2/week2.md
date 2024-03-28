# Week 2 Jan 16

## Learning Outcomes and Topics

This week:
- explain reasons why you want(need) to use the command line in 2023
- explain how commands are executed in a shell
- learn to use a few commands that will help you navigate in a command line environment
  - cd
  - pwd
  - ls
- learn how to use the `man` utility
- learn how to read man pages
  

Next week:
- The Linux file system
- Finding files with `find` and `grep`
- command line text editors:
  - vi(m)
  - nano
  - helix

## Why are we still using the command line?

The reading from last week (everyone did the reading, right) provides a few good reasons to use the command line in 2023.

- Servers, and other minimal Linux configurations, where a GUI is:
  - unnecessary
  - adds complexity and might introduce additional vulnerability

Personally, I think the best reason to use the command line still is that it is really powerful. 
When you get even a little more comfortable with the command line you will find that you are able to achieve a lot of tasks faster than in a GUI.

One reason for this is that commands are a programming language. 
The shell is a REPL. It is possible to combine commands that generate data, with commands that filter data.

For example, man pages(more them later). 
If I wanted to search for a man page that allows me to change a user's password I could use `man -k password`. 
The information is in there, but it is a lot of data to sort through. So I add a filter with `grep` `man -k password | grep change`. 
This command returns a lot less data. Now I can open the man page, read how to use `passwd` and change a user's password.
All from the same tool!

Working in a text based interface can be tricky at first, and it takes practice. However, it is totally worth it. So stick to it.

## Getting around in the command line

Everyone probably knows the `cd` command. An interesting note about `cd` is that it is a shell built-in. 
Unlike `grep` which is a separate utility, `cd` is actually part of bash. 
You can find out about other shell built-ins with `help` if you want to see help for `cd` `help cd`.

`cd` is change directory, we can use it to change the location we are running commands in. 

*What does `cd ..` do?*

**`ls`**

The `ls` utility lists the contents of your current working directory.

On Linux OSs, and Mac and Windows, "hidden" files are created by starting the file name with a '.'. 
For example ".bashrc". Generally these are hidden so that an average computer user doesn't do anything to them. 
They are generally used to configure software and services on your device. 

You can see the hidden files with `ls -a`.

In addition to showing you a list of files, `ls` can also show you information about those files. 
`ls -l` "-l" is "long listing". This shows you file permissions, ownership, the date the file was last edited and the files size. 
You can combine l and a `ls -ahl`. *What do you think -h does? How could you find out?* 

```
-rw-r--r-- 1 riversng river 1150 Sep 26 19:51 filename
|[-][-][-]-  [------] [---]
| |  |  | |     |       |
| |  |  | |     |       +------------> 7. Group
| |  |  | |     +--------------------> 6. Owner
| |  |  | +--------------------------> 5. Alternate Access Method
| |  |  +----------------------------> 4. Others Permissions
| |  +-------------------------------> 3. Group Permissions
| +----------------------------------> 2. Owner Permissions
+------------------------------------> 1. File Type
```

**Your prompt**

Most of the time when you connect to a remote server, or open a terminal from a desktop you will be in the home directory of the user you are logged in as.

You will likely see something that looks a little like this`username@hostname ~$` (this will be slightly different depending on the operating system that you are using. This little string is your shells prompt. The shells prompt usually provides you with some handy information. 

`username` is your user name, the user who is currently running commands in the shell. You can change users in the shell.

`hostname` the hostname of the machine you are connected to. 

The tilde character `~` is shorthand for the users home directory `~ = /home/vagrant` this is the directory where commands will be run. So if you run the command `mkdir week2` you will be creating a new directory in `/home/vagrant` 

The `$` is commonly used to represent a regular user, but it has no special functionality, some shells use a `%` as the default prompt character.

The `#` is likewise often used to represent root.

The information in your prompt is configurable, you can change it to provide more or less information. We will look at some basic configuration later on.

`pwd` this will print the name of the current working directory, where you are in your file path.

**Important**

“Your home” directory and “the home” directory are not the same thing.

Your home refers to `/home/user-name` 

The home directory is just `/home` this is where all of the users home directories are. Unless you are creating or removing a user you generally don’t run any commands from `/home` 


## A few special characters

We are going to see a lot of "special characters" in the command line. I might start a list in your notes.

The pipe character `|` will "pipe" output from one command into another command. Like filtering the man page search above.

The double ampersand `&&` `sudo apt update && sudo apt upgrade`. 
This command is actually two commands, the second will run only if the first succeeds. If the first returns an error the second command won't run.

`mkdir` this will make a directory `mkdir new-dir` `mkdir -p new-dir/other-new-dir`. *What do you think the second command does?*

## Options, or Flags

These are additional (optional) information that we can pass to a command. 
Some options take arguments, others do not. Options that don't take arguments can be written like this `ls -alh`

## Practice tips

In addition to just trying to use the command line more (try not using your file finder for a day.), try incorporating two new things to your command line skill set every week. 
This week, try these two things:
1. Use tab more to complete commands
2. Try using ctrl + u(delete command entered so far) and ctrl + w(delete a word) instead of backspace when you make a typo.

[keyboard shortcuts](https://www.computerhope.com/ushort.htm)

[Red Hat 10 terminal shortcuts for Linux](https://www.redhat.com/sysadmin/top-10-shortcuts)

## Getting help in the command line.

Earlier you saw `help` however the main tool used to learn more about commands is `man`. Short for manual.

### Pagers

When you open a man page you are opening the manual entry in a pager, `less`. 
A pager, shows you a page of info at a time.
- To find out how to use less press `h`
- To quit press `q`

### reading a man page

| Bold | type this exactly, this is often the command |
| --- | --- |
| [OPTION] | options or flags, ie -a |
| italic or underlined | Replace with appropriate argument |
| -a | -b | Options separated by pipe cannot be used together |
| … | Arguments followed by an ellipses can be repeated |
| <mandatory> | Mandatory argument, usually found in option descriptions |
| {yes, no} | Limited options, only those specified will work |

**Headings of a man page**

Not all of these headings will be in every man page

- **Name:** The name of the command the man page is describing.
- **Synopsis:** A summary of the command and its syntax.
- **Configuration:** Configuration details for a device.
- **Description:** An explanation of what the program does.
- **Options:** A description of the command-line options the command accepts.
- **Exit Status:** Possible exit status values for the command, and what might cause them to be used.
- **Return Value:** If the man page is for a library
routine, this describes the value the library routine can send back to
the function that called that routine.
- **Errors:** A list of the values that might be placed in `errno` [in the event of an error](http://man7.org/linux/man-pages/man3/errno.3.html).
- **Environment:** A list of the environment variables that affect the command or program, and in what way.
- **Files:** A list of the files the command or program uses, such as configuration files.
- **Attributes:** A summary of various attributes of the command.
- **Versions:** Details of the Linux kernel or library
versions where a system call or library function first appeared or
changed significantly from previous versions.
- **Conforming to:** A description of any standards with which the command might comply, such as [POSIX](https://en.wikipedia.org/wiki/POSIX).
- **Notes:** Miscellaneous notes.
- **Bugs:** Known issues.
- **Examples:** One or more examples demonstrating the use of the command.
- **Authors:** The people who wrote or maintain the command.
- **See also:** Recommended reading related to the command or topic.

**Additional Resources**  

[Arch wiki man page](https://wiki.archlinux.org/title/Man_page)

**Bookmark this tool** [explain shell](https://explainshell.com/https://explainshell.com/)
 

## Reading

**Reading Questions**
- How would you describe the Filesystem Hierarchy Standard?
- What is a Pseudo Filesystem
- What is the difference between /usr/bin and /bin
  - What is the historical difference
- Where are system level configuration files
- How do open a new file in Vim
- How do you save a file in Vim
- How do you search for a file in Vim

[Learning Modern Linux Ch 5. Filesystems](https://learning.oreilly.com/library/view/learning-modern-linux/9781098108939/ch05.html)

[Learning vi and Vim editors, 8th ED, Ch 1. Introducing vi and Vim](https://learning.oreilly.com/library/view/learning-the-vi/9781492078791/ch01.html)
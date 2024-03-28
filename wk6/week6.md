# Week 6 2420

## Learning Outcomes and Topics

This week:
- Quick midterm review
- Shell scripting what and why?
- Write conditionals and tests in bash
	- test to see if files and directories exist
- Loops in bash
	- Write for loops to loop over arguments passed to a script
- Functions in bash
	- write a bash function that accepts arguments
- exit status
	- use exit status to return none zero status on error
- Look at Assignment 1

Next week:
- More shell scripting
- case statments
- handling options with `getopts`
- Intro to AWK

## Midterm review

`/etc/passwd` vs `/etc/shadow`

Using `grep` (or `awk`) to find all the regular users on a Linux system, with the help of the `/etc/passwd` file

Figuring out how to do this involves 2 things
1. knowing how to use range in a regular express, `man grep`
2. Looking at your data source, and paying attention to the field separator ":"

```bash
# grep solution, this makes use of a range that is "good enough"
# You probably won't have more than 8999 users on a system
grep -E ':[1-9][0-9]{3}:' /etc/passwd
	  
# awk solution, awk is made for stuff like this
awk -F: ' $3 >= 1000 && $3 != 65534 {print} ' /etc/passwd
```

Why is it important that I don't use `sudo` or the root user when I don't need to?
Two reasons:
1. In addition to security there are other problems that can be caused by running a command with `sudo`. Commands run with `sudo` are run as the root user, this might make files that should belong to your regular user belong to the root user 
2. For better or worse, part of school assignments is demonstrating your understanding. Permissions are an essential part of Linux OSes. Using `sudo` when you don't need to demonstrates a lack of understanding, and therefore can result in you not getting full marks. The first question on the midterm, for example.

Why is it important that I follow the naming conventions in the assignment instructions?
Two reasons for this one too:
1. File names help me find your files. I have a little over 100 students this term, most weeks everyone submits 2 things (lab and quiz). That is a lot of assignments over the course of the term. I don't want to lose any of your work, or mix it up with one of your classmates.
2. File names matter in software development and DevOps. As well as when you are working in a team. If you don't pay attention to these things, code breaks and significan things get lost.

## Scripting review

	- What is this -> `#!/bin/bash`
	- What is this -> `$1`
	- What is wrong with this variable declaration -> `NAME = Art`

## Shell scripting what and why

You will often see shell scripting described as a “glue language”. This is because a lot of shell scripts exist to “glue” together existing commands. A perfectly useful shell script might just be a long command that makes use of two utilities. Something like getting some data about running processes and filtering that data with grep. If you are performing a task that requires you to run these commands frequently, it might make sense to put them in a shell script.

bash is also good at performing small file operations. Checking if a file exists, appending data to a log file… That sort of thing.

Generally, shell scripts are on the shorter side of computer programs. Although, there is no real upper limit to how long your scripts should be.

The syntax of the bash language is very simple. So learning to write useful scripts in bash is usually pretty quick. You can write “useful” programs in bash, much faster than you can write “useful” programs in many other languages.

Shell scripts are still an important part of the Linux ecosystem, and as we have seen Linux is everywhere, so learning a little shell scripting is a good idea.

References:
[Bash Reference Manual (the official bash manual)](https://www.gnu.org/software/bash/manual/bash.html)

## Where should your shell scripts go

There isn't one standard location. Shell scripts might relate to a specific project, in which case they may be in the directory structure of that project.

There is however a common convention of putting personal shell scripts in a local bin directory, ie `$HOME/bin`. This is so common that some distros have a conditional in their default shell configuration that will add bin to your path if it exists. You can also put other binaries that you might use here. I have the `terraform` binary in `~/bin`. We are going to use this convention for our shell scripts.

So, if you haven't already, create a new `bin` directory in your user's home directory. Next, open up your `.bash_profile` or `.profile` file in vim and check to see if there is already a conditional that adds bin to your path. Search for 'bin' in vim. If this doesn't exist, you can add one by copying the following code block.
```bash
if [ -d $HOME/bin ]; then
	PATH="$HOME/bin:$PATH"
fi
```

## printf and echo

echo and printf can both be used to write output from a script to stdout.

both echo and printf are shell builtins, they are part of the bash program.

echo is generally fine for simple print operations. If you need a little more formatting printf is preferable. For example:
```bash
# try these out
printf "%20s %d\\n" ted 56
printf "%-20s %d\\n" kim 32
printf "%.3f" 1.61803398
```

### Type conversion specifier

The type conversion `specifier` is a character that specifies how to interpret the corresponding argument. This character is required, and it is placed after the optional fields.
			- Below is a list showing all type conversions and what they do:
				- `%b` - Print the argument while expanding backslash escape sequences.
				- `%q` - Print the argument shell-quoted, reusable as input.
				- `%d`, `%i` - Print the argument as a signed decimal integer.
				- `%u` - Print the argument as an unsigned decimal integer.
				- `%o` - Print the argument as an unsigned octal integer.
				- `%x`, `%X` - Print the argument as an unsigned hexadecimal integer. `%x` prints lower-case letters and `%X` prints upper-case.
				- `%e`, `%E` - Print the argument as a floating-point number in exponential notation. `%e` prints lower-case letters and `%E` prints upper-case.
				- `%a`, `%A` - Print the argument as a floating-point number in hexadecimal fractional notation. `%a` prints lower-case letters and `%A` prints upper-case.
				- `%g`, `%G` - Print the argument as a floating-point number in normal or exponential notation, whichever is more appropriate for the given value and precision. `%g` prints lower-case letters and `%G` prints upper-case.
				- `%c` - Print the argument as a single character.
				- `%f` - Print the argument as a floating-point number.
				- `%s` - Print the argument as a string.
				- `%%` - Print a literal `%` symbol.

References:
[Red Hat Linux command basics: printf](https://www.redhat.com/sysadmin/command-basics-printf) 

## If conditionals and tests

`test` and `[]` are different syntax, but evaluate the same expressions to the same value. [] is just a more convenient way to write a condition, so you will see it more often.

More commonly, 'test' refers to the built-in tests, '-f' for file in the example below.
```bash
test -f ./file
[ -f ./file ] 
# remember that white space is important, you need space between the brackets
```

Use `help test` to see more info on tests.

**Examples**

```bash
# test to see if a directory exists
if [ -d "$1" ]; then
echo "directory exists"
fi
```

```bash
if [ condition ]; then
	your code
fi
```

**if else**
```bash
#!/bin/bash

if [ $(whoami) = 'root' ]; then
	echo "You are root"
else
	echo "You are not root"
exit 1
fi
```

**elif**
```bash
#!/bin/bash

# use read to get user input
read -p "Enter a number: " USER_NUM

if [[ $USER_NUM -gt 10 ]]; then
	echo "The variable is greater than 10."
elif [[ $USER_NUM -eq 10 ]]; then
	echo "The variable is equal to 10."
else
	echo "The variable is less than 10."
fi
```

What is the difference between `[` and `[[`

The short answer is that `[` is more portable (this will work in many other shells). While `[[` is more powerful (less prone to annoying errors) but it will work in less shells.
Since we are only concerned with  bash, my recommendation is to use `[[`

Single square brackets are a builtin, while double square brackets are considered a keyword.
references:
		- [Bash Conditional Expressions](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)
		- [Conditional Constructs](https://www.gnu.org/software/bash/manual/html_node/Conditional-Constructs.html)

## Loops

Bash has several loops: `until` `while` and `for` as well as `select` Which you can use to “select” options from a menu.

For now, we are just going to look at *for* loops.

Example with a range

```bash
for num in {1..10}; do
	echo "$num"
done
```
	
	basic syntax
```bash
for item in [LIST]; do
	[COMMANDS]
done
```

bash also has c style for loops, notice the `((` evaluates and expression, use this when you are doing some math in bash, try to avoid doing math in bash.
```bash
for ((i=0; i<10; i++)); do
	printf '%d\n' "$i"
done
```

looping over an array, [@] = all elements in an array
```bash
#!/bin/bash

name_list=("cassandra" "j'onn" "Tim")

for person in ${name_list[@]}; do
  echo $person
done
```

infinite loop in for
```bash
for ((;;)); do
	printf 'forever'
done
```

Bash includes built-ins `break` and `continue`. They behave similarly to the way they do in other languages:

**Break**
> Exit from a `for`, `while`, `until`, or `select` loop. If n is supplied, the nth enclosing loop is exited. n must be greater than or equal to 1. The return status is zero unless n is not greater than or equal to 1.
>  -- Bash reference manual

**Continue**
> Resume the next iteration of an enclosing `for`, `while`, `until`, or `select` loop. If n is supplied, the execution of the nth enclosing loop is resumed. n must be greater than or equal to 1. The return status is zero unless n is not greater than or equal to 1.
> -- Bash reference manual

References:
	- [Looping Constructs](https://www.gnu.org/software/bash/manual/html_node/Looping-Constructs.html)
	- [Bash Arrays](https://www.gnu.org/software/bash/manual/bash.html#Arrays)

## Functions

You can write functions in Bash too.
> A shell function is a compound command that has been given a name. It stores a series of commands for later execution. The name becomes a command in its own right and can be used in the same way as any other command. 
> [-- Pro Bash Programming, ch 6](https://learning.oreilly.com/library/view/pro-bash-programming/9781484201213/9781484201220_Ch06.xhtml#Sec1)
> 
```bash
# this is the more common syntax for bash functions
function_name() {
	commands
}

# this syntax also works
function function_name() {
	commands
}
```

The `return` keyword

What do you think the code below will return
```bash
#!/bin/bash

bash-rtrn() {
	return 42
}

bash-rtrn
```

**Returning values from a function**

```bash
#!/bin/bash

my_function () {
	local func_result="some result"
	echo "$func_result"
}

func_result="$(my_function)"
echo $func_result

```
The `local` keyword makes a variable local to the functions scope, by default variables are global.

This `$()` is command substitution. This allows you to run a command, return the value of that command and store that in a variable. `$(find -name $1)`

**Function parameters**

Function parameters are a little different in bash as well.
```bash
#!/bin/bash

greeting () {
	echo "Hello $1"
}

greeting "Joe"
```

Using a function and a for loop to print all of the arguments to a script

```bash
#!/bin/bash

# This will print arguments to the function
function Listem() {
  for arg; do
    echo "arg to func: '$arg'"
  done
  echo "Inside func: \$0 is still: '$0'"
}

# This passes all positional parameters to the Listem function
Listem $@
```

References:
		- [Shell Functions](https://www.gnu.org/software/bash/manual/bash.html#Shell-Functions)

## Exit status and the `exit` command

The 'exit status' of a command indicates whether a command succeeded or not. In general an exit status of `0` indicates success and non-zero exit status indicates an error

You can check the exit status of the previous command with `echo $?`

exit codes fall between 0 and 255

examples:
If a command is not found, the child process created to execute it returns a status of 127.
If a command is found  but is not executable, the return status is 126.

The `exit` command will exit out of the current process and return an exit status which can be provided as an argument  `exit 1`. You have been using `exit` all term

When writing your own scripts you really only need to use 0 and 1
```bash
#!/bin/bash

if [[ "$(whoami)" != root ]]; then
	echo "This script should be run with sudo"
	exit 1
fi

echo "doing stuff..."

exit 0
```

References:
		- [Exit Status](https://www.gnu.org/software/bash/manual/html_node/Exit-Status.html)

## Useful bash snippets and tips

Print the name of your program this might be helpful in your help message or error messages

```bash
PROGRAM=${0##*/} # you can also use the basename command
echo $PROGRAM
```

debugging

You can write tests for bash, and there are testing frameworks. Tests are a little less common in bash because you can write a useful bash script that is just one line of code.

Because the scripts that you are going to write initially are pretty simple, I recommend using `set -x` Just add this to your script below the shebang. This "sets" xtrace to on.

[set docs](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)

[Bash scripting cheatsheet](https://devhints.io/bash) This resource contains a number of simple examples.

## Reading

Read Ch 8, Arguing from [bash Idioms](https://learning.oreilly.com/library/view/bash-idioms/9781492094746/ch08.html) By Carl Albing and JP Vossen

You may also want to read *Ch 3, Just In Case*,  which is about writing case statements.


## Learning outcomes and topics
This week:
- `case`
- `getopts`
	- Use case statements and getopts to write shell scripts with options
- use `shift` to change position of positional parameters
- Intro to AWK
	- Use awk to process text files
- More debugging
	- PS4
- Transfering your scripts from your VM to your host, so you can submit them
	- Vagrant, shared directory
	- DigitalOcean, use `sftp`
Next week:
- init 
- systemd
- systemctl
- cloud service providers

## case statements

```bash
#!/bin/bash

read -p "Enter a character " letter

case "$letter" in
  A)
    echo "You entered A"
    ;;
  B|b)
    echo "You entered B or b"
    ;;
  C)
    echo "You entered C"
    ;;
  [1-3])
    echo "You entered 1 2 or 3"
    ;;
  *)
    echo "You did not enter A, B, b, or C or 1, 2, or 3"
    ;;
esac
```

```
* Matches any string of characters. Use for the default case. 

? Matches any single character. Use this for the default character in getopts, where you are passing a single letter as a flag 

[...] Defines a character class. Any characters enclosed within brackets are tried, one at a time, in an attempt to match a single character. 

A hyphen between two characters specifies a range of characters. ex [123] and 
[1-3] will both match 1, 2, or 3 if you wanted to match a 2 digit number you could use [][] 

| Separates alternative choices that satisfy a particular branch of the case structure.
```


## `shift` positional parameters

`shift` will; shift positional parameters *n* characters to the left. The default is 1 character.

Save the file below and execute it like so `shift-examp 1 2 3 4`
the first line will print `1 2 3`
the second `2 3 4` shifted 1 position to the left
and the third `4` shifted 2 additional positions to the left 
```bash
#!/bin/bash

echo $1 $2 $3

shift

echo $1 $2 $3

shift 2

echo $1 $2 $3
```

## handling options with `getopts`

getopts is a a shell builtin that can be used to aide you in handling options in your shell scripts.

let's start in the obvious place, read the help `help getopts` since getopts is a builtin this will display the help message.

getopts is a function that takes three arguments:

-   valid options that will be handled, ie `“a:b”` in the example below
-   a variable populated with the option arguments ie `opt` in the example below
-   a list of options and arguments to be processed `$@`

The first colon in the options string below tells getopts to run in silent error checking mode. Or let the script define errors.

```bash
#!/bin/bash

vara=""
varb=""

while getopts ":a:b:" opt; do
  case "${opt}" in
    a)
      vara=${OPTARG}
      ;;
    b)
      varb=${OPTARG}
      ;;
    :)
      echo "Error: -${OPTARG} requires an argument"
      exit 1
      ;;
    ?)
      exit 1
      ;;
  esac
done

if [[ $# -lt 1 ]]; then
  echo "you need to provide an option -a and or -b with an argument"
fi

if [[ -n $vara ]]; then
  echo "$vara"
fi

if [[ -n $varb ]]; then
  echo "$varb"
fi
```

**OPTARG**

When a flag is set to expect an argument, the argument for that flag is held in the OPTARG variable. ie `my_script -n "ted"` the OPTARG for -n is the string ted.

**OPTIND**

When dealing with a mix of getopts and positional arguments, **the flags and flag options should always be provided before the positional arguments!** This is because we want to parse and handle all flags and flag arguments before we get to the positional parameters.

The line below shifts the positional parameters to remove any options handled by getopts.

```bash
shift $(($OPTIND -1))
```

This also works if your options accept arguments. Try running the previous example with an additional positional parameter.

```bash
#!/bin/bash

vara=""
varb=""

while getopts ":a:b:" opt; do
  case "${opt}" in
    a)
      vara=${OPTARG}
      ;;
    b)
      varb=${OPTARG}
      ;;
    :)
      echo "Error: -${OPTARG} requires an argument"
      exit 1
      ;;
    "?")
      exit 1
      ;;
  esac
done

shift $((OPTIND - 1))
echo "$1"

if [[ $# -lt 1 ]]; then
  echo "you need to provide an option -a and or -b with an argument"
fi

if [[ $vara != "" ]]; then
  echo "$vara"
fi

if [[ $varb != "" ]]; then
  echo "$varb"
fi
```

### Example script, Generate a random password

```bash
#!/bin/bash

#: Title       : rndpswd
#: Version     : 1.0
#: Description : generate a random password
#: Options

# Users can set a password length -l
# Users can add a special character -s
# Users can set vrbose mode with -v

#######################################
# Print a Usage message.
# Arguments:
#   None
# Outputs:
#   Write a help message
#######################################
usage() {
  echo "Usage: ${0} [-vs] [-l LENGTH]" >&2
  echo 'Generate a random password.'
  echo ' -l LENGTH specify the password length.'
  echo ' -s Append a special character to the password.'
  echo ' -v display output in verbose mode'
  exit 1
}

#######################################
# log additional messages
# locals:
#   MESSAGE
# Arguments:
#   message to log
# Outputs:
#   log message passed as argument
#######################################
log() {
  local MESSAGE="${@}"
  if [[ "${VERBOSE}" = 'true' ]]; then
    echo "${MESSAGE}"
  fi
}

# set a default password length
LENGTH=48

while getopts :vl:s OPTION; do
  case ${OPTION} in
    v)
      VERBOSE='true'
      log 'Verbose mode on.'
      ;;
    l)
      LENGTH="${OPTARG}"
      ;;
    s)
      USE_SPECIAL_CHARACTER='true'
      ;;
    ?)
      usage
      ;;
  esac
done

log 'Generating a password'

PASSWORD=$(date +%s%N${RANDOM}${RANDOM} | sha256sum | head -c${LENGTH})

#Append a special character
if [[ "${USE_SPECIAL_CHARACTER}" = 'true' ]]; then
  log 'Selecting a random special character.'
  SPECIAL_CHARACTER=$(echo '!@#$%^&*()-=+' | fold -w1 | shuf | head -c1)
  PASSWORD="${PASSWORD}${SPECIAL_CHARACTER}"
fi

log 'Done'
log 'Here is your password'

#display password
echo "${PASSWORD}"
exit 0
```

**General conventions to help make getopts code more readable**

**use variables:**

Create variables and assign them values in your case statement instead of using if conditions nested in your case statement. See OPTARG above

**use functions:**

If you are writing something like a usage or help message, instead of writing four lines in your case statement, write a function and call the function in the case statement.

There isn’t a specific number of lines at which you should break out logic into a function, but generally if you need to write more than 3 lines in a case match in getopts you should put it in a function to make it a little cleaner.

**getopt** note

`getopts` and `getopt` both exist, you want to use `getopts`, not `getopt`. 

`getopt` is an external utility which you would need to install.

-   `getopt` does not handle empty flag arguments well; `getopts` does
-   `getopts` is included in the Bourne shell and Bash; `getopt` needs to be installed separately
-   `getopt` allows for the parsing of long options ( `--help` instead of `-h` ); `getopts` does not
	- Although as you saw in the text book you can hack your way into -- options
-   `getopts` has a simpler syntax; `getopt` is more complicated (mainly because it is an external program and not a builtin)

## Configure `PS4`

Bash has several prompts, the one you are most familiar with is PS1. This is the prompt where you enter commands. On most systems, the default looks a little like this: `user@host`.

PS1 one is set with a command like this: `export PS1="u@h w$ "` in your shells configuration.

PS2 is the prompt you see when you extend a command to the second line. The *secondary prompt*.

PS3 is the prompt that you see if you use a select statement. The *select prompt*.

PS4 is the prompt that you see when you set xtrace on with `set -x`
The default value is '+'. Multiple '+' are printed to denote nesting.

So with the following script
```bash
#!/bin/bash
set -x
VAR=one

echo '${VAR}'
echo "${VAR}"
```

This would be printed when run.
Lines that start with + are lines in your script, lines without are the output of commands that print to stdout.
```
+ VAR=one
+ echo '${VAR}'
${VAR}
+ echo one
one
```

Sometimes it is helpful to know which line in your script is being printed. To do this you can set your PS4 prompt, you could also add this to your shell config to make it permanent.

```bash
#!/bin/bash

# show line numbers in PS4
PS4='$LINENO+ '
# turn on xtrace for debugging
set -x

# declare a variable, VAR
VAR=one

# compare '' and ""
echo '${VAR}'
echo "${VAR}"
```

From the bash reference manual

>The value of this parameter is expanded like `PS1` and the expanded value is the prompt printed before the command line is echoed when the -x option is set (see [The Set Builtin](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#The-Set-Builtin)). The first character of the expanded value is replicated multiple times, as necessary, to indicate multiple levels of indirection. The default is ‘+ ’.
> -- [bash reference manual](https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#index-PS4)

Reference:
[bash cookbook Using Secondary Prompts: ps2, ps3, ps4](https://learning.oreilly.com/library/view/bash-cookbook/0596526784/ch16s10.html)


## Intro to AWK

> The name `awk` comes from the initials of its designers: Alfred V. Aho, Peter J. Weinberger, and Brian W. Kernighan. The original version of `awk` was written in 1977 at AT&T Bell Laboratories. 
> -- Effective awk Programming, 4th ED

Today, you are probably using gawk, gnu awk, which is maintained by Arnold Robbins.

Programs in awk are data driven, an awk program is made of rules, each rule is a pattern that you want to find in that data and an action that you want to perform on that data.

`pattern { action }`

Either the pattern or the action can be omitted, but not both. If the pattern is omitted, all lines will match. If the action is omitted, all lines that match the pattern will be printed.

An awk program can be a single line, run directly from the command line.

`awk 'pattern' { action } input-file`

An awk program can also be written in a file and run, similar to any interpreted language.

`awk -f program-file input-file`

And you can even write an awk file that begins with a shebang

```awk
#!/bin/awk -f

BEGIN { print "Don't Panic!" }
```

The BEGIN statement is used to perform any actions, before processing data from the input file. There is a corresponding END statement which can be used to perform actions after processing any input files.

Sample data from the gawk user's guide, We will look at some examples that use this data
```
Amelia       555-5553     amelia.zodiacusque@gmail.com    F
Anthony      555-3412     anthony.asserturo@hotmail.com   A
Becky        555-7685     becky.algebrarum@gmail.com      A
Bill         555-1675     bill.drowning@hotmail.com       A
Broderick    555-0542     broderick.aliquotiens@yahoo.com R
Camilla      555-2912     camilla.infusarum@skynet.be     R
Fabius       555-1234     fabius.undevicesimus@ucb.edu    F
Julie        555-6699     julie.perscrutabor@skeeve.com   F
Martin       555-6480     martin.codicibus@hotmail.com    A
Samuel       555-3430     samuel.lanceolis@shu.edu        A
Jean-Paul    555-2127     jeanpaul.campanorum@nyu.edu     R
```

print the total kilobytes of files in a directory
`ls -l | awk '{ x += $5 } END { print "total K-bytes:", x / 1024 }'`

print the total bytes of all the files that were last edited in November
`ls -l | awk '$6 == "Nov" { sum += $5 } END { print sum }'`

Although awk is a language, and not a utility, awk can be run similar to a utility and even includes a few options. One of these is the field separator, ''-F".

An awk program operates on data in fields and records. By default, white space is the field separator used. Here is an example from last weeks notes that sets the field separator

`awk -F ":" ' $3 >= 1000 && $3 != 65534 {print} ' /etc/passwd`

references:
- [GAWK User's Guid](https://www.gnu.org/software/gawk/manual/gawk.html#SEC_Contents)

## Transferring files from your VM to your host

You will need to write and test your scripts in your VM. You should write them in Vim for the practice. 

### Vagrant and VirtualBox

In week one I mentioned that one of the reasons that we are using Vagrant is to introduce the concept of configuration as code. This allows us to write a configuration file once and apply that configuration more quickly than we could going through the GUI.

One of the things that we can configure is a shared directory, a directory that is accessible from your host and your VM. So you can put a file in your shared directory in your VM and send me that file using D2L.

There are other ways that you can share files over a network, sftp and rsync, for example. These require you to run a command every time you want to share a file. A shared directory on the other hand behaves just like a regular directory.

If you followed along with the Vagrant setup video you have a Vagrantfile that contains several example configurations commented out, along with a description. The shared folder section looks like this🔻

```ruby
# Share an additional folder to the guest VM. The first argument is
# the path on the host to the actual folder. The second argument is
# the path on the guest to mount the folder. And the optional third
# argument is a set of non-required options.
# config.vm.synced_folder "../data", "/vagrant_data"
```

The last line in that code block above is the actual code that sets the shared directory.

In the same directory where your Vagrantfile is create a new **data** directory. This is on your host machine, Windows, NOT in your VM.

This data directory is “./data” in the config below. It is relative to your project root, which is also where your Vagrantfile is.

“/home/vagrant/data” is (hopefully obviously) on your VM, in your vagrant users home directory. You don’t have to manually create this directory, it will automatically be created the next time you start your VM. You can also use `vagrant reload` from your host.

```ruby
# Share an additional folder to the guest VM. The first argument is
# the path on the host to the actual folder. The second argument is
# the path on the guest to mount the folder. And the optional third
# argument is a set of non-required options.
config.vm.synced_folder "./data", "/home/vagrant/data"
```

That’s it. Now if you run the commands `vagrant destroy` and `vagrant up` you will still have this directory on your host and any files will still be there. They will also be on your new VM😎

### DigitalOcean

If you are using DigitalOcean from Windows, the easiest way to do the following is to setup WSL first. Let me know if you need to do this

For anyone running macOS you will have `rsync` and `sftp` already installed.

`sftp` uses openssh to connect to a remote server, and you connect the same way you connect to a server using ssh. `sftp user-name@server-ip`

Once you are connected, your prompt will look like this `sftp> `. From here, you can type `help` and press enter to get more info about what you can do with sftp. Since you want to copy the contents of ~/bin you can do `get -R bin` this will copy the contents of bin to the directory where you connected to your server, wheren you ran `sftp user-name@server-ip`

## Reading

[Chapter 12. Introduction to systemd Red Hat Enterprise Linux 9 | Red Hat Customer Portal](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/introduction-to-systemd_configuring-basic-system-settings)

[Chapter 13. Managing system services with systemctl Red Hat Enterprise Linux 9 | Red Hat Customer Portal](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/managing-system-services-with-systemctl_configuring-basic-system-settings#doc-wrapper)

[Chapter 14. Working with systemd targets Red Hat Enterprise Linux 9 | Red Hat Customer Portal](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/configuring_basic_system_settings/working-with-systemd-targets_configuring-basic-system-settings#doc-wrapper)

**Not** a reading requirement, however if you are curious about DigitalOcean you may want to look at their site.

[DigitalOcean - The developer cloud](https://www.digitalocean.com/)

## Todo

- [ ] Complete Assignment 1 due Friday March 03
- [ ] Reading, due before next weeks class 

## Assignment 1 Shell Scripting

**Due Date:** Friday March 03, 23:30

### Instructions

### Scripts, General:
This is an individual assignment. You can share resources with classmates, however everyone should submit their own, unique work. So don't share your code with other people, or ask anyone for their code.

If you need help, send me a message and tell me
- what you are trying to do
- what is happening now
- what you have tried to do to solve the problem so far

All of your scripts should include the following
- use the getopts builtin for all the options in your scripts. All of your scripts have at least one option '-h'
- use functions for organizing your code.
- include a usage, or help function which can be displayed with the `-h` option. This will describe how to use your script.
- scripts must use unix formatted line endings. I need to be able to run your scripts, if they have dos line endings bash will complain.
- use an error function similar to the example below to handle error messages. And return an exit code.

```bash
err() {
	echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $*" >&2
	exit 1
}  

# using the error function	
if ! do_something; then
	err "Unable to do_something"
fi
```

- comments that describe what your code does
- don’t forget to look at the Google style guide for some tips [styleguide](https://google.github.io/styleguide/shellguide.html)

### Script 1, tasks
This one is actually two scripts
**Script one:**
Write a script that allows a user to add a task to file.
Each task can have optional:
- due-by (add with -d date) 
	- date formatting is up to you. There are a number of ways that you could autopopulate dates, or change formatting (see bonus). To start with, I would just pick a simple format and stick with it. *Maybe* include instructions on formatting in your help function. 

- tag(s) (add with -t tag)
You will need to have some method for demarcating tags so that only matching tags are returned when searched for.

**Script two:**
Write a companion script that will allow a user to display
- all tasks (if no arguments are provided)
- tasks with a matching tag (search with -t tag)
	- only tags should be returned. If for example I have a tag "bcit" and a different task with the word "bcit" only the bcit tag is displayed, not both
- tasks with a matching due-by date (search with -d date)
- tasks should be displayed using a pager (like the man pages)

**Bonus** 2 points
add functionality to the search script so that a user can search for a range of dates. For example, all tasks due between today and Friday

**Bonus Bonus** 2 points
Write a third script to normalize dates. Use this third script in both of your tasks scripts. 
For example Jan 03 2023, 01-03-2023 and Jan 3 will all match a single date format. So if you have one format in your file, and search for a date with a different format, the correct results will be returned.


### Script 2, remove log
Write a script that will delete files and keep a log of any files deleted

Include an option to delete a file without writing to the log `-s` for silent

Write an alias so that your script is used instead of `rm` when you run the `rm` command.

Generally we would use journald for logs, since we haven't looked at this yet, or logging. Your "log" file is more of a history file. Like `.bash_history`. So keep your log file in the same location as your `.bash_history` file. `.remove_log` is a good name for this file.

Your script should create this file if it doesn't already exist.

This file should contain a line that indicates:
- the date
- user-name 
- files deleted

example of formatting `Tue Feb 21 01:27:53 UTC 2023: pond: test-file2`

### Script 3, print a file with line numbers and a header

Your script should take one or more files as arguments and print each file one line at a time, prepending a line number to each line.

Each file should start at line number 1

Before the start of each file, print a simple header, to help separate the files. Your header should include the following:
- file name
- file owner, the username of the file owner
- dash, or an empty line, to help make it clear where one file ends and another begins when you print more than one file.

You cannot use an existing utility for the file printing. **Just bash!**

### Grading

**per Script**
**5 points**
	- All requirements have been met
	- Script works as intended
	- Code is clean, efficient, properly formatted
**4 points**
	- Almost all requirements have been met
	- or script mostly works as intended
	- or code could be more efficient, cleaner...
**3 points**
	- Most of the requirements are included
	- and/or script doesn’t work as intended
	- and/or code could be more efficient, cleaner
**2 points**
	- Many requirements are not met
	- code needs a lot of work
**0 points**
	- Very little was done on the script
**Total: 20 points**

### Submission Instructions
Submit a "your_name_as1.zip" file that includes the following:
- You scripts, as individual plain text files
- A .pdf "your_name_as.pdf" that includes:
	- Screenshots that demonstrate that your scripts work as intended
	- Notes on how you performed tasks outside your scripts. For example: the remove log script, how did you add the alias to use your script instead of the `rm` command.
	- Notes or instructions that I may need to run your script.
	- I shouldn't need to create any files or directories before running your scripts
	- I must be able to run your scripts in a Linux environment.
		- I am going to be using DO and Ubuntu 22.10

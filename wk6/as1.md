# Assignment 1 Shell Scripting
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
- due-by (add with -d <date>) 
	- date formatting is up to you. There are a number of ways that you could autopopulate dates, or change formatting (see bonus). To start with, I would just pick a simple format and stick with it. *Maybe* include instructions on formatting in your help function. 

- tag(s) (add with -t <tag>)
You will need to have some method for demarcating tags so that only matching tags are returned when searched for.

**Script two:**
Write a companion script that will allow a user to display
- all tasks (if no arguments are provided)
- tasks with a matching tag (search with -t <tag>)
	- only tags should be returned. If for example I have a tag "bcit" and a different task with the word "bcit" only the bcit tag is displayed, not both
- tasks with a matching due-by date (search with -d <date>)
- tasks should be displayed using a pager (like the man pages)

**Bonus** 2 points
add functionality to the search script so that a user can search for a range of dates. For example all tasks due between today and Friday

**Bonus Bonus** 2 points
Write a third script to normalize dates. Use this third script in both of your tasks scriptsFor example Jan 03 2023, 01-03-2023 and Jan 3 will all match a single date format. So if you have one format in your file, and search for a date with a different format the correct results will be returned.


### Script 2, remove log
Write a script that will delete files and keep a log of any files deleted

Include an option to delete a file without writing to the log `-s` for silent

Write an alias so that your script is used instead of `rm` when you run the `rm` command.

Generally we would use journald for logs, since we haven't looked at this yet, or logging. Your "log" file is more of history file. Like `.bash_history`. So keep your log file in the same location as your `.bash_history` file. `.remove_log` is a good name for this file.

Your script should create this file if it doesn't already exist.

This file should contain a line that indicates:
- the date
- user-name 
- files deleted

example of formatting `Tue Feb 21 01:27:53 UTC 2023: pond: test-file2`

### Script 3, print a file with line numbers

Your script should take one or more files as arguments and print each file one line at a time, prepending a line number to each line.

Each file should start at line number 1

You cannot use an existing utility for this script. **Just bash!**

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

## Submission Instructions
Submit a "your_name_as1.zip" file that includes the following:
- You scripts, as individual plain text files
- A .pdf "your_name_as.pdf" that includes:
	- Screenshots that demonstrate that your scripts work as intended
	- Notes on how you performed tasks outside your scripts. For example: the remove log script, how did you add the alias to use your script instead of the `rm` command.
	- Notes or instructions that I may need to run your script.
	- I shouldn't need to create any files or directories before running your scripts
	- I must be able to run your scripts in a Linux environment.
		- I am going to be using DO and Ubuntu 22.10

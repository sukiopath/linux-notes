# Lab

**Due date:** To be completed in class, before the end of class.

Make sure that you work with your lab partners. If a team member is absent contact them and work with them remotely via discord, zoom, ravens...

**Don't divide up the work, work on each question together.** It is important that you all learn the material in every lab.

Lab must be completed as a regular user, not the root user.

**Instructions**

**Part one: 1 point**

Re-write your grep command from a couple of weeks ago so that the command writes the names of the files that contain the word “alias” to a new file `~/Documents/week5/files-alias`. If a previous file with that name exists your command should replace it. You will have to create the directories first.

Submit a screenshot of your command

**Part two: 3 points** 

Find the PID (process id) of a process with `ps`

- use the -e option and the -o option
- with the -o option display:
    - COMMAND
    - PID
- use another utility to filter your output to look for ‘ssh’

submit screenshots that show the following:

- your complete ps command, and the output
- the relevant sections in the man page that provide the correct arguments for the -o option.

Include a note that says what section of the man page this information is in. Bold text, left justified.

**Part three: 2 points**

Add an alias to your shell configuration file that changes the default behaviour of the `ls` command. Part one will tell you which file to edit.

Your new ls behaviour should include the following:

- all
- classify
- human-readable

The file you found in part one contains example aliases. Open the file in vim and search for the aliases, add your alias in the same section.

remember you can search for text in vim the same way you can in less (the pager that you use everytime you open a man page) `/search-term` 

submit a screenshot that includes your alias, along with the existing aliases 

**part four: 2 points**

Take your command from part 1 and add it to a bash script file. 

Save your file in `~/bin/lclsrch` lclsrch is the name of the file.

Your new script should allow you to do the following:

- pass the search word as a positional parameter when you run the script
- write to a new file, the name of new file should be `search-file-<search-word>` where ‘<search-word>’ will be the word you pass as a positional parameter. So if you searched for alias you filed would be named ‘search-file-alias’
- Write the new file to the same directory as part 1,
- search in your home directory

What to submit:

- copy your code into your document, make it clear that it is code, use a code block or make the font a monospaced font.
- add a note to your document that describes the steps needed to successfully run your new script.

**********************************************Submission Instructions**********************************************

Submit a .pdf using the dropbox on D2L

Title your pdf “your-group-lab4.pdf” ie "group1-lab3.pdf”

- File must be a .pdf.
- Only one submission per team
- Include all contributing team members names in the file, at the start of the file so it is easy to find.

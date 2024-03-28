# Lab

******************Due Date****************** Friday Jan 27, 23:30

************************Instructions************************

Please submit your answers in order.

DigitalOcean note: Before completing this week's lab you will need to follow along with the video I created on working with DigitalOcean. Specifically you need to complete this weeks lab as a regular user, not the root user. So before doing the lab follow along with the video on D2L, in the content section.

**Part 1 2 points**

Two directories are described below. For each, cd into the directory, run the command `pwd` and take a screenshot.

1. The directory where most of the systems configuration files are.
2. The directory where binaries for many of the utilities we have used, like `mkdir`, ***really*** are.

**Part 2 3 points**

For **three** of the directories below, write a paragraph, in your own words, about the purpose of that directory. What type of files are stored there, what is special about it…?

- /etc
- /dev
- /var
- /usr/bin
- /bin
- /boot
- /proc

**Part 3 1 point**

Write a grep command that will find all the files in your home directory that contain the word ‘alias’.

The output should only show the files, not every instance of word ‘alias’. You can find out how to do this in the man page for grep.

Example of what the output will look like.

```bash
./.bash_history
./.bashrc
```

Include a screenshot that shows your command, and the output.

**Part 4 3 points**

Create a new week3 directory in your home directory copy the script below into your shell and hit enter. You don’t have to create a file, you can just copy this directly into the shell and hit enter

```bash
mkdir dir{1..2}

for dir in $(find . -mindepth 1 -type d); do
  touch $dir/f{1..4}.pyc $dir/p{1..4}.py
done
```

This will create two directories and write 8 files to each directory

use the `find` utility to find all of the `.pyc` files in both directories and delete them. 
Do this with just a single `find` command. 

`find` has an `-exec` command, you can search for this in the man page for find. You are looking fot the option that will allow you to delete all of files in one invocation of -exec.
**Don't** use the *-delete* option.

include a screenshot of the command you used to successfully delete the .pyc files.

**Part 5: 3 points**

Inside of the week3 directory create an **index.html** file using vi(m) and copy and paste the HTML file below into the new file you just created. 
Use vi(m) to remove all of the comments in the code below.

There is more than one way to perform this task. the full 3 points will be awarded to one of the more efficient methods.

Take at least 2 screenshots

- a screenshot of the command you used to create a new index.html file with vi(m)
- a screenshot of the edited file, to demonstrate that you successfully removed all of the comments

Describe the method that you used to remove the comments. Do this by adding a note to your pdf.
Your description needs to demonstrate that you understand how you removed the comments to get full marks.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <!-- <link rel="stylesheet" href="style.css"> -->
  <title>Document</title>
</head>
<body>
  <!-- comment -->
  <div>
    <p>one</p>
  </div>
  <!-- make the following changes: -->
  <div>
    <p>two</p>
  </div>
  <!-- add a footer -->
</body>
</html>
```

**Total: 12 points**

**********************************************Submission Instructions**********************************************

Submit a .pdf using the dropbox on D2L

Title your pdf “your-name-lab2.pdf” ie "nathan-mcninch-lab2.pdf”

File must be a .pdf.
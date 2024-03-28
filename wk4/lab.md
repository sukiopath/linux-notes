# Lab 3, Week 4

**Due date:** Friday, February 03 23:30

See the lab partners file included with this weeks notes.

Make sure that you work with your lab partners. If a team member is absent contact them and work with them remotely via discord, zoom, ravens...

**Don't divide up the work, work on each question together.** It is important that you all learn the material in every lab.

Lab must be completed as a regular user, not the root user.

**Instructions**

**Part one: 2 points**

Create a new regular user with the `useradd` utility. Your new users should have:

- a home directory /home/user-name
    - config files from /etc/skel should be copied to the new user's home directory
- use bash as their login shell

Include 4 screenshots for this.

Include 1 screenshot that demonstrates the command you used.

Include 2 screenshots that show where you found how to do both of the above bullet points in the man page for `useradd`

Include a screenshot that demonstrates you succeeded in creating a new user by finding the new line created in a file that contains information about users. We have looked at this file a few times.

**Part two: 1 point** 

Give your new user a password.

Submit a screenshot of the command that you used.

**Part three: 1 point**

Give your new user permission to use sudo.

Submit a screenshot of the command you used.

**Part four: 1 point**

Create a new system user with the name “weekfour”

Submit a screenshot of the command you used.

**Part five: 2 points**

create a new week4 directory in `/var`

Make the weekfour user and group the owner of the week4 directory

 Submit a screenshot of the command you used to change the ownership of the directory.

**Part  six: 2 points**

create a new file in the week4 directory (file name is up to you)

Change the file's permission to match the following.

`rwxr---wx` 

Submit a screenshot of the command you used to change the file's permission.

Include a note on why you think these permissions are odd.
This only needs to be 1-2 sentences.

**Part seven:  2 points**

How could you change to your new regular user created in part one? Without logging out and logging back in again. In addition to becoming the new user, you want to inherit their environment variables and shell configuration.

Include the command you found and citations to any resources you used. 

You don’t need to format your citation using any specific style guide.
A man page can be a resource.

**Total: 11 Points**

**********************************************Submission Instructions**********************************************

Submit a .pdf using the dropbox on D2L

Title your pdf “your-name-lab3.pdf” ie "nathan-mcninch-lab3.pdf”

File must be a .pdf.
Only one submission per team, make sure that all team members names are on the file.

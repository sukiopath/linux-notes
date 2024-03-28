# Week 4

## Learning Outcomes and Topics

**This Week:**

- Look at users, groups, ownership and permissions
- create a new user with `useradd` / `adduser`
- give a new user a password with `passwd`
- add a user to group with `usermod`
- view file ownership and permissions with `ls`
- change file permissions with `chmod`
- change file ownership with `chown`

**Next Week:**

- More on the shell
    - streams
    - processes
    - intro to scripting in bash

## Users, Permissions and Groups

In Week 2 we looked at this🔻 some output from the `ls` command, and what it all means.

```
$ ls -al
total 0
-rw-r--r--  1  mh9  devs  9  Apr 12 11:42  test
^           ^  ^    ^     ^  ^             ^
|           |  |    |     |  |             └──  filename
|           |  |    |     |  └──  last modified date
|           |  |    |     └──  filesize in bytes
|           |  |    └── group the file belongs to
|           |  └──  user the file belongs to
|           └──  number of hard links
└──  file mode (- indicates a regular file) and permissions g/u/o
```

The small amount of text above illustrates the main topics that we are going to talk about today. All three of these things, Users, Permissions and Groups are used as part of a relatively simple access control system. That is to say, Users, Permissions and Groups determine *who* can do *what* with a file.

## Users

We could separate the two types of users into:

- Regular users, humans (this could include root)
- System users, daemons might make use of system user accounts to run services, such as a database.

Every user on the system has a **UID**, user identification. You can view a user's UID with the `id` command.

- UID 0 Is `root`
- UID 1 to 999 Are reserved for system
- users UID 65534Is user `nobody`—
    - used, for example, for mapping remote users to some
      well-known ID, as is the case with [“Network File System”](https://learning.oreilly.com/library/view/learning-modern-linux/9781098108939/ch07.html#nfs)
- UID 1000 to 65533 and 65536 to 4294967294 Are regular users.

**Some important files associated with Users and Groups**

| Purpose | File |
| --- | --- |
| User database | /etc/passwd |
| Group database | /etc/group |
| User passwords | /etc/shadow |
| Group passwords | /etc/gshadow |

We have looked at the `/etc/passwd` file before, as used in an example to compare man pages and different sections. The passwd file stores data about all the users on a system. Each field is separated by a colon. 

```
root:x:0:0:root:/root:/bin/bash
|    | | | |    |     └──>  The Login Shell
|    | | | |    └──>  The users home directory
|    | | | └──>  users information, generally this is just name
|    | | └──> Group ID (GID)
|    | └──>  User ID (UID)
|    └──>  x indidcates that the user has an encryped password in the shadow file
└──>  The users name
```

**Creating a new user**

There are a few different tools that can be used to create a new regular user on a Linux OS. Today we are going to use the `useradd` command. The reason that we are using `useradd` instead of `adduser`(which is used in the textbook) is because `useradd` is available on most, if not all Linux OSs. 

`adduser` is a little more confusing. Sometimes `adduser` is a perl script, sometimes it is linked to `useradd`. Fedora for example, if you try to open the man page for `adduser` you will see the man page for `useradd`. And sometimes it is not available at all.

So we are going to use `**useradd**`

The `useradd` command can be used to define many attributes of a new user, where their homed directory is, which login shell they use and a password, although you shouldn’t use this for adding a password from the command line. (see the man page for why)

The defaults for some of these values are defined in `/etc/default/useradd`

When a new user is created, the default configuration files in /etc/skel can be added to the new user's home directory. Generally these are bash configuration files, but we could add other default configuration files, like a .vimrc file if we wanted to.

**Giving a new user a password.** 

Earlier I said not to use the password option with `useradd` from the command line, so how do we give a new user a password? With the `passwd` utility.

## Groups

In addition to Users, Linux distros have the concept of Groups. Every user belongs to at least one group, their user group. Users can belong to more than one group.

To see the groups that your user belongs to you can use the `groups` command

Adding a user to a group is one way that you can give a user permissions to perform certain tasks. The most general example of this is adding a user to a group that belongs to sudo. On most Linux distributions, this is ‘wheel’ or ‘sudo’. On Ubuntu it is the sudo group.

Just like regular users, system users have a default group. Adding a regular user to that group allows the regular user to perform certain tasks with that software.

You can add a new regular user to group with the `usermod` command.

`sudo usermod -aG sudo <user-name>`

The `-aG` options are important -a = append, without this the new group will replace your existing groups. -G is a list of groups that you want to add a user to.
replace <user-name> with the user you want to add to the sudo group. For example, if I have a regular user ‘pond’ on the system. `sudo usermod -aG sudo pond`. This will add pond to the sudo group and pond will be able to use sudo.

[Users and groups](https://wiki.archlinux.org/title/Users_and_groups)

## Permissions and ownership

Every file on a Linux OS is owned by one user and one group.  Files also have permissions associated with them. 

Permissions on a file are of three scopes (or three categories of who):

- User
- Group
- Other

And each scope has some combination of three possible permissions:

- Read
- Write
- Execute

When you run the `ls -l` command in directory, you can see the file permissions of each file. 
In the example below, the permissions are:

- User
    - read, write and execute
- Group
    - read and write
- Other
    - read

```
d rwx rw- r--
^ ^   ^   ^
| |   |   └──  other
| |   └──  group
| └──  user
└── file type (d indicates a directory)
```

Permissions have a symbolic representation (rwx) and a numeric representation (0-7)

**Permissions table:**

| Pattern | Effective permission | Decimal representation |
| --- | --- | --- |
| --- | None | 0 |
| --x | Execute | 1 |
| -w- | Write | 2 |
| -wx | Write and execute | 3 |
| r-- | Read | 4 |
| r-x | Read and execute | 5 |
| rw- | Read and write | 6 |
| rwx | Read, write, execute | 7 |

ownership can be changed with the `chown` command.

`sudo chown user:group file` change the user and group that own a file.

`sudo chown user: file` also change user and group that own a file.

`sudo chown user file` change only the user that owns a file.

`sudo chown :group file` change only the group that owns a file.

Remember system users, users that are processes. One reason that you might change the ownership of a file is to give a service permission to write to a file. 

You can change permissions on a file with the `chmod` command. The `chmod` utility can be used with both the symbolic and numeric (sometimes referred to as the octal) method.

The symbolic method uses a combination of values that represent permissions and scope and operations that represent the change you want to make. 

```
- '+' add a permission
- '-' remove a permission
- '=' set a permission explicitly
```

`chmod u+x file` will add execute permissions to the user.

`chmod a+x file` will add execute permissions to the user, group and other

`chmod u+x,g+w,o-r file` will add execute to the user, write to the group and remove read from other.

`chmod u=rw,g=r,o= file` will set the users to read and write, groups to read and other to nothing.

The numeric method is a little more succinct.

`chmod 644 file` will set the user's permissions to read and write and group and other to read. 

[Inodes and the Linux filesystem](https://www.redhat.com/sysadmin/inodes-linux-filesystem)

[Users and groups](https://wiki.archlinux.org/title/Users_and_groups#Permissions_and_ownership)

## Sudo and Root

As mentioned earlier, you can add a user to the sudo group, which on an Ubuntu based OS will allow that user to use the sudo command. This grants a user temporary elevated privileges.

The sudo command has configuration files in `/etc` `sudo.conf` and `sudoers` 

| File | Purpose |
| --- | --- |
| /etc/sudo.conf | Configuration for the sudo command |
| /etc/sudoers | Configuration for who can use sudo |

For example, the sudoers file on my system contains the line `%wheel ALL+(ALL) ALL` Which grants members of the wheel group the ability to do everything with sudo.

Why do we have the sudo command? Why not just use the root user for everything?

[Sudo](https://wiki.archlinux.org/title/Sudo)

## Reading

**Reading Questions:**

- Why should additions to PATH go in .bash_profile?
- What are streams in Linux?
- How do you run a startup file in the current shell?
- When is a PID assigned?
- What is a background process?
- How are commands run by the shell?
- What should the first line of a shell script be?

Read the following sections from [A Practical Guide to Linux Commands, Editors and Shell Programming](https://learning.oreilly.com/library/view/a-practical-guide/9780134774626/ch05.xhtml#ch05) Ch 5:

- Standard Input and Standard Output

[A Practical Guide to Linux Commands, Editors, and Shell Programming, Fourth Edition](https://learning.oreilly.com/library/view/a-practical-guide/9780134774626/ch05.xhtml#ch05)

Read the following sections from [A Practical Guide to Linux Commands, Editors and Shell Programming](https://learning.oreilly.com/library/view/a-practical-guide/9780134774626/ch08.xhtml#ch08) Ch 8:

- Startup Files
- Processes
- Processing the Command Line

[A Practical Guide to Linux Commands, Editors, and Shell Programming, Fourth Edition](https://learning.oreilly.com/library/view/a-practical-guide/9780134774626/ch08.xhtml#ch08)

Read the following sections from [Learning Modern Linux](https://learning.oreilly.com/library/view/learning-modern-linux/9781098108939/ch03.html) Ch 3:

- Scripting

[Learning Modern Linux](https://learning.oreilly.com/library/view/learning-modern-linux/9781098108939/ch03.html#scripting)

---
type: Page
title: Week 9 ACIT 2420
description: null
icon: null
createdAt: null
lastUpdated: null
tags: [2420, UnitFiles, Services, Timers, systemctl]
---

## Learning Outcomes and Topics

This week:

- Write a Unit, service, that starts a process. In this case runs a script

- Write a Unit, timer, that will run the service at a specified time and date

Next week:

- Intro to web servers, Nginx Caddy

- Intro to Networking components:

    - VPC

    - Firewall

    - Load balancer

## Weather service and timer

So that we have an application to create a timer and service around we are going to create a really simple shell script that makes use of curl and wttr to get the weather every day.

We are also going to use curl. `man curl` if you have not used curl before.

curl is used to transfer data to and from servers and supports a number of popular protocols, including http and https

[curl](https://curl.se/)

And [wttr.in](http://wttr.in), which is a simple python web application that can be used to retrieve weather information.

[GitHub - chubin/wttr.in: The right way to check the weather](https://github.com/chubin/wttr.in)


## Units

**Types of Units** (Today we are only going to look at service and timer files)

- `.service`: A service unit describes how to manage a service or application on the server. This will include how to start or stop the service, under which circumstances it should be automatically started, and the dependency and ordering information for related software.

- **`.**socket`: A socket unit file describes a network or IPC socket, or a FIFO buffer that `systemd` uses for socket-based activation. These always have an associated `.service` file that will be started when activity is seen on the socket that this unit defines.

- **`.**device`: A unit that describes a device that has been designated as needing `systemd` management by `udev` or the `sysfs` filesystem. Not all devices will have `.device` files. Some scenarios where `.device` units may be necessary are for ordering, mounting, and accessing the devices.

- **`.**mount`: This unit defines a mountpoint on the system to be managed by `systemd`. These are named after the mount path, with slashes changed to dashes. Entries within `/etc/fstab` can have units created automatically.

- **`.**automount`: An `.automount` unit configures a mountpoint that will be automatically mounted. These must be named after the mount point they refer to and must have a matching `.mount` unit to define the specifics of the mount.

- **`.**swap`: This unit describes swap space on the system. The name of these units must reflect the device or file path of the space.

- `.target`: A target unit is used to provide synchronization points for other units when booting up or changing states. They also can be used to bring the system to a new state. Other units specify their relation to targets to become tied to the target’s operations.

- **`.**path`: This unit defines a path that can be used for path-based activation. By default, a `.service` unit of the same base name will be started when the path reaches the specified state. This uses `inotify` to monitor the path for changes.

- `.timer`: A `.timer` unit defines a timer that will be managed by `systemd`, similar to a `cron` job for delayed or scheduled activation. A matching unit will be started when the timer is reached.

- **`.**snapshot`: A `.snapshot` unit is created automatically by the `systemctl snapshot` command. It allows you to reconstruct the current state of the system after making changes. Snapshots do not survive across sessions and are used to roll back temporary states.

- **`.**slice`: A `.slice` unit is associated with Linux Control Group nodes, allowing resources to be restricted or assigned to any processes associated with the slice. The name reflects its hierarchical position within the `cgroup` tree. Units are placed in certain slices by default depending on their type.

- **`.**scope`: Scope units are created automatically by `systemd` from information received from its bus interfaces. These are used to manage sets of system processes that are created externally.

[Understanding Systemd Units and Unit Files | DigitalOcean](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)

## Unit files

Reminder of where these files are found



`/usr/lib/systemd/system/`

systemd unit files distributed with installed RPM, apt… packages.

`/run/systemd/system/`

systemd unit files created at run time. This directory takes

precedence over the directory with installed service unit files.

`/etc/systemd/system/`

systemd unit files created by systemctl enable as well as unit files added for extending a service. This directory takes precedence over the directory with runtime unit files.



**Example unit file, writing a service**

Unit files are just plain text files, you can create them in your favorite text editor or you can use the command `systemclt edit`, which will create files (if needed) and open the default editor. see the ‘edit UNIT...’ section in the systemctl man page for more info

`man systemd.service`



```text

[Unit]
Description=Write a short description, what does the service do?

[Service]
Type=simple
ExecStart=/path/to/executable

[Install]
WantedBy=multi-user.target
```




Unit files are comprised of Sections in `[]` and key value pair directives.

The sections don’t have to go in any order, however generally the `[Unit]` Section is the first section in a file. The unit section contains information about the unit, a brief description, the location of any documentation... Although not required you should always include a description, so that other people using the service know what it does.

The `[Service]` section in our example above contains the `ExecStart` directive, this is the command that is used to start the service. For a shell script with a shebang this might be as simple as the full path to the script.

Another important directive in the service section is the `Type` directive. If it is absent, the default type of `simple` is used. 

For services started with a timer, you generally want `Type=oneshot`

Why do you think this is the case?



Finally, in the example above there is an `[Install]` section. The `WantedBy` directive is the simplest way to specify a relationship to when a service should start. In this example, it starts when the [mulit-user.target](http://mulit-user.target) is reached.

`man systemd.unit.5` to see more on unit files.



## Timers



A timer is a type of unit file that allows you start a service at a specified time.



`man systemd.timer`

view running timers `systemctl list-timers` or 



**file naming convention**

It is recommended that you name timers using the same name as the service they start, you don't have to, but unless you have a good reason to not use the same name you should. You also have to add an additional line to a timer file if the name is different. If you use a different name in the Timer section, add `Unit=service-name.service` If you use the same name, you can leave this line out.



**Timers OnCalendar and OnUnitActiveSec**



systemd has two types of timers **Monotonic** timer and **Realtime** timer

**Monotonic timer**, These start in relation to other system events, such as on Boot

```text
[Unit]
Description=Run foo weekly and on boot

[Timer]
OnBootSec=15min
OnUnitActiveSec=20d

[Install]
WantedBy=timers.target
```

**Realtime timer**, these start on calendar events, 5 am first day of the month for example

```text
[Unit]
Description=Run foo weekly

[Timer]
OnCalendar=weekly
Persistent=true

[Install]
WantedBy=timers.target
```

OnCalendar event format

An OnCalendar event uses a format similar to cron, it can take a little while to wrap your head around, but it isn’t actually too bad.

`DayOfWeek Year-Month-Day Hour:Minute:Second`

To run a service on the first Saturday of every month, use:

```text
OnCalendar=Sat *-*-1..7 18:00:00

```

When using the `DayOfWeek` part, at least one weekday has to be specified. If you want something to run every day at 4am, use:

```text
OnCalendar=*-*-* 04:00:00

```



**testing your onCalendar (realtime) timers** before adding a time in a timer file it is useful to test out that the timer will run as you intend. systemd-analyze can be used for this as well.

```text
systemd-analyze calendar "*-*-* 01:00:00"
```

[systemd/Timers - ArchWiki](https://wiki.archlinux.org/title/Systemd/Timers)

see `man systemd.timer` and `man systemd.time`



**Just like services after creating a new timer you need to run the following command**

this checks for new unit files, or changes to unit files, it doesn't restart a service even if a change exists

```text
sudo systemctl daemon-reload
```

## Changing the TimeZone

By default most servers are set to UTC so if you want your timer to start at 05:00 local time you either need to figure out the time difference, or set the time zone.

For this weeks lab, set the timezone.

You can set the time zone in Ubuntu with this command

`sudo timedatectl set-timezone America/Vancouver`

check the current TZ with `timedatectl`

[UTC - exact time now - Time.is](https://time.is/UTC)

## sftp strict host checking

When you connect to a server for the first time via ssh it asks you if you trust the host. If you respond “yes” that ip address is added to your `~/.ssh/known_hosts` file. sftp doesn’t check this, and if the ip address isn’t in known_hosts you won’t be able to connect.

You can connect via ssh first, or `sftp -oStrictHostKeyChecking=no user@ip-address`

This -o option allows you to pass additional information, like Port number. In this case checking is ignored and the ip address is automatically to know_hosts.



## Troubleshooting

If you would like to see additional logs to help you troubleshoot errors, add the following to your service file.

```text
[Service]
Environment=SYSTEMD_LOG_LEVEL=debug
```

## Reading

Reading questions:

- What is a server?

- What is HTTP(s)

    - What are the default ports that http and https run on?

- What are some features of Nginx?

    - Highlights, why might you choose Nginx?

- What is a firewall?

- How does a firewall work?

    - How do you protect a server with a firewall

- How do you start and stop UFW?

[How To Set Up a Firewall with UFW on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-22-04)

[Nginx: From Beginner to Pro](https://learning.oreilly.com/library/view/nginx-from-beginner/9781484216569/A371820_1_En_1_Chapter.html)


## Lab Groups

## 86052

G1

- Gill, Aarnav Singh

- Galam, Mike Javee

- Lak, Nawdar Shazad

G2

- Chitwan, Kaur

- Nunmawi, Melody

- Singh, Rajveer

G3

- Faghih-Ordobadiu, Mehrdad

- Liu, Aristotle

- Kim, Calvin

G4

- Li, Leon

- Oxcino, Jyle

- Wentworth, Adam

G5

- Kaur, Arshdeep

- Delacour, Suki

- Sotelo, Ender

G6

- Tran, Victor

- Stewart, Ian

- Zhuang, Ziyin

G7

- Wong, Vincent

- Yang, Jimmy

- Nguyen, Tuan

G8

- Jayakody Mudiyanselage, Anjana

- Uppin, Dev

- Reziapov, Timur

## 86053

G1

- Bell, Maia

- Rogerson, Joshua

- Nguyen, Coburn

G2

- Calalang, Joshua

- Causing, Louie Jade

- Rede, Abell

G3

- Arcalas, Aaron Matthew

- Choi, Joongwon

- Eustaquio, Raphael Miguel

G4

- Khavin, Daniel

- Le, Minh-Anh

- Nguyen, Hoang Quyen

G5

- Su, Carmen

- Gholamhosseini, Haniehsadat

- Le, Tin

G6

- Chang, Henry

- Ong, Nigel

- Poon, Marco Yat Long

G7

- Kayeh, Nicolas

- Kim, Sungyul

- Lee, Cameron

## 86054

G1

- Leung, Wen Kai Quinten

- Kandola, Devin

- Lim, Norvin

G2

- Cortez, Bryce

- Yoon, Andrew

G3

- Al-Mridha, Saad

- Grover, Bhavya

- Thompson, Quinn

G4

- Le, Stanley

- Wyse, Latham

- Pham, Hoang Khoi

G5

- Mushtaq, Muhammad Asad

- Rajan, Ramith

- Afonso, Markus

G6

- Lazzaroni, Daniel

- Asfaw, Surafele

- Song, Joseph

G7

- Davari, Mohammadamin

- Bhavdeep Singh, -

- Nguyen, Duy



## Labs



**Due Date:** Friday, March 24 23:30



### **Instructions**

In additions to the below, submit the wthr script, service and timer that we will create in class.



**Setup**



Create a new Spaces Bucket on DigitalOCean in SFO3, you should also have a droplet in this region. 



Install the `s3cmd` utility in a droplet running Ubuntu 22.04 or 22.10

`sudo apt update `

`sudo apt install s3cmd`



Follow the instructions below for configuring the `s3cmd` so that you can access the bucket you just created.



Run the command `s3cmd ls` to list your buckets.



Try moving a file to your bucket `s3cmdn put <file> <your-bucket>`



**Tasks**



Edit the backup script below so that it will copy your backup directory to your spaces bucket.



**Write a service file that will run the backup script provided below**



**Write a timer file that will run the backup script once a week**



In your pdf document describe: 

- Include group members names

- The directives that make up your service and timer that you created for the backup script.

- The changes that you had to make to the backup script provided.

- The steps needed to get the backup service working:

    - Where do files go and why?

    - What are the commands that you needed to run to get the backup service setup?

Include screenshots to demonstrate that you get everything setup. `systemctl status` for both the service and timer.
​Commands and code snippets should stand out. So that it is clear what the commands are. 

### Grading



2 points. Completing the wthr script, service, and timer in class.

2 points.  Service that runs the backup script.

2 points. Timer that runs the backup service.

4 points. Document that describes setup of project.

### **Submission Instructions**



Submit a ***your-name-unit-files.zip*** using the D2L dropbox "service and timer"

Your .zip should include:

- document.pdf

- wthr.timer

- wthr.service

- wthr script

- bckup.timer

- bckup.service



**Resources:**

[How to Manage Administrative Access to Spaces | DigitalOcean Documentation](https://docs.digitalocean.com/products/spaces/how-to/manage-access/#access-keys)

[Setting Up s3cmd 2.x with DigitalOcean Spaces | DigitalOcean Documentation](https://docs.digitalocean.com/products/spaces/reference/s3cmd/)

[Examples of s3cmd 2.x Usage with DigitalOcean Spaces | DigitalOcean Documentation](https://docs.digitalocean.com/products/spaces/reference/s3cmd-usage/)


**Backup Script**

```shell
#!/bin/bash

# run the command like so:
# spcbup directory-you-want-to-back-up

DATETIME=$(date +%y%m%d-%H_%M_%S)
SRC=$1
DST=4640-test #change to your spaces bucket
FILENAME=backup

# create a .tar.gz file
tarfile(){
  if tar -czvf $FILENAME-$DATETIME.tar.gz $SRC; then
    echo "Tar file created"
  else
    echo "Failed to create .tar.gz"
    exit 1
  fi
}

# move back up file to spaces with s3cmd
movetoSpace(){
  if s3cmd put $FILENAME-$DATETIME.tar.gz s3://$DST; then
    echo "Backup moved to $DST"
  else
    echo "Failed to move backup to the $DST"
    exit 1
  fi
}

if tarfile; then
  movetoSpace
  mv $FILENAME-$DATETIME.tar.gz /tmp
  echo "cleaning up, .tar.gz file moved to /tmp"
fi

exit 0
```



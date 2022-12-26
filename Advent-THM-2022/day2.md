 The Story

an illustration depicting a wreath with ornaments

Check out CMNatic's video walkthrough for Day 2 here!

Santa’s Security Operations Center (SSOC) has noticed one of their web servers, santagift.shop has been hijacked by the Bandit Yeti APT group. Elf McBlue’s task is to analyse the log files captured from the web server to understand what is happening and track down the Bandit Yeti APT group.

a picture of ElfMcBlue

Learning Objectives

In today’s task, you will:

Learn what log files are and why they’re useful
Understand what valuable information log files can contain
Understand some common locations these logs file can be found
Use some basic Linux commands to start analysing log files for valuable information
Help Elf McBlue track down the Bandit Yeti APT!
What Are Log Files and Why Are They Useful

Log files are files that contain historical records of events and other data from an application. Some common examples of events that you may find in a log file:

Login attempts or failures
Traffic on a network
Things (website URLs, files, etc.) that have been accessed
Password changes
Application errors (used in debugging)
and many, many more
By making a historical record of events that have happened, log files are extremely important pieces of evidence when investigating:

What has happened?
When has it happened?
Where has it happened?
Who did it? Were they successful?
What is the result of this action?
For example, a systems administrator may want to log the traffic happening on a network. We can use logging to answer the questions above in a given scenario:

A user has reportedly accessed inappropriate material on a University network. With logging in place, a systems administrator could determine the following:

Question	Answer
What has happened?
A user is confirmed to have accessed inappropriate material on the University network.
When has it happened?
It happened at 12:08 on Tuesday, 01/10/2022.
Where has it happened?
It happened from a device with an IP address (an identifier on the network) of 10.3.24.51.
Who did it? Were they successful?
The user was logged into the university network with their student account.
What is the result of the action?
The user was able to access inappropriatecontent.thm.


What Does a Log File Look Like?a blue-team elf holding a magnifying glass


Log files come in all shapes and sizes. However, a useful log will contain at least some of the following attributes:

A timestamp of the event (I.e. Date & Time)
The name of the service that is generating the logfile (I.e. SSH is a remote device management protocol that allows a user to login into a system remotely)
The actual event the service logs (i.e., in the event of a failed authentication, what credentials were tried, and by whom? (IP address)).
an annotated picture of an example of a log file


Common Locations of Log Files

Windows

﻿Windows features an in-built application that allows us to access historical records of events that happen. The Event Viewer is illustrated in the picture below:

a picture of the event viewer on Windows

These events are usually categorised into the following:

Category	Description	Example
Application	This category contains all the events related to applications on the system. For example, you can determine when services or applications are stopped and started and why.	The service "tryhackme.exe" was restarted.
Security	This category contains all of the events related to the system's security. For example, you can see when a user logs in to a system or accesses the credential manager for passwords.	User "cmnatic" successfully logged in.
Setup	This category contains all of the events related to the system's maintenance. For example, Windows update logs are stored here.	The system must be restarted before "KB10134" can be installed.
System	This category contains all the events related to the system itself. This category of events contains logs that relate to changes in the system itself. For example, when the system is powered on or off or when devices such as USB drives are plugged-in or removed.	The system unexpectedly shutdown due to power issues.
Linux (Ubuntu/Debian)

﻿On this flavour of Linux, operating system log files (and often software-specific such as apache2) are located within the /var/log directory. We can use the ls in the /var/log directory to list all the log files located on the system:

Listing log files within the /var/log directory
cmnatic@aoc2022-day-2:/var/log$ ls -lah
total 724K
drwxrwxr-x   9 root      syslog          4.0K Nov 14 10:59 .
drwxr-xr-x  13 root      root            4.0K Oct 26  2020 ..
drwxr--r-x   3 root      root            4.0K Nov 14 10:56 amazon
drwxr-xr-x   2 root      root            4.0K Oct 26  2020 apt
-rw-r-----   1 syslog    adm              11K Nov 14 11:03 auth.log
-rw-rw----   1 root      utmp               0 Oct 26  2020 btmp
-rw-r--r--   1 root      root            7.3K Nov 14 10:59 cloud-init-output.log
-rw-r--r--   1 syslog    adm             251K Nov 14 10:59 cloud-init.log
drwxr-xr-x   2 root      root            4.0K Oct  7  2020 dist-upgrade
-rw-r--r--   1 root      adm              36K Nov 14 10:59 dmesg
-rw-r--r--   1 root      adm              36K Nov 14 10:56 dmesg.0
-rw-r--r--   1 root      root             12K Oct 26  2020 dpkg.log
drwxr-sr-x+  3 root      systemd-journal 4.0K Nov 14 10:55 journal
-rw-r-----   1 syslog    adm              98K Nov 14 10:59 kern.log
drwxr-xr-x   2 landscape landscape       4.0K Nov 14 10:57 landscape
-rw-rw-r--   1 root      utmp            286K Nov 14 11:03 lastlog
drwx------   2 root      root            4.0K Nov 14 10:55 private
-rw-r-----   1 syslog    adm             207K Nov 14 11:03 syslog
drwxr-x---   2 root      adm             4.0K Nov 14 10:55 unattended-upgrades
-rw-rw-r--   1 root      utmp            8.3K Nov 14 11:03 wtmp
The following table highlights some important log files:

Category	Description	File (Ubuntu)	Example
Authentication	This log file contains all authentication (log in). This is usually attempted either remotely or on the system itself (i.e., accessing another user after logging in).	auth.log	Failed password for root from 192.168.1.35 port 22 ssh2.
Package Management	This log file contains all events related to package management on the system. When installing a new software (a package), this is logged in this file. This is useful for debugging or reverting changes in case this installation causes unintended behaviour on the system.	dpkg.log	2022-06-03 21:45:59 installed neofetch.
Syslog	This log file contains all events related to things happening in the system's background. For example, crontabs executing, services starting and stopping, or other automatic behaviours such as log rotation. This file can help debug problems.	syslog	2022-06-03 13:33:7 Finished Daily apt download activities..
Kernel	This log file contains all events related to kernel events on the system. For example, changes to the kernel, or output from devices such as networking equipment or physical devices such as USB devices. 	kern.log	2022-06-03 10:10:01 Firewalling registered
Looking Through Log Files

Log files can quickly contain many events and hundreds, if not thousands, of entries. The difficulty in analysing log files is separating useful information from useless. Tools such as Splunk are software solutions known as Security Information and Event Management (SIEM) is dedicated to aggregating logs for analysis. Listed in the table below are some of the advantages and disadvantages of these platforms:

Advantage	Disadvantage
SIEM platforms are dedicated services for log analysis.	Commercial SIEM platforms are expensive to license and run.
SIEM platforms can collect a wide variety of logs - from devices to networking equipment.	SIEM platforms take considerable time to properly set up and configure.
SIEM platforms allow for advanced, in-depth analysis of many log files at once.	SIEM platforms require training to be properly used.
Luckily for us, most operating systems already come with a set of tools that allow us to search through log files. In this room, we will be using the grep command on Linux.

﻿Grep 101A blue-team elf holding a feather and notepad

﻿Grep is a command dedicated to searching for a given text in a file. Grep takes a given input (a text or value) and searches the entire file for any text that matches our input. 

Before using grep, we have to find the location of the log file that we want to search for. By default, grep will use your current working directory. You can find out what your current working directory is by using pwd. For example, in the terminal below, we are in the working directory /home/cmnatic/aoc2022/day2/:


Using pwd to view our current working directory
cmnatic@thm:~/aoc2022/day2 pwd
           /home/cmnatic/aoc2022/day2/
If we wish to change our current working directory, you can use cd followed by the new path you wish to change to. For example, cd /my/path/here. Once we've determined that we are in the correct directory, we can use ls to list the files and directories in our current working path. An example of this has been put into the terminal below:

Using ls to list the files and directories in our current directory
cmnatic@aoc2022-day-2:~$ ls -lah
webserver.log helloworld.txt mydirectory
Now that we know where our log files are, we can begin to proceed with learning how to use grep. To use grep, we need to do three things:

Call the command.
Specify any options that we wish to use (this will later be explained), but for now, we can ignore this.
Specify the location of the file we wish to search through (grep will first assume the file is in your current directory unless you tell it otherwise by providing the path to the file i.e. /path/to/our/logfile.log).
For example, in the terminal below, we are using grep to look through the log file for an IP address. The log file is located in our current working directory, so we do not need to provide a path to the log file - just the name of the log file.

Using grep to look in a log file for activity from an IP address
ubuntu@thm:~ grep "192.168.1.30" access.log
192.168.1.30 - - [14/Nov/2022:00:53:07 +0000] "GET / HTTP/1.1" 200 13742
192.168.1.30 - - [14/Nov/2022:00:53:43 +0000] "HEAD
In the terminal above, we can see two entries in this log file (access.log) for the IP address "192.168.1.30". For reference, we've narrowed down two entries from a log file with 469 entries. Our life has already been made easier! Here are some ideas for things you may want to use grep to search a log file for:

A name of a computer.
A name of a file.
A name of a user account.
An IP address.
A certain timestamp or date.
As previously mentioned, we can provide some options to grep to enable us to have more control over the results of grep. The table below contains some of the common options that you may wish to use with grep.

Option	Description	Example
-i	
Perform a case insensitive search. For example, "helloworld" and "HELLOWORLD" will return the same results

grep -i "helloworld" log.txt and grep -i "HELLOWORLD" log.txt will return the same matches.
-E	Searches using regex (regular expressions). For example, we can search for lines that contain either "thm" or "tryhackme"	
grep -E "thm|tryhackme" log.txt

-r	Search recursively. For example, search all of the files in a directory for this value.	
grep -r "helloworld" mydirectory

Further options available in grep can be searched within grep's manual page via man grep

Practical:

the logo of the BanditYeti APT group

﻿For today's task, you will need to deploy the machine attached to this task by pressing the green "Start Machine" button located at the top-right of this task. The machine should launch in a split-screen view. If it does not, you will need to press the blue "Show Split Screen" button near the top-right of this page.

If you wish, you can use the following credentials to access the machine using SSH (remember to connect to the VPN first):

IP address: MACHINE_IP
Username: elfmcblue
Password: tryhackme!
Use the knowledge you have gained in today's task to help Elf McBlue track down the Bandit Yeti APT by answering the questions below.

Answer the questions below
Ensure you are connected to the deployable machine in this task.
No answer needed
Use the ls command to list the files present in the current directory. How many log files are present?

Answer format: *
Elf McSkidy managed to capture the logs generated by the web server. What is the name of this log file?

Answer format: *********.***
Begin investigating the log file from question #3 to answer the following questions.

No answer needed
On what day was Santa's naughty and nice list stolen?

Answer format: ******
What is the IP address of the attacker?

Answer format: **.**.***.***
What is the name of the important list that the attacker stole from Santa?

Answer format: **********.***
Look through the log files for the flag. The format of the flag is: THM{}

Answer format: ***{****************}
Interested in log analysis? We recommend the Windows Event Logs room or the Endpoint Security Monitoring Module. 

No answer needed

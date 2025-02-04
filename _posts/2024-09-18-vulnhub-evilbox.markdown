---
layout: post
title: "Over the Wire: Bandit Walkthrough"
date: 2024-09-18 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Evilbox: One is a vulnerable machine on vulnhub, by Mowree. This blog post will be a walkthrough of hacking the machine.


The first step is to find the IP address of the target. (Technically the IP address is up there, but I’m going to ignore that). To do this, I did an nmap scan of the network.


After doing this, the IP Address ports 22 for SSH and 80 for HTTP are open. I started up a second nmap scan of the machine, this time to find service versions, and poked around on the web page.


The web page however, was an Apache2 default page.


My next step was to enumerate the target through a dirb scan. Through that scan, I found a robots.txt file…


And a directory called /secret. Going to that directory was not helpful however.


It was time for more enumeration, this time for the /secret directory. This time with a gobuster scan of the target to find anything interesting.


If a file called ‘evil.php’ isn’t interesting, then nothing is. Going there however left me with nothing once again.


It came time to use the ffuf tool in order to fuzz the directory and find out what it can do. With this, I was able to figure out which parameter I needed in order to continue.



With the /etc/passwd file, I saw the mowree user. I managed to get an ssh key for this user.



However, trying to use this key didn’t work because there was a passphrase on it.


I needed the crack the passphrase in order to use the key. I used ssh2john in order to do that.


Now I was able to log in as mowree and get the first flag.


Now for privilege escalation. The first thing I do is see what I can do as sudo, running sudo -l. However, that didn’t work here.


The next few steps go in much the same way. Trying to find a way for privilege escalation, it not working, and then me trying a different method. To save some time, I ran the below command to find world writable folders.


Now that’s a lot of files, but all the way at the end of this command is something really interesting.


A world writable /etc/passwd file. The /etc/passwd file has all the information for user accounts. It doesn’t have passwords (/etc/shadow has that) but world writable means anybody can just edit the file to elevate their privileges.

To take advantage of this, we can change the root password to something we know.

Use openssl to generate a password hash, replace the ‘x’ in the /etc/passwd file with that hash, and then switch over to root.


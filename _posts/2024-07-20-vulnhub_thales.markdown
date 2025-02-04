---
layout: post
title: "Vulnhub Thales Walkthrough"
date: 2024-07-20 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Thales is a vulnerable machine found on VulnHub, named so in reference to Thales of Miletus. This blog post will be a walkthrough of how to get the two flags on this machine, the user flag and the root flag. I will also try to include any helpful links that I used while doing this machine, and try to write down my thought process during the machine as well.


Starting off, after installing the machine on VirtualBox, I did an nmap scan of my internal network to make sure the machine was on there and to get the IP address.


After that I pinged the machine a few times, just to make sure.

There are two ports open on the machine, Port 22 for SSH and Port 8080 for HTTP. Since there was a website, I went there to poke around. I was thinking that maybe I would use the web page to find some credentials to use ssh to login, just from previous machines that I have completed.


But there wasn’t anything that could be used on the website to use for an SSH login. There was just a default Apache Tomcat page. There were no comments in the page source. The next step was further enumeration using nmap, and the dirb and gobuster tools.





This, unfortunately, was sort of a dead end. I wanted to see if there were any hidden directories that would contain some interesting information. There wasn’t anything like that, but there was the /manager page, which was a log in page. I just needed to find the username and password.

I opened up msfconsole. Metasploit has a ton of apache tomcat modules, and it was just a matter of choosing the right one. After some trial and error, I found the correct one.


This was the right one. Using this module, I found the username and password, and logged in to the manager page.



Not really necessary, but I thought it would be at the time

And there was somewhere to upload. I used HackTricks, which has a helpful page on Tomcat, to craft a WAR payload.


This was the command on HackTricks that will send a reverse shell. Once uploaded, there was a new page called /revshell.


I set up the listener, and then went to that page, and I had my initial access.


I forget how to spell commands all the time
To clean everything up, and to make the shell a little bit more stable, I used python to spawn a more interactive shell. python3 -c ‘import pty;pty.spawn(“/bin/bash”)’ was the command.


I poked around a little bit more to see what I could do as this user, but there wasn’t much. I couldn’t even open up the user flag.


But there was a .ssh directory that had a private key for Thales accessible. I copy and pasted that over to my Kali machine and got to work on utilizing that key.


First I used ssh2john to get the key into the proper format, and used john the ripper to crack the password. My first instinct after that was to try to use ssh, but that didn’t work. I had to use the shell I currently had to switch over.



My first instinct after that was to just check to see what I could do as thales, which also wasn’t much. This user wasn’t allowed to use sudo.


But there was that notes.txt file.


Going over to that directory, there was a file called backup.sh. The first thing I did was check to see what this file did, where it, as the name suggests, makes some backups. The next thing I did was check to see the permissions. This was an interesting file because it was owned by root, and it was world writable and executable.

I went over to Pentest Monkey to see the reverse shell cheatsheet. Modified the command to fit my needs, and appended it to backup.sh.


I set up my other nc Just needed to wait a little bit, and then I had a root shell on the target.


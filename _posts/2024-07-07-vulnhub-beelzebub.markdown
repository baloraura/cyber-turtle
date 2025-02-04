---
layout: post
title: "Vulnhub Beelzebub Walkthrough"
date: 2024-07-07 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Beelzebub: 1 is a vulnerable machine on Vulnhub, ranked as an easy machine. This post is going to be a walkthrough of the machine, from initial recon, to gaining access, to making it to root.

The first thing I did was make sure the vulnerable machine was connected to my network with an nmap scan. This was also important to get the IP address for the machine.


After an nmap scan, I saw a few open ports, the first one catching my attention being a web server. The other port was ssh on port 22. From this scan, I figured that I would be looking for some kind of credentials on the web server for ssh. I poked around the web server, and looked for a few directories using dirb. I found a login page, phpmyadmin, but I was unable to figure out anyway to log in. I looked at the page source, used burpsuite, and also checked a few of the network tabs, but was unable to figure out a way in.


The web server

phpmyadmin

trying to log in

phpinfo

checking the network tab to see if there was anything valuable
In one of the directories I found, there was an index.php page. Looking at the page source, I found an interesting comment. This was a pretty interesting place for this, as I would never think a page with a 404 error would have a clue.


From this clue, I gathered that I would have to do an md5sum on the word ‘beelzebub’. When you do this, make sure you to use the -n to remove the newline. With hashes, even a newline would create a completely different hash. After that, do a dirb scan with the hash as the directory.


wrong way to do this

the right way to do this
There will be an interesting directory, /wp-content/uploads. From there, I found a page, ‘Talk to Valak’. I opened up burpsuite again to figure out what was going on when I hit that ‘Say Hi to Valak!’ button.



And in there, I found a password. Now technically I did already know a username (krampus) just because this machine is running on my own computer. But I wanted to treat this as much like a black box as possible. A black box, when it comes to pentesting, is a system in which the pentesters know nothing about.


So to find a username, I used a tool called wpscan. It took a little bit to find how to get this to work, but the below command gave me the proper username (krampus!).



With that, I was able to use ssh to get into the system, and get the first user flag.



After that, I did a few basic checks to see what I could do on this machine. I ran sudo -l to see what commands I could run as sudo, ran find / -perm -u=s -type f 2>/dev/null to see which binaries were SUID.


To automate this whole process, I got linpeas.sh on the target machine. Linpeas is the Linux Privilege Escalation Awesome Script, and it automates the whole process. It checks to see where there are some vulnerabilities, what are some interesting things about if there are CVEs to which the machine is vulnerable.


And there is a CVE, CVE-2021–4034. This is a vulnerability in polkit’s pkexec utility, which allows unprivileged users to run commands as privileged users. There is a script that takes advantage of this vulnerability for privilege escalation.



After getting the zip file on the target machine, it was simply a matter of getting it ready. After running it, I was the root user. I ran this command:

python -c ‘import pty; pty.spawn(“/bin/bash”)’

in order to get a better looking shell. I forgot that the clear command does not work here.


And then I got the root flag.


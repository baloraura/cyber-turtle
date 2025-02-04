---
layout: post
title: "Vulnhub Dripping Blues Walkthrough"
date: 2024-06-19 10:00:01 +0000
categories: [post, hacking]
tags: [post, hacking]
---


Dripping Blues: 1 is a vulnerable machine found on VulnHub, rated easy. This post is going to be a walkthrough of the machine, starting from enumeration going all the way into initial access and getting to root. This walkthrough isn’t going to be clean, and there are a few dead ends that I hit trying to hack this machine that I tried to include.

The first thing I did in a machine like this is perform an nmap scan of the network, just to make sure the machine is connected and get it’s IP address. After that, I did another nmap scan to see what ports are open, and I saw three ports open: port 21 for ftp, port 22 for ssh, and port 80 for http.

I poked around the website, and I saw this message:


But nothing else was there. I tried to see if I could log in to the ftp using ‘anonymous’ as the username and leaving the password blank, and I was successful. I got a zip file named ‘respectmydrip.zip’. I tried to unzip it, but it was password protected. The thing about this was that I failed to enter the correct password, another zip file called ‘secret.zip’ came out. And I did see the names of the files in both zip files.



After that, I did a little bit more enumeration, first using gobuster. That scan didn’t turn up anything particularly useful. The next thing I did was start another nmap scan to see the service versions of each of the protocols running on the machine, to see if there was any vulnerability that I could exploit. That didn’t give anything useful in this context.



Then I just did a simple dirb scan, and through that I learned there was a robots.txt page. Robots.txt tells web crawlers what pages they can access. I see two pages: ‘dripisreal.txt’, and ‘/etc/dripispowerful.html’. Going to ‘dripisreal.txt’ didn’t pull up anything so useful, and I was unable to go to /etc/dripispowerful.html at this time.



I then decided to try to crack the password on the zip files, to see if there was anything there. I installed and used fcrackzip, and managed to get the password for ‘respectmydrip.zip’. I could not get the ‘secrets.zip’ file open (and even when I got the root flag later, I still was unable to get it open).


installation, because I couldn’t remember if it was installed

This took so long, I was about to quit when it finally found the password

Useful
I opened up Burpsuite to see if there was anything I could find there, but that wasn’t particularly useful either.


I figured that the next best thing to do would be to try to find the ‘/etc/dripispowerful.html’ page. I figured since both ‘dirb’ and ‘gobuster’ didn’t give me anything, I would use another tool: wfuzz. Fuzzing is providing unexpected or random data to a program. I used wfuzz to try to figure out where that page might be.


They did tell me to focus on drip
After finding it, I found the password for thugger’s account. I used ssh to get into the account. And through there, I got the user flag.



I was looking around for quite a while. I tried to find clues on how to get to root. I tried the Linux privilege escalation cheat sheet, but I was finding it rather difficult to figure out where to go next. I did try to find text files named ‘secret’, since there was the ‘secret.zip’, and I did find an interesting directory with files like ‘secret.pdf’ and ‘confidential.pdf’. I used scp to transfer those over to my Kali machine…


the contents of confidential.pdf
And they were no help at all.

I eventually used the Linux Privilege Escalation Awesome Script to figure out what I needed to do next. When I tried to find SUID binaries, I did come across an interesting binary.



And running linpeas told me polkit could be vulnerable to some privilege escalation. I came across CVE-2021–3560. A tool in polkit allows a user to run a command as a different user, but it requires authentication. This CVE is a vulnerability for bypassing that authentication. I used this script to take advantage of the exploit.



Running this script creating a new user that I could switch to, who was able to switch over to root. I went to the root directory to get the root flag.

I also tried to find the password for the secret.zip file, but that was in vain. I also took a look at the apache logs to see what all my scans looked like.


---
layout: post
title: "Over the Wire: Bandit Walkthrough"
date: 2024-08-29 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Double Trouble is a vulnerable machine found on Vulnhub, rated as easy. There are a few twists and turns in this machine though, so it might not be as ‘easy’ as other machines.


The first step is to find the machine’s IP address. To do that, I used the nmap command on the network. I like to output the results to text file, that way I can


The IP address is 192.168.191.16. Port 22 for SSH, and port 80 for HTTP are open on the target machine. Taking a look at the web server, it has a login page for a qdPM 9.1 website.


QDPM is a project management tool, and this particular version of qdPM has a pretty severe vulnerability. Through the profile picture, an attacker can upload a malicious PHP file.

There is now a target, we just need to find credentials to log in. The page source has nothing, so it is time for further enumeration.

The next step is to find the versions of SSH and HTTP the target is running, to see if there are other vulnerabilities. To do this, I used nmap with the -sV option.


Then, I used a dirb scan to find any web pages that could give some information. I was looking for credentials, so I was looking for any hidden directories.



There was a robots.txt file, which is a file that tells web crawlers what they are allowed and not allowed to look out. This is usually a good place to find hidden directories, but in this case it didn’t lead to anything.


There were 2 other interesting directories though. The first is the /uploads directory, which is where our eventual payload will end up.


And then there is a /secret directory.


Going to /secret, there is a .jpg file.



This part I did get a little stuck on, but there are ways to extract hidden messages from images.

I originally tried to use steghide to get the hidden messages, but there was a passphrase on the image.


And there is another tool, stegseek, that can be used to brute force this image. From there, I found the credentials.


With that, I could log in to the page.

(Note that at this point, I did run into some tech difficulties, and I did need to remove and get the vulnerable machine back running. So the change in IP address is due to that).


From there, I clicked on the profile, then went to account details, and I found the upload profile picture button. Just to test out that this will work, I created a text file and uploaded it.


I got the below error, which I thought was interesting until I looked it up and just found it meant that a library was missing.


But in the uploads/users directory, my test file was there.


So now I needed to upload a php reverse shell. I used this one from pentestmonkey, changing the IP address and port number to my Kali machine and 4444 respectively. I then set up the listener.


And uploaded the php reverse shell.


After clicking on it, I had a shell on the target machine.


I first improved the interactivity of the shell.


And then used sudo -l to see what I could do on this machine. To my surprise, there was a command, awk, that I could run.

According to GTFObin, you escalate privileges using awk, using this command:
sudo awk ‘BEGIN {system(“/bin/sh”)}’

So I ran that, and I was the root user.


However, there was no root flag. There was instead another .ova file. This is where the ‘double trouble’ comes in, because this machine had a second part. Another vulnerable machine to hack.



Doing everything all over again, I found the IP address (192.168.191.17) and found that ports 22 and 80 for SSH and HTTP respectively were on the vulnerable machine. Going to the website, I found a login page.


Skipping through the enumeration, because that didn’t really give me anything useful, I tried some default usernames/passwords, and it didn’t show any error messages when it didn’t log in.

I fired up Burpsuite and experimented a bit more with the login page.



Through a lot of experimentation (A LOT) I found that this machine was vulnerable to a time based SQL injection. Time-based SQL injections are useful because for a machine that doesn’t give any error messages, it can be the only feedback you get.

Automating a bit of the SQL injection, I used sqlmap and found the username and password.


And with the clapton username, with ZubZub99 as the password, I made the initial access and got the user flag.


Time for some privilege escalation. Utilizing the uname -r command, I found the kernel version.


This version of Linux is vulnerable to Dirty Cow. Utilizing this exploit on this machine, I got to the root user. I followed the instructions given, except changing the name from firefart to root.


---
layout: post
title:  "Can you cheat while hacking? Jangow Walkthrough"
date:   2023-06-05 17:53:33 -0500
categories: [post, hacking]
tags: [post, hacking, medium]
---

Can You Cheat While Hacking? Jangow Walkthrough
I want to get back into hacking. Jangow is a vulnerable machine rated as ‘easy’ on Vulnhub, so in order to get back into hacking I figured that a machine like that would be perfect.


In a Capture the Flag like this, there are two flags to get. The first flag is the user flag, and the second is when the hacker gets to the root user.

I first created a directory specifically for this machine. I wanted to make sure I put all the files in the same place, and document all my findings.

The next step in this CTF was to get the IP address and make sure it was on the same network as my Kali Linux machine. I know the IP address was in the log in screen, but I did want to do it in a more “official” way. I did this using a simple nmap scan of the network.


After I did that, I also saw that two ports were open. FTP on port 21 and HTTP on port 80. I then tried to see if I could log in to the FTP anonymously. Anonymous FTP logins use the username anonymous and any password, sometimes the anonymous user’s email as a way of documenting who is coming in and out.


That didn’t work, so my next step was to check out the HTTP website. I inspected the page source for any kind of comments in the HTML and JavaScript, but there was nothing there.


Bootstrap is a CSS framework, a way of easily formatting the appearance of a website.

I noticed there was a link on the website to a page called “buscar”. I remembered enough Spanish to recognize it as such, but didn’t remember enough to translate (sorry to all my Spanish teachers from fifth grade to high school).


This looked like this was a way of remote code execution, but I did want to gather a little bit more info.

To do that, I first used nmap -sV to get the service versions of the FTP and HTTP, to see if there were any vulnerabilities.


I then did a dirb scan of the website, although I will admit that I didn’t take a look at this afterwards.


My next move was to actually open up Burpsuite and intercept a request on that buscar page, to play around with it and test if my theory was correct. I opened up the browser in Burpsuite, intercepted a request, and sent it to the repeater.


My theory was correct. I executed whoami, and I got back ‘www-data’. I tested out a few more commands, and I used https://www.urlencoder.org/ to encode them.


I got the /etc/passwd file, and saw the user jangow01. I checked out the /home directory, and saw that this user had a directory. I looked inside, and I found the user.txt flag. Encoding the command to open up that user.txt, I got the first flag.



Important to know the version, and this comes to play later
Next step was to try to establish a more permanent presence. I first tried to do this by establish a reverse shell.

I have absolutely no idea why, but nothing I did worked. I first tried to do it through executing it through the URL, but that failed.

So I took a look around and found this config.php file in a wordpress directory.


Maybe those were the same credentials for the FTP?

I tried to log in, first with that username and password, but that failed. I then used jangow01, and managed to get in.


The next few steps were quite frustrating, because they were trying to establish a shell on the target machine, and each one failed.

I first made a bash script on my Kali machine, used the FTP credentials to put it in the /tmp directory of the target machine and confirmed that it was on the target machine.


Pro-Tip: Don’t name your reverse shell script ‘reverse_shell.sh’.
When I tried to execute that file, it still failed.

So at a loss, I decided to do one thing I didn’t really want to do when I started this, but it was quite necessary at this stage, because I had no idea why my efforts to establish a shell weren’t working.

I logged in through the actual machine. Is that cheating? I don’t know, but I did it anyway.

So having (technically) gotten access, I resolved to get to root. Through, researching the kernel version, I found this exploit for this kernel version. I downloaded it to my Kali machine first, compiled it on my kali machine, and used FTP to get it on the target machine.


I don’t think ‘normal_elf’ is a particularly good name for malicious binaries either
However, there were a few bumps in the road.

First, while on the Jangow machine, I couldn’t use the ‘/’ key. When executing a binary, you need to type ‘./binary_name’. So that was the first problem.

I managed to figure out a way around this, but to my chagrin I got the “version ‘glibc_2.34’ not found”. In this scenario, this just means I shouldn’t have compiled this binary on my Kali machine.

So I went through this again, except I just put the source code on the target machine and compiled it there.

But how did I get around not being able to use the ‘/’ key?


I compiled the source code, which the output was a file named ‘a.out’. I don’t know why, but I wasn’t able to name the output file. I had to figure out a way to get ‘./a.out’ into the command line. I used the find command, and outputted the results of that to a text file.

I then opened up the text file, and then used this ‘$(!!)’ to execute ‘./a.out’. This executed the a.out file, and I finally got to root.


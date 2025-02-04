---
layout: post
title: "Vulnhub Empire: Breakout"
date: 2023-04-25 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Vulnhub Empire: Breakout Walkthrough
This post is going to be a walkthrough of the vulnhub machine Empire: Breakout. Although this machine was rated as an easy machine, there were still some challenges.


After setting up the machine, the first thing I did was make a directory to keep all the information I collected during the enumeration phase. This is more of a preference than a requirement, but I did a lot of enumeration in this, and I did not want to keep running those commands.


This was an nmap scan of the network to discover the IP address of the machine. I found that the IP address was 192.168.56.26, and I found that ports 80, 139, 445, 10000, and 20000 were open. Notably in my opinion, SSH on port 22 was not open.

I then went to the web application to see if there was anything I could find. I was specifically looking for login pages or some kind of command line interface.


The first page I came across was a default Apache2 page. There was nothing notable on the page itself, but I viewed the page source and found an encrypted message there.


This message was the part that caused me a lot of issues. The message itself was pretty clear that the encrypted message was a password, however I did not recognize the encryption algorithm. I wanted to look for some kind of login page and a username before I tried to decrypt the message.

The next thing I wanted to do was to see if I could enumerate the SMB that was open on the target machine. SMB is Server Message Block, and is a file sharing protocol. Enum4linux is a command line tool that can enumerate SMB. To use it, you can just type ‘enum4linux <IP Address>’.


Scrolling through the information (there was a lot) got me a username, ‘cyber’. So at this point I had a username and an encrypted password.

To find more information, I needed to perform some more enumeration. I did a second nmap scan with the -sV option to get the service versions of all the protocols running.


HTTP was running on the target machine on ports 80, 10000, and 20000. It was running Webmin, which is a web based admin tool for Unix.

I also ran a dirb scan to see if there were any directories I could visit, but that didn’t give me anything useful.


I visited the web application on port 10000, and I found a login page. I found the same login page when I visited port 20000.


Now was the time I tried to decrypt the message, since I had both a login page and a username. This was the part that gave me the most trouble, since I didn’t recognize the encryption. I actually looked up another walkthrough in order to figure this part out, because I was completely stuck.

I found out that the algorithm wasn’t actually an encryption algorithm, it was an esoteric programming language called Brainfuck. An esoteric programming language is, from what I found, a programming language that is intentionally hard to understand. I found this writeup and tutorial if you want to read more about it.

Using decode.fr/brainfuck-language, I got the password.


I went back to 192.168.56.26:20000 and entered all the information, and I got into the system.


I looked around the interface and under login, I found a command shell. I ran whoami, id, ls, and I found the first flag.


I used this to establish a reverse shell. I set up a netcat listener on my Kali machine, and I used nc on the victim command shell to connect to the listener. And I gave myself a bit more control by spawning a TTY Shell.



The next step was to get to the root user in order to get the second flag. I ran sudo -l to see what commands I would be able to run as the sudo user, but the sudo command was not found.


I then ran ‘find / -perm -u=s -type f 2>/dev/null’ to see if there were any binaries that I could use, but that also did not give me much.


I also tried to see if I could open the /etc/shadow file, but that was also denied.

In the home directory of cyber, there is a binary called ‘tar’. I ran ‘whereis tar’ to see if the tar command was on the machine. It was, and checking further, I found that the binary in the home directory is just the tar command. I used the getcap command on the binary to find out more information.



I found that basically means I could use this to read any files. I first did this to try to get the /etc/shadow file. I wanted to try to crack the password using john the ripper. However, that didn’t work because no wordlist I had contained the password. I decided to look around the machine to find something.


The next part took a long time, but I eventually found something in the /var/backups directory. There was a hidden file called old_pass.bak. I used the tar binary to get the contents of that file.


And that was the root password. I used that to switch to root, went to the root directory, and got the final flag.



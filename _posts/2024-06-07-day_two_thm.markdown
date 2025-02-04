---
layout: post
title: "30 Days of THM: Day 2"
date: 2024-06-05 10:00:00 +0000
categories: [post, hacking, tryhackme]
tags: [post, hacking, tryhackme, medium]
---

30 Days of TryHackMe: Day 2
Welcome to 30 Days of TryHackMe, where I try to complete a section of TryHackMe every single day for 30 days. Today is the second day of this challenge I gave myself, and I am going to be picking up where I left off with the Complete Beginner Pathway. This series is not going to be a walkthrough or a writeup, but more a way of tracking my progress and some thoughts I have about the website along the way. If you are looking for answers, then you can stop reading here.

Yesterday, I worked on the tutorial section. Today, the section I will be working on is Linux Fundamentals. Linux is a free and open source operating system based on the UNIX operating system, and it is named after creator Linus Torvalds. Most people will know about MacOS or Windows, but Linux is important to know because so many servers run on it. Free means you can use it without paying for it, and open source means anyone can work on it.


Part One is a good introduction to Linux and its command line. It gives a bit of background about the operating system, and then it allows you to deploy a virtual machine so you can mess around with it. The machine you’re given is a Command Line Interface, which is just you typing commands. So no clicking around, just you and the commands you know.


Part Two explores SSH, Secure Shell. That is a way of remotely accessing another machine, very important in the context of hacking. In this room we open up the AttackBox and use the ‘ssh’ command in order to access another machine. It also introduces a few flags and switches that you would want to use to add more functionality to the commands. For example, the ‘ls’ command has the ‘-a’ argument, which will list all files in the directory, including any hidden files.

This section also goes over a few other commands, like ‘touch’, ‘mv’, and ‘file’, as well as file permissions. File permissions tell users who can do what with a file. Can they read it, write to it, execute it?

It also goes over the common directories and what goes in them. The /etc directory has files important to the operating system, including the ‘passwd’ and ‘shadow’ files, which contains the passwords for all the users on the system. The /var directory, which contains data from different applications running. /var/log is an important directory as it contains all the log files. The root directory, which is just the home for the root user. And the /tmp directory, short for temporary, which is world-writeable and anything in there will disappear after being restarted.


The third section introduces some other stuff that is useful to know. It goes over text editors, such as Nano and Vim. It goes over downloading files and transferring files using ‘wget’ and ‘scp’ respectively. Starting up webservers (it recommended I read the documentation for python’s HTTPServer module).

The next few sections I really appreciated because this stuff never really came easily to me. It goes over processes. How to look at processes using the ‘ps’ and ‘ps aux’ commands, starting and killing processes, and backgrounding and foregrounding. It goes over package management and system logs.

This section was an excellent introduction to Linux, and it was nice to go over all of this again.


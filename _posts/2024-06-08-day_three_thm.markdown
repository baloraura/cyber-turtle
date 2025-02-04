---
layout: post
title: "30 Days of THM: Day 3"
date: 2024-06-08 10:00:00 +0000
categories: [post, hacking, tryhackme]
tags: [post, hacking, tryhackme, medium]
---

30 Days of TryHackMe: Day 3
Welcome to 30 Days of TryHackMe, where I try to complete a section of TryHackMe every day for 30 days. Today is the 3rd day of the challenge, and I will be picking up right where I left off on the Complete Beginner Pathway. This is not a writeup or a walkthrough, this is more of a way of tracking my own progress and documenting my own thoughts as I go on.


Today’s section is the Network Exploitation Basics section. This section is an overview of networking. It isn’t promising to teach you everything about networking, just a rough overview to get you started.


And the first room is exactly that, titled Introductory Networking. The first section of this room, after the introduction, is all about the OSI model. The OSI model is a way of abstracting the way information is sent across networks. There are 7 layers of the OSI model:

Physical: the hardware and electrical pulses
Data Link: Responsible for physical addressing (think MAC addresses)
Network: Responsible for logical addressing (think IP addresses)
Transport: chooses which protocol to send the data over. TCP- for cases when you need every last bit data to be sent over — think web pages, file downloads. Or UDP — for cases when a little bit of packet loss is acceptable — think live video streaming.
Session: sets up and maintains the connection.
Presentation: receives the data from the application layer and translates the data into a standardised format.
Application: provides applications an interface to transmit data.
I remember the OSI model through the helpful mnemonic, Please Do Not Throw Sausage Pizza Away.

The next section goes over encapsulation, which is the process of data moving down the OSI model, starting from the application layer and moving all the way down to the physical layer. At each layer, a header is added. De-encapsulation is the reverse, and that happens when a computer is on the receiving end.

The next section after that goes over the TCP/IP model, which is like the OSI in that it is a way of abstracting the way information moves across a network. It also goes over the three way TCP handshake, which consists of a SYN, a SYN ACK, and an ACK. And the next few sections go over some networking tools like ping, traceroute, whois, and dig.


The next room is Nmap. Nmap is a tool used for security and port scanning, and it is really useful for information gathering about a target. If you want to know what services are running on a target, what kind of machine they’re using, what ports are open, nmap is the tool to use.

This room goes into a lot of detail about nmap, and there is a lot to know about it. For some of the answers, I was going to either the man page or nmap.org to learn more about it. There are a lot of things that no one is going to be able to memorize, so knowing about those two resources are beneficial.



The third room is Network Services. This room goes over different services and the various misconfigurations those services can potentially have. First on the list to go over is Server Message Block, a communication protocol meant for sharing access to resources on a server. After learning a little bit about it, I went on to enumerate a machine running SMB, and learned a little bit about exploiting it to get a flag.

The next protocol is telnet, a protocol that allows for remote access similar to ssh. Unlike ssh though, telnet is unencrypted. This part goes through much of the same thing, where I first learn about the protocol, enumerate a machine that has it, and then exploit it. The last protocol in this section is FTP, file transfer protocol.


And the final section is a ‘sequel’ to the last one, where I also go over some protocols, go through enumerating a machine with the protocol, and learn how to exploit it. First up is NFS, network file system. NFS is a protocol that allows users to access files as if they were local to their systems. NFS does this by mounting the files on a server.

The next protocol it goes over is SMTP, Simple Mail Transfer Protocol. SMTP is responsible for three things:

Verifies who is sending emails
Sends the outgoing mail
Sends it back if it does not get delivered

It works with POP, post office protocol, and IMAP, internet message access protocol, which are used for retrieving emails. The last part is going over, enumerating, and exploiting MySQL, a database management system.

This section was a lot of fun, and a lot more challenging than the last two days. It was a great introduction to networking protocols, and I really liked using the last two sections to learn a little bit about each and how to exploit them.


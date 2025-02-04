---
layout: post
title: "30 Days of THM: Day 4"
date: 2024-06-11 10:00:00 +0000
categories: [post, hacking, tryhackme]
tags: [post, hacking, tryhackme, medium]
---

30 Days of TryHackMe: Day 4
Welcome to 30 Days of TryHackMe, where I try to build up a streak of 30 days of learning using the popular website, TryHackMe. Today is Day 4, and I am still on the complete beginners pathway. Yesterday was the Network Exploitation Basics section. Today, I am doing the Web Hacking Fundamentals section, and I am excited to continue on this path.


The first section is the basic introduction of how websites work. The two major components are the front end (what you see), and the back end (the server processing the requests your browser makes). When you go to a website, you make a request through a browser. The browser can be called the client. The website’s server responds to that request. It goes over HTML, Javascript, and an HTML injection.


The second section goes over HTTP in detail. The Hyper Text Transfer Protocol, this is how we communicate with websites. There is also HTTPS, which is the secure version of HTTP (meaning it is encrypted).

It goes over HTTP requests, the different methods (GET, POST, PUT, and DELETE), the status codes, headers, cookies, and how to make requests.


And then the next room goes over the security tool Burp Suite. This room is all about learning what Burp Suite is and what it does, while future rooms (that are not part of this section) are more about using Burp Suite. At it’s most basic form, Burp Suite allows a user to intercept an HTTP request and manipulate it. There are a few version of Burp Suite, but the free one, Burp Suite Community Edition, is the one used in this room.


The features of Burp Suite Community are:
1. Proxy: allows the interception and modification of HTTP requests.

2. Repeater: allows the capture and resending of multiple requests.

3. Intruder: allows spraying endpoints with requests, common in brute-force attacks.

4. Decoder: Can decode captured information and encode payloads.

5. Comparer: Can compare two pieces of data at either word or byte-level.

6. Sequencer: Analyzes the randomness of things like cookies.

The room goes into the basics of Burp Suite, how to use it and configure it, but future rooms will go into greater detail.


The next room goes into detail about the OWASP Top 10, the 10 most critcal web vulnerabilities. Those vulnerabilities are:
1. Broken Access Control: a user being able to view something on a website that they aren’t supposed to view. For example, being able to view an administrative page.

2. Cryptographic Failure: Cryptography ensures that any data, either being sent or being stored, is encrypted.

3. Injection: When the application interprets user controlled input as commands or parameters. SQL Injections and command injections are common types of injections.

4. Insecure Design: The design of the application itself is flawed.

5. Security Misconfiguration: There is a part of the application that has been configured incorrectly.

6. Vulnerable and Outdated Components: Using a program with a well-known vulnerability.

7. Identification and Authentication Failures: Gaining access to other user’s accounts through weak authentication.

8. Software and Data Integrity Failures: The data has been modified in some way.

9. Security Logging and Monitoring Failures: No logs to determine what an attacker might have done.

10. Server-side request forgery: An attacker causes a server to send requests to unintended destinations.


The next room is OWASP Juice Shop, which goes over the different vulnerabilities in a web application. This one is kind of like a tutorial of a vulnerable machine, where you are being pointed to on where to go next. This also gives you more experience with Burp Suite.

The neat thing about this room is that it also has a score board, and if you want you can solve more challenges after the room is complete.


The next room is Upload Vulnerabilities, which is all about file upload vulnerabilities in websites. This section went into great detail about all the ways mechanisms for uploading files can be exploited. How files can be overwritten, used for RCE, how websites use client-side and server-side filtering to make sure the proper files are uploaded and how that can be worked around. It went over file signatures and how to manipulate that (for the challenge I used the Wikipedia article here).

And the final project in this room is using all of the above content to breach a room with no hand holding. I found this part to be really difficult, but when I finally got it I felt so good about it.


The final part of this section is Pickle Rick, a CTF where you have to find three flags in order to turn Rick back into a human.


As a bit of a mini-waltkthrough, here is mine:

Once I had the machine setup, I went to the web application to poke around. I opened up the page and looked at the HTML and found the username.


I then used nmap to look at the first 1000 ports, and saw that port 22 (ssh) and port 80 (http) were open.


With a few more scans of the website, I saw that there was a robots.txt file.


I have no idea what this means, I have seen maybe two episodes of Rick and Morty in my life. But, maybe a password? I tried to use ssh to get into the machine, but that was denied. So maybe there was another thing?

With a little bit more enumeration, I saw there was an /assets directory. In there, there was a file called portal.jpg. A little bit more enumeration showed me there was a /portal.php page. I imputed the credentials I found and was in.


A place I could execute some code. I first ran whoami, pwd, and ls to figure out which user I was, the directory I was in, and the contents of that directory, and I saw the first flag.

I tried to open the file, but I found that was disabled. At least with the cat command. With another command, I was able to open it. I used that same command to open the file clue.txt which says to look around the file system. I found the second ingredient in the home directory. I found the last ingredient in the root directory.

This section was a pretty involved section, and it did take me a lot longer to complete than just a day. I will still consider this ‘day 4’ because ’30 Sections of TryHackMe’ just didn’t sound as good.


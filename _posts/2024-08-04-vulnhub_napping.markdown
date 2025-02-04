---
layout: post
title: "Vulnhub Napping Walkthrough"
date: 2024-08-04 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

Napping is a vulnerable machine found on Vulnhub. This post will be a walkthrough of the machine.


The first thing to do is to get the IP address of the vulnerable machine. To do this, I just ran an nmap scan of my network. After getting the IP Address of 192.168.191.15, I ran another nmap scan to see what ports were open.


Port 22 for SSH and port 80 for HTTP. Looking at this, I figured that the most likely way would be to find vulnerabilities on the web server to make the initial access. Before doing anything else, I took a look at the web server.


There was a log in page. I clicked on the sign up now button to take a look at the sign up process.



After creating an account, I logged in.


And while this was going on, I decided to do a few scans of the web app to look for any interesting directories. That didn’t really come up with anything useful though.



I decided to play around with the Blog Link submit form.



Looking at the page source, I saw that when clicking on the link, it will open up a link in a new page. This is a blog post that details the `target=”_blank”`. I found this blog post detailing how this could be a potential vulnerability. When the user clicks on a link, they are sent to a new tab, and the new link gains partial access if the ‘window.opener’ object is in the link’s own page source.

To exploit this, I first created a login page with the exact source code as the target’s login page.


Then, I created another html page with the ‘window.opener’ object.


Set up a netcat listener:


Then I set up a web server and submitting a link to test.html in the target page.


Then I clicked on the link, went back to the page, and on the listener, I caught the credentials.


I then used ssh to login to the target machine.


Daniel is part of a group called ‘administrators’. Poking around the machine, there is another user, ‘adrian’, and there is a file called ‘query.py’ in his directory.


‘Ls -l’ basically lists all the information about the file. The permissions, the owner, etc. The permissions for this file state that the group (administrators) can write to the file. I just added a few lines to the file to create another netcat listener, this time for the ‘adrian’ user.


And then I ran ‘sudo -l’ to see what I could do as this user, and I found that ‘adrian’ could run vim as sudo. Looking at GTFObin for vim, I found what I needed to do to reach the root user.


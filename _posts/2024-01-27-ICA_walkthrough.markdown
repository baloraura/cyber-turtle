---
layout: post
title:  "A Secret Mission: ICA Vulnerable Machine Walkthrough"
date:   2023-06-05 17:53:33 -0500
categories: [post, hacking]
tags: [post, hacking, medium]
---

“According to information from our intelligence network, ICA is working on a secret project. We need to find out what the project is.

Once you have the access information, send them to us. We will place a backdoor to access the system later. You just focus on what the project is. You will probably have to go through several layers of security. The Agency has full confidence that you will successfully complete this mission. Good Luck, Agent!”


ICA: 1 is a vulnerable machine found on Vulnhub, rated easy. From the description, I knew this was a top secret mission only the top agents in the world could undertake.

To start out, after getting the machine set up on VirtualBox, I made sure it was attached to the same network as my Kali machine.

To do that, I used the nmap command. I like to ouput the results of those scans to a file.


Ports 22, 80, and 3306 were open. I went to the http website to see what I could find.


I inspected the HTML and JavaScript to see if there was anything I could find, but nothing there stood out to me.

The next step was to check the versions of the services that were running, see if there were any vulnerabilities I could exploit. I used nmap again, this time with -sV to get the versions.


And then I did a dirb scan of the website, to find some directories worth looking into.


While that was going on, I researched some vulnerabilties for those versions. I didn’t find anything useful, although that version of apache httpd did have a pretty serious vulnerability, I didn’t think I would be able to exploit it.

I instead decided to look for vulnerabilities in QDPM, the web project management service the website was running. I used searchsploit to try to find something.


Password exposure stood out to me, and it was for the version this website was running too. I looked further into it.


“Password and connection string for the database are stored in a yml file”. This website was also running mysql, so maybe I could find some credentials there. I tested that out and went to the the link in the description: http://192.168.191.6/core/config/databases.yml in this case, and I found what I was looking for.


With a username and password, it was time to try to look around the database. I used mysql to get in, and with that username and password I was able to gain access.


And then it was time to poke around. First thing to do was to see the databases.


Staff looked the most intriguing. I selected staff, and I did find some really juicy information.


Usernames and passwords. The machine was also running ssh, so I might have just found my way in.

First thing to do was to get all the usernames into a text file. I did that using leafpad, but any text editor of choice would do. The second thing to do was to decode the passwords. I did that on the command line, but any online decoder (like Cyberchef) would do.


Then, it was time to use the usernames and passwords to brute force the login. I did that using hydra.


Now I had some passwords that worked, so it was time to get in. I used Travis and his credentials to get in. Although seeing as how Dexter’s role was as a Cyber Security Analyst, maybe I should have used his credentials for the irony?


I was in, and I immediately used the ls command to see what I could find.


Hooray! The first flag! I transfered it over to my Kali machine using scp, and now it was time to get the root flag.

I had another look around. I looked to see what kind of active processes were running and checked to see if I could get the /etc/shadow file (the file with all the hashed passwords).


Some screenshots of my reconnaissance

And then I ran this little command, and found something very interesting.


So this is the find command, with specific parameters to find binaries to run as the owner, not the user who started them. This is a useful privilege escalation technique, because those binaries can be used to gain higher access.

That first binary, /opt/get_access, looked really promising.


I first ran it, and got this output. I used the strings command to take a look at the binary’s human readable parts (I did not take a screenshot, but I should have). Using the strings command on binaries is useful because you can see if the binary calls on other binaries. If their full path isn’t used, then that is a potential security risk.

So this /opt/get_access binary was calling on the cat command. Seeing an exploit, I created a bash script, put it in a file called cat and changed it so it was executable.


There’s actually a big mistake here
I then changed the PATH variable to include the /tmp directory. When you type a command, ‘ls’ for instance, Linux looks in the PATH variable to see where that binary might be located. Adding the /tmp directory means when the ‘cat’ command is called, it’ll look in there instead of the usual place.


I then called on the /opt/get_access binary in order to get to root.

Unfortunately, the first time I did it, I made a mistake and put ‘bin/bash’ instead of ‘/bin/bash’ in the binary. So I ran the command a few dozen times and wondered why my sinister plot to gain root access had failed.

I did a quick fix, and then when I called on it again, I became root.


I had to use the full path for the ‘cat’ command, due to my previous machinations, but I got the root flag.


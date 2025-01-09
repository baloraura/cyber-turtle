---
layout: post
title:  "Under the Wire Cyborg Walkthrough"
date:   2023-05-02 17:53:33 -0500
categories: [post, hacking]
tags: [post, hacking]
---

In this post, I am going to be continuing my PowerShell training with a walkthrough of Under the Wire’s Cyborg. The goal of each level is to find the password for the next level using PS. I already have a walkthrough of the previous game, Century. To play these games, you will need an SSH client. I used the recommended Putty.


Cyborg 0
The credentials to join the game are on UTW’s slack channel. I connected to cyborg.underthewire.tech on port 22, and entered the username and password. After I did that, I was in, and was ready to start playing the game.



Cyborg 1

“The password for cyborg2 is the state that the user Chris Rogers is from as stated within Active Directory”

The state referred to the actual state of the United States, not whether the user was enabled or disabled.

This one required that I find an Active Directory user, which I did with the ‘Get-ADUser’ cmdlet. However, by default that only gave me a limited amount of information.


I needed to specify one more thing, the state that the user was in. To do that, I just added ‘-Properties State’ to the cmdlet.


With kansas being the password, I went to the next round.

Cyborg 2

“The password for cyborg3 is the host A record IP address for CYBORG718W100N PLUS the name of the file on the desktop.”

DNS maps IP addresses to hostnames. Host A records are just the IPv4 addresses of the hostname.

The first thing I did was use ‘Get-ChildItem’ to see the file on the Desktop, which was _ipv4. I could have also used ‘ls’ or ‘dir’.


I then used get-help to see if there were any cmdlets like dig or host that would return DNS records. I found a cmdlet named ‘Resolve-DNSname’, which was exactly what I needed.


The password was 172.31.45.167_ipv4

Cyborg 3

“The password for cyborg4 is the number of users in the Cyborg group within Active Directory PLUS the name of the file on the desktop.”

I used ‘Get-ChildItem’ to see the file.


And then I just had to get the users in the group ‘cyborg’. I used a few cmdlets for that. ‘Get-ADGroup -Filter ‘Name -eq “cyborg”’’ will look for the group with the name ‘cyborg’. ‘Get-ADGroupMember’ gets the users in that group. ‘Measure-Object’ will count how many there are.


The password was 88_objects.

Cyborg 4

“The password for cyborg5 is the PowerShell module name with a version number of 8.9.8.9 PLUS the name of the file on the desktop.”

I first figured out the name of the file.


I then used the cmdlet ‘Get-Module -ListAvailable’ to see all the modules. From there, I could have just looked through them all for the version number, but I wanted to challenge myself.


I used ‘Select-Object’ to pull out what I needed, which was the version number and name. And then I used ‘Select-String’ to find the exact version number.

The password was bacon_eggs

Cyborg 5

“The password for cyborg6 is the last name of the user who has logon hours set on their account PLUS the name of the file on the desktop.”

I used Get-ChildItem to get the name of the file


I needed the last name of a user who has logon hours set. I just took a wild guess and used ‘Get-ADUser -Filter {logonHours -like ‘*’}, and that returned two users. I did not think it would be the administrator, so I guessed the second user.


And I was correct. The password was rowray_timer.

Cyborg 6

“The password for cyborg7 is the decoded text of the string within the file on the desktop.”

https://www.sans.org/blog/month-of-powershell-profile-hack-base64-encoding-decoding/

This one required some research on how to encode/decode base64 in PowerShell, and I included the resource I used in the link above. It also explains much better than I could how it works.

The first thing I did was open the file. The encoding was base64. I used the link above to figure out how to decode it.


The password was cybergeddon.

Cyborg 7

“The password for cyborg8 is the executable name of a program that will start automatically when cyborg7 logs in.”

https://learn.microsoft.com/en-us/windows/win32/cimwin32prov/win32-startupcommand

This one I used the above link to figure out how to get the information I needed. Basically, I was looking for the Win32_StartUpCommand, which is the class of commands that run whenever a user logs in. To access that command, I needed to use the ‘Get-WMIObject’ cmdlet.


The password was skynet.

Cyborg 8

“The password for cyborg9 is the Internet zone that the picture on the desktop was downloaded from.”

https://devblogs.microsoft.com/scripting/powertip-use-powershell-3-0-to-identify-zone-information-of-files/

For this one, I used the above link to figure out how to get to the internet zone, because coming into this I really had no idea how to find that.

Essentially for this one, it took two cmdlets.


The first one was the above cmdlet, which is using the ‘Get-Item’ cmdlet, and specifying the Stream parameter as ‘zone*’. I used the wildcard because I did not know the name yet.


After I found the name, I used the ‘Get-Content’ cmdlet to get more info.

The password was 4.

Cyborg 9

“The password for cyborg10 is the first name of the user with the phone number of 876–5309 listed in Active Directory PLUS the name of the file on the desktop.”

I used Get-ChildItem to find the name of the file.


This one took a lot of playing around with the phone number parameter. I first put in ‘phoneNumber’, for instance. I eventually figured out that I had to use the filter ‘telephoneNumber’.


The password was onita99.

Cyborg 10

“The password for cyborg11 is the description of the Applocker Executable deny policy for ill_be_back.exe PLUS the name of the file on the desktop.”

https://learn.microsoft.com/en-us/powershell/module/applocker/get-applockerpolicy?view=windowsserver2022-ps

I used Get-ChildItem to find the name of the file.


There is a helpful cmdlet that gets the Applocker policy. It is called Get-ApplockerPolicy. I used the above link to figure out how to use it in this context.


This essentially just puts it into an XML format. XML is a format for storing data.

The password was terminated!99.

Cyborg 11

“The password for cyborg12 is located in the IIS log. The password is not Mozilla or Opera.”

Through the power of Google, I have found that the IIS logs are located in c:\inetpub\logs\LogFiles. When I went there, there was one directory. I went into that directory, and found one file. I used the cmdlet ‘Get-Content’, then used the ‘Select-String’ cmdlet to exclude anything that didn’t match Opera or Mozilla.


The password was spaceballs.

Cyborg 12

“The password for cyborg13 is the first four characters of the base64 encoded full path to the file that started the i_heart_robots service PLUS the name of the file on the desktop.”

https://adsecurity.org/?p=478

I used the above link to figure out how to encode in base64 in PowerShell.

I first used Get-ChildItem to find the name of the file.


I then used the below cmdlet to figure out the path name.


I followed along with the above link to figure out what to do next.


The password was ywa6_heart.

Cyborg 13

“The password cyborg14 is the number of days the refresh interval is set to for DNS aging for the underthewire.tech zone PLUS the name of the file on the desktop.”

I used Get-ChildItem for the file on the desktop.


Then, using get-help, I found a useful cmdlet, ‘Get-DnsServerZoneAging’, which was exactly what I needed.


The password was 22_days

Cyborg 14

“The password for cyborg15 is the caption for the DCOM application setting for application ID {59B8AFA0–229E-46D9-B980-DDA2C817EC7E} PLUS the name of the file on the desktop.”

I used Get-ChildItem to find the file on the desktop.


The next step took a lot of googling as well, but I found that using the below cmdlet to get the DCOM application setting. The hardest part was figuring out how the filter for the application ID would work. To figure that out, I just ran the below cmdlet without the filter to see what field the application ID would be named.


The password was propshts_objects.


---
layout: post
title: "Active Directory Help Desk Lab"
date: 2023-04-20 10:00:00 +0000
categories: [post, active directory]
tags: [post, active directory, medium]
---

Active Directory Help Desk Lab: Using PowerShell
In this blog post, I wanted to test my current knowledge of Active Directory and PowerShell in order to emulate a few real world scenarios. While the last few posts were all about installing and learning the basics, this post will be about implementing those basics in a real world scenario.

Changing Password

The first ‘lab’ or scenario that I want to do is a very simple password reset for a user. People forget their passwords all the time, they reuse passwords, they change passwords only slightly when they have to (think changing password to Password), they leave their passwords on a sticky note right next to their computer (or in a file on a desktop).

One thing that people do often is forget their passwords and get locked out of their own accounts. So for this scenario, a user gets locked out of their account and needs their password reset. The goal is to just reset a user’s password.

The first thing I need to do is get an unlucky user from my AD. I could just look at the list of users and grab somebody at random. However, to make it a little bit more interesting, and to keep experimenting with PowerShell, I want to use a script.


A simple cmdlet saved as a .ps1 file. Get-ADUser -Filter * just gets all the AD users, and Get-Random picks out a random object. When I run it for the first time, it gives me this:


Hildegarde Mortenson has been locked out of their account, and needs their password reset.

Doing a little bit of research on resetting a user’s password, I found that you need to have a password that it is going to be reset to, and the user’s identity. I already have the identity, so now I just need to set the password.

First, to set the password, you need to do something like this:


In the screenshot, I’m essentially just setting a password variable, that way when I do the cmdlet to change passwords, I just call that variable.

Next, I need to use this command to set the password for Hildegarde:


The next part is optional, but in a real world scenario, this password would obviously need to be changed. So I used the below cmdlet to force Hildegarde to change the password at logon.


In a previous lab, I think I set passwords to never expire, which is why I had to do the first command in the screenshot. So for Hildegarde, the password will expire, and she will need to change it at the next logon.

To test out if this worked, I booted up the Client1 virtual machine to try to log in as hmortenson.




Logging in, the new password worked, and it prompted the user to change the password. With a new password, Hildegarde can move on and do their work in peace, and hopefully she will remember the password.

Creating a New User

This challenge is going to be creating a new user using PowerShell. I’m sure there are countless scenarios where a new user needs to be added this way.

Instead of going to my script to generate a random user, I went to fakenamegenerator.com to generate a new user.


So I am creating the new user of William Shaeffer using cmdlets.


To go over the cmdlet, New-ADUser is the cmdlet to create new users, with all the parameters. -Name is the account name, -GivenName is the first name, -SamAccountName is the login name, and Account Password is the user’s password. All is well and good, however the one problem is that in the original cmdlet I forgot to enable the user.


The way to fix that is just to use the Enable-ADAccount cmdlet. Now this new employee is ready to get to work.

Creating a Group and Adding Users

For the next scenario, I’m going to be creating a group in AD. Higher management wants a whole group filled with every employee that has the letter ‘x’ in their name. They want the group to be called “The X Group”.

This task has a few steps. The first step is to get all the users with ‘x’ in their names. The second step is to create a new group, and the third step is to add all those users into that group.

So, as a disclaimer, I did try to do this all in one powershell script, but I kept on running into errors and couldn’t figure out how to do it that way.

In order to get all the members, I did this cmdlet, which is just Get-ADUser with a few filters that are looking for the letter ‘x’ in the name. I then used Select-Object SamAccountName to get only their login names. I then put it all in a text document.


After that, I created a .ps1 file that will create the group and add all the members to that group.


The powershell script is essentially just going through each user in the text file and adding it to ‘The X Group’.

I ran the script, and the group was created. When I checked the server manager, the group was there with all the members.



Doing a couple of these tasks got me a little bit more comfortable with using PowerShell alongside Active Directory. The two of them are incredibly useful skills to have in any IT job, because so many organizations use AD. PS offers users many ways to play around with AD, ways that you can’t do in the server manager.

I’m looking forward to exploring more and developing my understanding of Active Directory and PowerShell.


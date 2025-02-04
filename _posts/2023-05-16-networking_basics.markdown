---
layout: post
title:  "Under the Wire Cyborg Walkthrough"
date:   2023-05-16 17:53:33 -0500
categories: [post, networking]
tags: [post, networking, medium]
---

Networking Basics: Network Types
Networking is how computers communicate with each other, how they share and use resources, how they connect to the internet. It’s an important concept for anybody working in IT to understand because it’s critical for business or personal use.

In these series of articles, I’m going to try to explain different networking concepts.I’d like to share what I’ve learned so far, in an effort to encourage understanding and dialogue.


Photo by Jordan Harrison on Unsplash
For this post, I will first describe the types of networks, and then I will conduct an experiment to demonstrate how a LAN works.

What is a network?

To define a network simply, in computer terms, it’s a collection of devices that can communicate with each other and share resources.


Photo by Marvin Meyer on Unsplash
Local Area Network

A local area network is a group of devices in the same geographic area, usually in the same building. LANs are designed for fast data transfer, and they’re cheaper to maintain. As an example, an office building with many different devices that are all connected to each other.

As another example, when I was a kid we had things called LAN parties. My friends and I would bring our computers to the same physical location to play games together.

There are wired LANs and wireless LANs. Wired LANs use switches and ethernet cables to connect to each other, while wireless LANs use wireless technology. Wi-Fi is an example of wireless LAN technology.

Wide Area Network

A WAN spans over a large geographic area. It can span across countries, and can cover the globe. The Internet is an example of a WAN.

Personal Area Network

A PAN connects smartphones, tablets, laptops, and other devices together on a personal level. They are typically wireless, although they are wired as well. If you are using bluetooth, then you are using a PAN.


Photo by Brett Jordan on Unsplash
Metropolitan Area Network

This is a network that connects LANs that span across a city or a town. They typically use fiber optics for their connections.

Storage Area Network

A SAN is a network dedicated to data storage. It is partitioned off from the rest of the network so it isn’t affected by network traffic.

Campus Area Network

CANs connect multiple LANs in similar geographic areas, such as a university campus. These can also be called Corporate Area Networks, as they can be used in large corporate networks as well.


Photo by Parker Gibbons on Unsplash
To demonstrate how a LAN works and the kind of traffic that it generates, I set up a few virtual machines on my virtualbox to act as a LAN.

I first installed a few machines: a Linux Lite, a Windows, and a Ubuntu Desktop. I made sure that each one had the necessary protocols and ports open. I made sure SSH was running on each of the Linux machines, and I installed apache2 on my Linux Lite machine.

After all three were installed and ready to go, I played around with the networking settings. I tried a few settings, and for this particular experiment, I created a host-only network so that each machine could only communicate with each other.


The initial setup
When all three were up, I pinged each machine a few times to see if they could communicate with each other. And after that, I used SSH for each machine a few times and sent files over to each machine.

To take the experiment a little bit further, I wanted to set up a DNS server on the Linux Lite machine. I first powered off all the machines. Then I connected the Linux Lite machine to a bridged adapter, so it had internet connectivity to install software. I kept the host-only adapter as well, so that it was connected to my LAN.

Dnsmasq is intended to provide DNS support for LANs, which is perfect for this experiment. For those who do not know, DNS is the protocol that maps IP addresses to domain names. It is the reason why you don’t have to memorize IP addresses when you are browsing the web.

The first thing I did was stop the systemd-resolved service, which would conflict with dnsmasq. Systemd-resolved essentially provides DNS.

Then I ran ‘sudo apt update’ and ‘sudo apt install dnsmasq’ to install dnsmasq.


After that, I played around with the configuration file. The important part here was making sure it was listening for DNS requests on the interface for my LAN. Then I added the new addresses, naming the Windows machine ‘windows.testlan’ and naming the Ubuntu machine ‘ubuntu.testlan’.



After that was done, I went to the Windows machine to configure the DNS for that service, so it uses my new DNS server. I went to ‘Control Panel’, ‘Network and Internet’, then to ‘Network and Sharing Center’. I clicked on ‘Change Adapter Settings’, right clicked on the connection (ethernet in this case), clicked on properties, went to ‘Internet Protocol Version 4 (TCP/IP)’, and then added the IP address of my DNS server there.


How to do it on Windows
The Ubuntu machine was similar. I clicked on the connection icon in the right hand corner, clicked on ‘wired connection’ , then went to Settings, clicked on IPv4, and set the DNS server to be my own.


How to do it on Ubuntu
After everything was set up, I went to my Windows machine to ping the Ubuntu machine using the address, to make sure that it worked properly. I did the same thing from my Ubuntu machine.


And then I used SSH to get into each machine using their new addresses.


In a practical sense, configuring a DNS server on a LAN can make accessing the resources of each machine much easier. Even in this experiment, I had to have the IP addresses and the machines they belonged to written down. It was a lot easier after the DNS server was setup. If there was a LAN with a lot more than just three machines, like in an office setting, proper DNS configuration would be beneficial just for everyday use.

In the future, I want to take this LAN setup further by installing additional services to experiment with them, attempting a penetration test of all the machines, and even intentionally cause network problems to troubleshoot.


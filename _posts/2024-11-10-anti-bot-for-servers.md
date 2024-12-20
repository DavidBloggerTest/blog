---
layout: post
title: "Anti Bot For Servers"
date: 2024-11-10 15:58 +0800
tags: [Guide,IT,Web]
comments: true
author: DavidX
---
## Introduction
This was my first time creating a self-hosted site (this blog itself isn't strictly self-hosted). The project is [here](https://github.com/Davidasx/flask-gpt). Just a few weeks after deployment, I've already seen a load of bots trying to hack into my site -- under plain sight. 

Here's a screenshot of the log:
![](https://blog.davidx.top/images/pic2024111001.png)

## The Basis of the Attacks
They were mostly trying to hack into index.php -- a file that I don't even have. PHP combines the front-end and back-end, so it's a common target for hackers. Hacking into the front-end may also enable hackers to inject into the back-end.

Such behavior are mostly by automated scanning scripts, scanning every IP on the Internet for such vulnerabilities. Once they found one, the site will be doomed.

## Confirmation of the Scanning Method
To confirm that these were caused by bots scanning IPs, not domain names, I put the server under CloudFlare so that anyone visiting the domain name normally will be shown in the logs as from CloudFlare data centers. Any IP other than these will certainly be visitors trying to access through direct IP.

I updated the logger code (it was previously logging 127.0.0.1 because the site is behing Nginx) and let it log the real IP of visitors. Then, I let it run for a night and found three potential attackers, none of them from CloudFlare data centers.

## Potential Attackers
179.43.168.146  Private Layer  SE        PHP          

47.251.29.235   Alibaba        US        PHP          

20.118.69.180   Microsoft      US        Autodiscover 

## Fighting the Bots
A major cause was that the project was running directly at the 80/443 ports, which means that bots can access this directly without adding a custom port, making scanning much easier. I have since added another fake website that was at the top priority of the Nginx configuration, and the real site was moved to a custom port. This way, bots will have to scan all ports to find the site, which is much harder.

Code for the fake site is [here](https://github.com/Davidasx/bot). You can deploy it yourself and it will run at port 8899. Just use Nginx to proxy_pass to the port. It will return 404 for every page except the main page and display a warning message. The logs will record the visitor's IP and all will be accessible by myself. Here's an even more stupid attack attempt:
```
[2024-11-10 07:09:29] from 200.178.226.162 GET /remote/login 404
```

Let the bots come! I'm ready for them! Their IP addresses will all be recorded one by one!

The main page of the fake site:
```
Hey Bot, We've Recorded Your IP!
You'll find nothing here, bot.
Your IP Address is: xx.xx.xx.xx
Congratulations on you stepping into a trap!
```

The index.php page:
```
Hey Bot, We've Recorded Your IP!
I know that you're trying to access index.php but I'll NEVER use PHP for my website!
Your IP Address is: xx.xx.xx.xx
Congratulations on you stepping into a trap!
```
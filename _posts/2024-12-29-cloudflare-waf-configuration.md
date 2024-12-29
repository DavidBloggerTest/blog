---
layout: post
title: "Cloudflare WAF Configuration"
date: 2024-12-29 18:51 +0800
tags: [Guide,IT,Ops]
comments: true
author: DavidX
---
## Introduction

I recently bought the domain name `davidx.top` and put it under Cloudflare. However, just a few days later, the analytics showed that there were an unusual number of visitors.

![](https://blog.davidx.top/images/pic2024122901.jpg)


![](https://blog.davidx.top/images/pic2024122905.jpg)

Yes, more than 400 requests from Russia and over 600 requests in a single hour. This is definitely not human.

So I set up the WAF and tried to analyze the situation.

## My WAF Rules

Firstly, I blocked visitors from Tor and those with IP security score of 15+. Those have a really high risk of scam. I tested this by visiting the site through Tor and it was blocked.

In CloudFlare, Tor is a continent and also a country name, so it's quite easy to set up a block on it.

![](https://blog.davidx.top/images/pic2024122902.jpg)

Secondly, I set up a rule to block visitors whose UA doesn't contain `Mozilla/5.0`. I tested this by using `curl` and python's `requests` library and both were blocked. As the UA can be faked, `cloudscraper` can still easily bypass this.

To let search engines through, I let verified bots bypass this rule. (Although it seems like Googlebot and Bingbot all have `Mozilla/5.0` in their UA.)

Thirdly, I added a CAPTCHA challenge for everyone who visits the sites that are served by my own server ([flask-gpt](https://chat.davidx.top) and [free-chat](https://free-chat.davidx.top)) because they are a demo site and a free AI service respectively. I don't want them to be abused by bots (for example, reverse-engineering the AI to make it become a public API).

Finally, to analyze the problem of Russian visitors and other potential threats, I set up a rule to challenge visitors from Russia, with an IP risk score of 3+, X-Forwarded-For of `.`, or HTTP version of `1.x`. Those are all characteristics of a bot.

![](https://blog.davidx.top/images/pic2024122903.jpg)

## Results

The firewall has been unning for nearly 24 hours and here are the results.

![](https://blog.davidx.top/images/pic2024122904.jpg)

Seems like nobody from Russia are humans. None of them passed the CAPTCHA at all. The Tor-blocking rule hasn't blocked many visitors, however. Also, 300+ visitors were blocked by the UA rule, which is about the same as the number of Russian visitors. About 30 visitors were blocked by CAPTCHA on chat sites. (Among them were Bingbot. Well, it doesn't matter very much as I don't want my chat site to appear on search engines anyway.)
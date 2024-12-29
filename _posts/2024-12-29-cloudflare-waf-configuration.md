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

![](https://blog.davidx.top/images/pic2024122901.png)


![](https://blog.davidx.top/images/pic2024122902.png)

Yes, more than 400 requests from Russia and over 600 requests in a single hour. This is definitely not human.

So I set up the WAF and tried to analyze the situation.

## My WAF Rules

Firstly, I blocked visitors from Tor and those with IP security score of 15+. Those have a really high risk of scam. I tested this by visiting the site through Tor and it was blocked.

In CloudFlare, Tor is a continent and also a country name, so it's quite easy to set up a block on it.

```
(ip.src.country eq "T1") or (ip.src.continent eq "T1") or (not cf.client.bot and cf.threat_score gt 15)
```

Secondly, I set up a rule to block visitors whose UA doesn't contain `Mozilla/5.0`. I tested this by using `curl` and python's `requests` library and both were blocked. As the UA can be faked, `cloudscraper` can still easily bypass this.

To let search engines through, I let verified bots bypass this rule. (Although it seems like Googlebot and Bingbot all have `Mozilla/5.0` in their UA.)

```
(not cf.client.bot and not http.user_agent contains "Mozilla/5.0")
```

Thirdly, I added a CAPTCHA challenge for everyone who visits the sites that are served by my own server ([flask-gpt](https://chat.davidx.top) and [free-chat](https://free-chat.davidx.top)) because they are a demo site and a free AI service respectively. I don't want them to be abused by bots (for example, reverse-engineering the AI to make it become a public API).

```
(http.request.full_uri wildcard r"*://chat.davidx.top/*") or (http.request.full_uri wildcard r"*://free-chat.davidx.top/")
```

Finally, to analyze the problem of Russian visitors and other potential threats, I set up a rule to challenge visitors either from Russia, with an IP risk score of 3+, X-Forwarded-For of `.`, or HTTP version of `1.x`. Those are all characteristics of a bot.

```
(not cf.client.bot and cf.threat_score gt 3) or (not cf.client.bot and ip.geoip.country eq "RU") or (not cf.client.bot and http.x_forwarded_for eq ".") or (not cf.client.bot and http.request.version in {"HTTP/1.0" "HTTP/1.1" "HTTP/1.2"})
```

## Results

The firewall has been unning for nearly 24 hours and here are the results.

![](https://blog.davidx.top/images/pic2024122903.png)

Seems like nobody from Russia are humans. None of them passed the CAPTCHA at all. They were mostly trying to access wp-admin although my site is certainly not a WordPress site.

The Tor-blocking rule hasn't blocked many visitors, however.

300+ visitors were blocked by the UA rule, which is about the same as the number of Russian visitors. Many were using Go-http and were accessing wp-admin.

About 30 visitors were blocked by CAPTCHA on chat sites. (Among them were Bingbot. Well, it doesn't matter very much as I don't want my chat site to appear on search engines anyway.)

If you have a short domain name, consider setting up a WAF, especially if you're using WordPress. It's a must-have for security.
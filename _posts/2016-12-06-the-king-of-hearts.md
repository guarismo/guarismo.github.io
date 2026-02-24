---
layout: post
title: "The King of Hearts"
date: 2016-12-06T03:04:00.002Z
author: gu4r15sm0
---

## Metasploitable 3

### The King of Hearts:

All right, we got the Joker card, now let's look for more.
The next easy target seems to be another web server running on TCP port 8585
**8585/tcp  open  http       Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)**
Let's look at it:

[![](/assets/images/king_of_hearts_wamp.jpg)](/assets/images/king_of_hearts_wamp.jpg)

WAMP it is, this means Windows (we knew), Apache (we also knew), MySQL (good to know), PHP (yes we knew)

Clicking around gave us a lot of Forbidden blah..blah..blah... except, **uploads** which is empty and **wordpress:**

[![](/assets/images/wp-metasploitable3.jpg)](/assets/images/wp-metasploitable3.jpg)

Here's an image with the flags we should find - 15 in total it seems

Look at the Top Right corner!! 

[![](/assets/images/king_of_hearts.jpg)](/assets/images/king_of_hearts.jpg)

**KING OF HEARTS**

We got 2 without metasploit.
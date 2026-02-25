---
layout: post
title: "The Jack of Hearts"
date: 2017-01-06T19:34:00.001Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

## Metasploitable 3

### The Jack of Hearts

I ssh'ed into the server with the Administrator credentials previously found.


```bash
root@igor-kali:~# ssh Administrator@10.20.10.19

Administrator@10.20.10.19’s password:
```
```


Permission denied, please try again.


```text
Administrator@10.20.10.19’s password:

\-sh-4.3$

\-sh-4.3$ pwd

/cygdrive/c/Users/Administrator
```


I started navigating through the folders and ended up in Users/Public/Documents and looking at the list of files I found a Word .docx file.

\-sh-4.3$ cd Public

\-sh-4.3$ ls

Desktop  Documents  Downloads  Favorites  Libraries  Music  Pictures  Videos  desktop.ini

\-sh-4.3$ cd Documents/

\-sh-4.3$ ls

My Music  My Pictures  My Videos  desktop.ini  jack_of_hearts.docx  seven_of_spades.pdf

I downloaded the file with ***scp*** and doubled click on the file in Kali Linux and since I don’t have any Application to read .docx files, it saw it as a Zip file and opened in the Archive Manager applications

[![](/assets/images/jack_of_hearts-docx.jpg)](/assets/images/jack_of_hearts-docx.jpg)

Archive Manager

[![](/assets/images/jack_of_hearts.jpg)](/assets/images/jack_of_hearts.jpg)

Happy guy!
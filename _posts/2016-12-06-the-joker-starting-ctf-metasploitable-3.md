---
layout: post
title: "The Joker: Starting CTF - Metasploitable 3"
date: 2016-12-06T02:35:00.002Z
author: gu4r15sm0
---

## **Playing with Metasploitable 3**

**So, a good friend told me the nice people of Rapid7 just published a new Catching the Flag virtual machine called [Metasploitable 3](https://community.rapid7.com/community/metasploit/blog/2016/11/15/test-your-might-with-the-shiny-new-metasploitable3) and I decided to take a look and here are my findings:**

**First step, let's see what ports are open:**

**Using my Kali VM a run nmap agains the metasplotable3 machine to see what I can find:**


```bash
root@igor-kali:~# nmap -sV -sT 10.20.10.19 -p0-65535
Starting Nmap 7.25BETA2 ( https://nmap.org ) at 2016-12-04 21:19 EST
Nmap scan report for 10.20.10.19
Host is up (0.00073s latency).
Not shown: 65519 filtered ports
PORT      STATE SERVICE    VERSION
21/tcp    open  ftp        Microsoft ftpd
22/tcp    open  ssh        OpenSSH 7.1 (protocol 2.0)
80/tcp    open  http       Microsoft IIS httpd 7.5
1617/tcp  open  unknown
3000/tcp  open  http       WEBrick httpd 1.3.1 (Ruby 2.3.1 (2016-04-26))
4848/tcp  open  ssl/http   Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
5985/tcp  open  http       Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8022/tcp  open  http       Apache Tomcat/Coyote JSP engine 1.1
8080/tcp  open  http       Oracle GlassFish 4.0 (Servlet 3.1; JSP 2.3; Java 1.8)
8282/tcp  open  http       Apache Tomcat/Coyote JSP engine 1.1
8484/tcp  open  http       Jetty winstone-2.8
8585/tcp  open  http       Apache httpd 2.2.21 ((Win64) PHP/5.3.10 DAV/2)
9200/tcp  open  http       Elasticsearch REST API 1.1.1 (name: Ziggy Pig; Lucene 4.7)
49153/tcp open  msrpc      Microsoft Windows RPC
49154/tcp open  msrpc      Microsoft Windows RPC
49195/tcp open  unknown
49197/tcp open  tcpwrapped
Interesting ports...
```


I'm not going to try any logins yet...

Let's see that web site on port 80:

[![](/assets/images/hahaha.jpg)](/assets/images/hahaha.jpg)

Hmmm... Where's Batman?!?

### The Joker Card:

Let's look at the source:

[![](/assets/images/joker-index.jpg)](/assets/images/joker-index.jpg)

 OK... so we have a huge HTML with some ASCII data, let's get ourselves a copy of this data to analyze it, we can just Save the file or do a curl command:

 root@igor-kali:~#  curl http://10.20.10.19/ > data.html

Now we can see that just before the data there are the characters **value="**  and after the data **">** , so let's make a regex to only get the data we need and save it in another file:

root@igor-kali:~# cat data.html | perl -ne 'print $1 if(/value\=\"(\w+)\"\>/g)' > data.hex

Looking better at the data we noticed that is not just ACII data, is HEXADECIMAL :)

So now we can use something like this to transform the data into a binary:

file: hex_to_bin.py

**import binascii**

**with open('data') as f, open('data.bin', 'wb') as fout:**

    **for line in f:**

        **fout.write(**

            **binascii.unhexlify(line)**

        **)**

This will leave the result in the data.bin file


```bash
root@igor-kali:~# python hex_to_bin.py

root@igor-kali:~# file data.bin
```


data.bin: PNG image data, 521 x 729, 8-bit/color RGBA, non-interlaced
```


Aha... a PNG file, let's open it:

[![](/assets/images/joker.jpg)](/assets/images/joker.jpg)

Cool, let's keep looking for cards then :) And we didn't use Metasploit for this one.
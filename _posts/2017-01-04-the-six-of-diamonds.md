---
layout: post
title: "The Six of Diamonds"
date: 2017-01-04T15:57:00Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

## Metasploitable 3

### The Six of Diamonds

Still using the same meterpreter session exploited in Elasticsearch we can use the search command to find cards if we know their names.

```text
meterpreter > search -f *diamonds*
Found 6 results...
    C:\jack_of_diamonds.png
    C:\Program Files\OpenSSH\home\Public\Pictures\ten_of_diamonds.png (406134 bytes)
    C:\Users\Public\Pictures\ten_of_diamonds.png (406134 bytes)
    C:\inetpub\wwwroot\six_of_diamonds.zip (384916 bytes)
    C:\vagrant\resources\flags\six_of_diamonds.zip (384916 bytes)
    C:\vagrant\resources\flags\ten_of_diamonds.png (406134 bytes)
meterpreter >
```


Looking at the path it seems the six_of_diamonds.zip file is probably on the main IIS website on port 80 so I downloaded it from there.


```bash
root@igor-kali:~# wget http://10.20.10.19/six_of_diamonds.zip
--2016-12-09 23:37:15-- http://10.20.10.19/six_of_diamonds.zip
Connecting to 10.20.10.19:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 384916 (376K) [application/x-zip-compressed]
Saving to: 'six_of_diamonds.zip'
six_of_diamonds.zi 100%[================>] 375.89K  --.-KB/s    in 0.003s
2016-12-09 23:37:15 (105 MB/s) - 'six_of_diamonds.zip' saved [384916/384916]
```

 I proceeded to use ‘unzip’ but it prompted me for a password, I tried a couple and failed, then I guessed the password. (tip: it's always the same password)


```bash
root@igor-kali:~# unzip six_of_diamonds.zip
Archive:  six_of_diamonds.zip
[six_of_diamonds.zip] six_of_diamonds.png password:
password incorrect--reenter:
password incorrect--reenter:
  extracting: six_of_diamonds.png
```

[![](/assets/images/Six_of_Diamonds.jpg)](/assets/images/Six_of_Diamonds.jpg)

That's a creepy looking dude
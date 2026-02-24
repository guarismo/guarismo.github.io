---
layout: post
title: "The Six of Diamonds"
date: 2017-01-04T15:57:00Z
author: gu4r15sm0
---

## Metasploitable 3

### The Six of Diamonds

Still using the same meterpreter session exploited in Elasticsearch we can use the search command to find cards if we know their names.

meterpreter > search -f \*diamonds\*

Found 6 results...

    C:\\jack\_of\_diamonds.png

    C:\\Program Files\\OpenSSH\\home\\Public\\Pictures\\ten\_of\_diamonds.png (406134 bytes)

    **C:\\Users\\Public\\Pictures\\ten\_of\_diamonds.png** (406134 bytes)

    **C:\\inetpub\\wwwroot\\six\_of\_diamonds.zip** (384916 bytes)

    C:\\vagrant\\resources\\flags\\six\_of\_diamonds.zip (384916 bytes)

    C:\\vagrant\\resources\\flags\\ten\_of\_diamonds.png (406134 bytes)

meterpreter >

Looking at the path it seems the six\_of\_diamonds.zip file is probably on the main IIS website on port 80 so I downloaded it from there.

**root@igor-kali**:**~**\# wget http://10.20.10.19/six\_of\_diamonds.zip

\--2016-12-09 23:37:15--  http://10.20.10.19/six\_of\_diamonds.zip

Connecting to 10.20.10.19:80... connected.

HTTP request sent, awaiting response... 200 OK

Length: 384916 (376K) \[application/x-zip-compressed\]

Saving to: ‘six\_of\_diamonds.zip’

six\_of\_diamonds.zi 100%\[================>\] 375.89K  --.-KB/s    in 0.003s 

2016-12-09 23:37:15 (105 MB/s) - ‘six\_of\_diamonds.zip’ saved \[384916/384916\]

 I proceeded to use ‘unzip’ but it prompted me for a password, I tried a couple and failed, then I guessed the password. (tip: it's always the same password)

**root@igor-kali**:**~**\# unzip six\_of\_diamonds.zip

Archive:  six\_of\_diamonds.zip

\[six\_of\_diamonds.zip\] six\_of\_diamonds.png password:

password incorrect--reenter:

password incorrect--reenter:

 extracting: **six\_of\_diamonds.png**     

[![](/assets/images/Six_of_Diamonds.jpg)](/assets/images/Six_of_Diamonds.jpg)

That's a creepy looking dude
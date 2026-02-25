---
layout: post
title: "The King of Diamonds (damonds??)"
date: 2016-12-06T03:22:00.002Z
author: gu4r15sm0
---

## Metasploitable 3 

### The King of Diamonds:

So we have a WAMP server running Wordpress:
Let's throw a wpscan at it and try to guess the password of the admin user
﻿

root@igor-kali:~# wpscan --url 10.20.10.19:8585/wordpress --wordlist /usr/share/wordlists/fasttrack.txt --username admin
\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
        \_\_          \_\_\_\_\_\_\_   \_\_\_\_\_                  
        \\ \\        / /  \_\_ \\ / \_\_\_\_|                 
         \\ \\  /\\  / /| |\_\_) | (\_\_\_   \_\_\_  \_\_ \_ \_ \_\_  
          \\ \\/  \\/ / |  \_\_\_/ \\\_\_\_ \\ / \_\_|/ \_\` | '\_ \\ 
           \\  /\\  /  | |     \_\_\_\_) | (\_\_| (\_| | | | |
            \\/  \\/   |\_|    |\_\_\_\_\_/ \\\_\_\_|\\\_\_,\_|\_| |\_|


```bash
        WordPress Security Scanner by the WPScan Team 
                       Version 2.9.1
          Sponsored by Sucuri - https://sucuri.net
   @_WPScan_, @ethicalhack3r, @erwan_lr, pvdl, @_FireFart_
```

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

[+] URL: http://10.20.10.19:8585/wordpress/
[+] Started: Sun Dec  4 22:39:28 2016

[!] The WordPress 'http://10.20.10.19:8585/wordpress/readme.html' file exists exposing a version number
[!] Full Path Disclosure (FPD) in 'http://10.20.10.19:8585/wordpress/wp-includes/rss-functions.php': C:\\wamp\\www\\wordpress\\wp-includes\\rss-functions.php
[+] Interesting header: LINK: <http://10.20.10.19:8585/wordpress/wp-json/>; rel="https://api.w.org/"
[+] Interesting header: SERVER: Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2
[+] Interesting header: SET-COOKIE: nf_wp_session=490fe120af9b2724d7460db8489034de%7C%7C1480910607%7C%7C1480910547; expires=Mon, 05-Dec-2016 04:03:27 GMT; path=/wordpress/
[+] Interesting header: X-POWERED-BY: PHP/5.3.10
[+] XML-RPC Interface available under: http://10.20.10.19:8585/wordpress/xmlrpc.php
[!] Upload directory has directory listing enabled: http://10.20.10.19:8585/wordpress/wp-content/uploads/
[!] Includes directory has directory listing enabled: http://10.20.10.19:8585/wordpress/wp-includes/

[+] WordPress version 4.6.1 identified from advanced fingerprinting (Released on 2016-09-07)

[+] WordPress theme in use: twentyfourteen - v1.8

[+] Name: twentyfourteen - v1.8
 |  Latest version: 1.8 (up to date)
 |  Location: http://10.20.10.19:8585/wordpress/wp-content/themes/twentyfourteen/
 |  Readme: http://10.20.10.19:8585/wordpress/wp-content/themes/twentyfourteen/readme.txt
 |  Style URL: http://10.20.10.19:8585/wordpress/wp-content/themes/twentyfourteen/style.css
 |  Theme Name: Twenty Fourteen
 |  Theme URI: https://wordpress.org/themes/twentyfourteen/
 |  Description: In 2014, our default theme lets you create a responsive magazine website with a sleek, modern des...
 |  Author: the WordPress team
 |  Author URI: https://wordpress.org/

[+] Enumerating plugins from passive detection ...
[+] No plugins found
[+] Starting the password brute forcer
  Brute Forcing 'admin' Time: 00:01:03 <=====================================================================  > (181 / 183) 98.90%  ETA: 00:00:01

  +----+-------+------+----------+
  | Id | Login | Name | Password |
  +----+-------+------+----------+
  |    | admin |      |          |
  +----+-------+------+----------+

[+] Finished: Sun Dec  4 22:40:34 2016
[+] Requests Done: 225
[+] Memory used: 14.797 MB
[+] Elapsed time: 00:01:06

OK, we didn't get the password from the fasttrack list so I'm not going to bother right now with that, let's see what else we got that is easy to examine:

Another uploads folder:

[!] Upload directory has directory listing enabled: http://10.20.10.19:8585/wordpress/wp-content/uploads/

Let's see what's there:

[![](/assets/images/king_of_diamonds_upload.jpg)](/assets/images/king_of_diamonds_upload.jpg)[![](/assets/images/king_of_diamonds.jpg)](/assets/images/king_of_diamonds.jpg)

As promised, full directory listing and there it is... **The King of Diamonds** (or Damonds?!?!)

No metasploit used yet.
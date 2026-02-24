---
layout: post
title: "The Four of Clubs"
date: 2017-01-06T19:20:00Z
author: gu4r15sm0
---

## Metasploitable 3

### The Four of Clubs

*Using the meterpreter’s search command I looked for the four of clubs and downloaded a WAV file.*

meterpreter > search -f four\_of\_clubs\*

Found 3 results...

    C:\\Program Files\\OpenSSH\\home\\Public\\Music\\four\_of\_clubs.wav (550302 bytes)

    C:\\Users\\Public\\Music\\four\_of\_clubs.wav (550302 bytes)

    C:\\vagrant\\resources\\flags\\four\_of\_clubs.wav (550302 bytes)

meterpreter > cd Users

cmeterpreter > cd Public

meterpreter > cd Music

meterpreter > dir

Listing: C:\\Users\\Public\\Music

\==============================

Mode              Size    Type  Last modified              Name

\----              ----    ----  -------------              ----

40776/rwxrwxrw-   0       dir   2009-07-14 00:57:55 -0400  Sample Music

100777/rwxrwxrwx  380     fil   2009-07-14 00:57:55 -0400  desktop.ini

100776/rwxrwxrw-  550302  fil   2016-11-25 20:47:41 -0500  four\_of\_clubs.wav

meterpreter > download four\_of\_clubs.wav

*‘binwalk’ showed me there was a PNG file attached to the WAV file*

**root@igor-kali**:**~**\# binwalk four\_of\_clubs.wav

DECIMAL       HEXADECIMAL     DESCRIPTION

\--------------------------------------------------------------------------------

58            0x3A            PNG image, 521 x 729, 8-bit/color RGBA, non-interlaced

120           0x78            Zlib compressed data, best compression

*I used ‘foremost’ this time to separate all the files*

**root@igor-kali**:**~**\# foremost four\_of\_clubs.wav

Processing: four\_of\_clubs.wav

|\*|

**root@igor-kali**:**~**\# cd output/

**root@igor-kali**:**~/output**\# ls

audit.txt  **png**  **wav**

**root@igor-kali**:**~/output**\# cd png/

**root@igor-kali**:**~/output/png**\# ls

**00000000.png**

[![](/assets/images/four_of_clubs.jpg)](/assets/images/four_of_clubs.jpg)

Serious guy!
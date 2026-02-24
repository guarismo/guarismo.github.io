---
layout: post
title: "The King of Clubs"
date: 2017-01-07T21:30:00Z
author: gu4r15sm0
---

## Metasploitable 3

### The King of Clubs

From the meterpreter I found in the Elasticsearch vulnerability, I used the search command to look for files that start with king:

meterpreter > search -f king\*

Found 5 results...

    **C:\\Windows\\System32\\kingofclubs.exe (824563 bytes)**

    C:\\vagrant\\resources\\flags\\kingofclubs.exe (824563 bytes)

    C:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds-150x150.png (46738 bytes)

    C:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds-214x300.png (130832 bytes)

    C:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds.png (585695 bytes)

I downloaded the file:

meterpreter > download kingofclubs.exe

**\[\*\]** downloading: kingofclubs.exe -> kingofclubs.exe

**\[\*\]** download   : kingofclubs.exe -> kingofclubs.exe

*I examine the file, as expected it’s a Windows executable, and 32bits.*

**root@igor-kali**:**~/metasploitable3**\# file kingofclubs.exe

kingofclubs.exe: PE32 executable (console) Intel 80386 (stripped to external PDB), for MS Windows, UPX compressed

**root@igor-kali**:**~/metasploitable3**\# binwalk kingofclubs.exe

DECIMAL       HEXADECIMAL     DESCRIPTION

\--------------------------------------------------------------------------------

0             0x0             Microsoft executable, portable (PE)

I ran it with wine

**root@igor-kali**:**~/metasploitable3**\# wine kingofclubs.exe

Could not load wine-gecko. HTML rendering will be disabled.

Could not load wine-gecko. HTML rendering will be disabled.

wine: configuration in ‘/root/.wine’ has been updated.

Who are you? What is your true identity?

**root@igor-kali**:**~/metasploitable3**\# wine kingofclubs.exe

Who are you? What is your true identity?

OK…hmmm....

I decompressed the UPX Exec file and copied it to my Windows host and used **OllyDbg** to take a look at the code.

**root@igor-kali**:**~/metasploitable3**\# upx -d kingofclubs.exe

                       Ultimate Packer for eXecutables

                          Copyright (C) 1996 - 2013

UPX 3.91        Markus Oberhumer, Laszlo Molnar & John Reiser   Sep 30th 2013

        File size         Ratio      Format      Name

   --------------------   ------   -----------   -----------

    962291 <-    824563   85.69%    win32/pe     kingofclubs.exe

Once I skipped the whole **ntdll** module all the way to the **kingofcl** module part, I noticed the stack was showing me some ASCII data that looked like the same header as the **[three\_of\_spades.png](http://gu4r15m0.blogspot.com/2017/01/the-three-of-spades.html)** I already did.

 [![](/assets/images/king_of_clubs-debug.jpg)](/assets/images/king_of_clubs-debug.jpg)

PNG Header XOR 0x0F

I ran an hexdump on the new decompressed exe file and searched for the characters I found in the three\_of\_spades.png

**root@igor-kali**:**~/metasploitable3**\# hexdump -C kingofclubs.exe | grep -A 1 \_AH

0003e000

86 5f 41 48 02 05 15 05  0f 0f 0f 02 46 47 4b 5d

|.\_AH........FGK\]|

0003e010

0f 0f 0d 06 0f 0f 0d d6  07 09 0f 0f 0f 32 53 bd

|.............2S.|

So I ran my byte\_xor.py script again with the same key 0x0f

**root@igor-kali**:**~/metasploitable3**\# cat byte\_xor.py

#!/usr/bin/python

input\_file = ‘kingofclubs.exe’

output\_file = input\_file+’.out’

b = bytearray(open(input\_file, ‘rb’).read())

for i in range(len(b)):

    b\[i\] ^= **0x0f**

open(output\_file, ‘wb’).write(b)

﻿Now binwalk shows me a PNG file in 0x3E000, same address my hexdump|grep did

**root@igor-kali**:**~/metasploitable3**\# binwalk kingofclubs.exe.out

DECIMAL       HEXADECIMAL     DESCRIPTION

\--------------------------------------------------------------------------------

253952        0x3E000         PNG image, 521 x 729, 8-bit/color RGBA, non-interlaced

254014        0x3E03E         Zlib compressed data, best compression

Now I use foremost and extract the file

**root@igor-kali**:**~/metasploitable3**\# foremost kingofclubs.exe.out -o king\_of\_clubs

Processing: kingofclubs.exe.out

|\*|

**root@igor-kali**:**~/metasploitable3**\# file king\_of\_clubs/png/00000496.png

king\_of\_clubs/png/00000496.png: PNG image data, 521 x 729, 8-bit/color RGBA, non-interlaced

[![](/assets/images/king_of_clubs.jpg)](/assets/images/king_of_clubs.jpg)

Ready to party!

And that's it! All 15 flags done!

 [![](/assets/images/caught-them-all.jpg)](/assets/images/caught-them-all.jpg)

Caught Them All!!

I won second place 😃

> The results are in for the [#Metasploitable3](https://twitter.com/hashtag/Metasploitable3?src=hash) CTF. Check out the winners: [https://t.co/Yv7gaKcjUS](https://t.co/Yv7gaKcjUS)
> 
> — Rapid7 (@rapid7) [January 4, 2017](https://twitter.com/rapid7/status/816765017093795840)
---
layout: post
title: "The Three of Spades"
date: 2017-01-03T19:10:00Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

## Metasploitable 3

### The Three of Spades

**How did you get access to the machine?**

*I guessed the password of the Administrator account in an SSH login prompt.*

**How did you spot the file?**

*Once I had a SSH console on the server I went into the Windows folder and listed the files.*


```bash
root@igor-kali:~# ssh Administrator@10.20.10.19

Administrator@10.20.10.19’s password:
```


Last login: Thu Dec 15 21:01:49 2016 from 10.20.10.50

\-sh-4.3$ cd / ../

\-sh-4.3$ pwd

/cygdrive/c/Users

\-sh-4.3$ cd ..

\-sh-4.3$ pwd

/cygdrive/c

\-sh-4.3$ ls windows

AppCompat              TSSysprep.log

AppPatch               Tasks

Boot                   Temp

Branding               Vss

Cursors                Web

DigitalLocker          WindowsShell.Manifest

Downloaded Program Files  WindowsUpdate.log

DtcInstall.log         assembly

Fonts                  bfsvc.exe

Globalization          bootstat.dat

Help                  debug

HelpPane.exe           diagerr.xml

IME                   diagnostics

Installer              diagwrn.xml

L2Schemas              en-US

LiveKernelReports      explorer.exe

Logs                   fveupdate.exe

Media                  hh.exe

Microsoft.NET          iis7.log

ModemLogs              inf

Offline Web Pages      mib.bin

PFRO.log               msdfmap.ini

PLA                   regedit.exe

Panther                rescache

PolicyDefinitions      schemas

Registration           security

RemotePackages         securitynew.sdb

Resources              servicing

SchCache               setupact.log

ServerStandard.xml     setuperr.log

ServerWeb.xml          splwow64.exe

ServiceProfiles        system

Setup                  system.ini

SoftwareDistribution   **three_of_spades.png**

Speech                 tracing

SysMsiCache            win.ini

SysWOW64               winhlp32.exe

System32               winsxs

TAPI                   write.exe

*But if you try to do the same on a CMD console, you cannot see it unless you look for hidden files*

﻿ ﻿\-sh-4.3$ bash

Microsoft Windows [Version 6.1.7601]

Copyright (c) 2009 Microsoft Corporation.  All rights reserved.


```text
C:\Users\Administrator>cd\

C:\>dir /AH windows
```


 Volume in drive C is Windows 2008R2

 Volume Serial Number is 1475-561C

 Directory of C:\\windows

12/15/2016  06:53 PM    <DIR>          Installer

11/25/2016  05:47 PM           519,696 **three_of_spades.png**

07/13/2009  08:57 PM               749 WindowsShell.Manifest

**How did you extract the file?**

*I  downloaded it with scp*


```bash
root@igor-kali:~# scp Administrator@10.20.10.19:/cygdrive/c/Windows/three_of_spades.png .

Administrator@10.20.10.19’s password:
```


```text
three_of_spades.png          
root@igor-kali:~/metasploitable3# file three_of_spades.png
three_of_spades.png: data
```

*I noticed the file is not in PNG format, but it still has a PNG extension so maybe something was done to the content.*

*Since I know how a good PNG starts I decide to compare both files headers:*

```text
BAD

00000000

86 5f 41 48 02 05 15 05  0f 0f 0f 02 46 47 4b 5d

|._AH........FGK]|

GOOD

00000000

89 50 4e 47 0d 0a 1a 0a  00 00 00 0d 49 48 44 52

|.PNG........IHDR|

BAD

00000010

0f 0f 0d 06 0f 0f 0d d6  07 09 0f 0f 0f 32 53 bd

|.............2S.|

GOOD

00000010

00 00 02 09 00 00 02 d9  08 06 00 00 00 3d 5c b2

|.............=\.|
```

*If you do a XOR byte to bad between good and bad, you get 0f so that’s our encryption key*

*I made a pyhton script:*


```bash
root@igor-kali:~/metasploitable3# cat byte_xor.py

#!/usr/bin/python

input_file = ‘three_of_spades.png’s;

output_file = input_file+’.out’;

b = bytearray(open(input_file, ‘rb’).read())

for i in range(len(b)):

    b[i] ^= 0x0f

open(output_file, ‘wb’).write(b)

*Ran my script*

root@igor-kali:~/metasploitable3# file three_of_spades.png

three_of_spades.png      three_of_spades.png.out 

root@igor-kali:~/metasploitable3# file three_of_spades.png.out
three_of_spades.png.out: PNG image data, 521 x 729, 8-bit/color RGBA, non-interlaced
```

[![](/assets/images/three_of_clubs.jpg)](/assets/images/three_of_clubs.jpg)

Good doggy!
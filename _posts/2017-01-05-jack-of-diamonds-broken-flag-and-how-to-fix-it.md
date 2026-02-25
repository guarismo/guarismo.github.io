---
layout: post
title: "Jack of Diamonds -  Broken flag and how to fix it"
date: 2017-01-05T17:11:00.001Z
author: gu4r15sm0
---

## Metasploitable 3

### The Jack of Diamonds

This card is broken, remember my post about [Broken Flags](https://gu4r15m0.blogspot.ca/2016/12/metasploitable-3-broken-flags-cards.html)?

Anyway, this one is pretty interesting because I've never seen this case before.

If you remember, in my post about the [Queen of Hearts](https://gu4r15m0.blogspot.ca/2016/12/metasploitable-3-queen-of-hearts-ok-i.html) I spotted a file called jack_of_diamonds.png in the root of the C:\\ drive with a zero (0) size.

C:\>dir
dir
 Volume in drive C is Windows 2008R2
 Volume Serial Number is 1475-561C

 Directory of C:\\

11/25/2016  07:33 PM    <DIR>          glassfish
11/25/2016  07:25 PM    <DIR>          inetpub
**11/25/2016  07:54 PM                 0 jack_of_diamonds.png**
11/25/2016  07:50 PM               103 java0.log
11/25/2016  07:50 PM               103 java1.log
11/25/2016  07:50 PM               103 java2.log
11/25/2016  07:48 PM    <DIR>          ManageEngine
11/25/2016  07:37 PM    <DIR>          openjdk6
07/13/2009  07:20 PM    <DIR>          PerfLogs
11/25/2016  07:53 PM    <DIR>          Program Files
11/25/2016  07:48 PM    <DIR>          Program Files (x86)
11/25/2016  07:54 PM    <DIR>          tmp
11/25/2016  07:38 PM    <DIR>          tools
11/25/2016  07:25 PM    <DIR>          Users
11/25/2016  07:32 PM    <SYMLINKD>     vagrant [\\\\vboxsrv\\vagrant]
11/25/2016  07:36 PM    <DIR>          wamp
11/25/2016  07:54 PM    <DIR>          Windows
10/07/2015  06:22 PM               226 \__Argon\_\_.tmp
               5 File(s)            535 bytes
              13 Dir(s)  48,041,852,928 bytes free

Some times a simple DIR command doesn't show all the information about the files we are looking at, and certainly it doesn't show hidden files. In this case we need to look at other attributes of the files.

This is the output of DIR /? showing other options we have:

[![](/assets/images/dir-help.JPG)](/assets/images/dir-help.JPG)

DIR /?

The /R option seemed interesting, it shows *alternative data streams of the file*, what's that?

I didn't know there was such a thing, (haven't finish that book I bought about malware 😞) and now I know this is specific for NTFS filesystems

Here's a [link](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364404\(v=vs.85\).aspx) from Microsoft explaining what it is.

So, if we run a DIR /R on the jack_of_diamonds.png file we get:

C:\>dir /R jack_of_diamonds.png

 Volume in drive C is Windows 2008R2

 Volume Serial Number is 1475-561C

 Directory of C:\\

12/15/2016  07:48 PM                 0 jack_of_diamonds.png

                                 **8,191 jack_of_diamonds.png:jack_of_diamonds.txt:$DATA**

               1 File(s)              0 bytes

               0 Dir(s)  51,540,779,008 bytes free

C:\>

And there's the alternative data stream, but the size seems too small compared with other text formats.

To extract it we can just use a ***more*** command, we can also use the [Get-Item](https://blogs.technet.microsoft.com/askcore/2013/03/24/alternate-data-streams-in-ntfs/) cmdlet from PowerShell.

C:\>more < jack_of_diamonds.png:jack_of_diamonds.txt:$DATA

iVBORw0KGgoAAAANSUhEUgAAAgkAAALZCAYAAAA9XLLXAAAACXBIWXMAABcRAAAXEQHKJvM/AAAgAElEQVR42uy9Waxl6XUe9u29/z2e+Zw71q2hq0ey1RQpUoIGm4IVS7IUSKaTOAkMGLCflAc9WA9GnvSgQEkejBiggUSCCBihJSQwFMqxYg2JTEoUZYviIFKcutns6u6qrqpbdzr3jHue8vB/6+yzm3YiWZRkkecHGrfr3nP2/vc/7bW+9a1vGXVd4y+yffbXf+67izz9oevpxXeH69n7LMvEan55POj3AQCu5QEAFqs1AEB19O/9bgcAEC2XAADLNPQFjQoAkBf6Z4efBwDHdgAAq1Bfy3GU/kw3AADYtg0ASJMIACBDs1qG+u+O/r7X9fXfoe9ZVWbzQJX+UlkUvIe+pm3peyVpovur2F9eoyhK/T1+XylX/2SfwnC5uYXv6THJ00zfQ+l+lbm+Z8nPRVmqPx/o/iqYfC79CcNiX6uK1/E39yirks/IfpX6Mxb0c+SZvvd41AMArMO5vibH32Efk5j34vx4djNWlqmvVdQyd/pvZV1xTHL9OcPgWOoxqUuTY2Tpe69XHCt9PcsymvkwZX3XXAN6PAuOlcXvgGNj2fqaeZ5tzQ5QVvrf7Bqq0mrGqqxb45yEEfuhPxPFsV7Lnp6notTP1e90W5+Ls7y1Fhzlbe6Rcz0pdteU23OyQ96z4qKVftYc95rPb/CJhoMxnzPf3COM9Dp3uS/KsuSzVq09W/PilqE7UVtOq98yarKfsqI5YxZXMwDAbHqu189kAAB46tlb7MOan+T6556Tvvi+395nslbQ9NHkOornel24rr7Ww8eP9Ae4bm7evMN143D5KX7f4B5O+TRVa/62nzXP9d8y7jXH1t81N+NuttZmmiV420ICAHS7+pwKs2asHMfn+ZJvrVDAMrlP2IflcqHXE89Mx3Va81aUGe+t11AQ9JrnYP9NngXyHcu0+Rz6c8u13t+ … etc

We can now store it in a file

C:\>more < jack_of_diamonds.png:jack_of_diamonds.txt:$DATA > jack_of_diamonds.b64

I had to clean up the file before decoding it back to binary and add an *‘=’* to the end

﻿…

jFj/+zD4TLJz85GA2wjhflnh9+zXOsSFufLEDCWNPb0YmDwz1a+/OWlSmWvrZPycxlrHh8eLN1LfHGTk6e0ZYV2Z6S3SAxvzE1EEp67otlk3cp8cubT92lU6itMGFQS+54UwCp7fVLnrJI3kp+e1k2XuWcrPeSXqR4lRK7ExRAvBp5vkgKo0i8ioiE8Af2Rp0G/pF+M0690TDgT/Fq6oJesGQJkJMgHp7EagOiBUnWRIsUc/xFLVDywm1auhPG4TNmSETM+BBURcasNtyWd+dt5WjbXDA11QzFMo/oQUtuP5QUgqFHRa9BtAokritFv0a9g2Y+Lpf06LKWNyzeY0gPtksUbLmaci0TIaEln8VJy672gqZw2IJ50EOiXpIxMbuYt70t122tf8dc0VvWY1LSo5KCULbbzHmaiPyh2erHRn6c+yMlT2VF9rbkvdv0QGyPHA2iSp7bsNBlziTu32Gc9mtf+apeg0QAb93Re/OcWQ9ffPk17s3jFqKyydPfOg5ERlnENOdcwwP5vRQBcvutdSfZT7JfRD1RCqYNxk3RItGnWMz1GIw5t6LPIplSQz6feMsb5MRrI1lmTW2QQZNtIh6n7MU+veQFM6IIisHiejM2KKDLdUVUhlyYBTUZhKUPAEOeZRYzvDYF0XjWyboSDYmI6IWE1YV3E1OjoTaEM9Lco0dU0TaEF8Rzi+eneLIFeUMxUQf^M

And then just use base64 to convert it into png, but as you know the flag is broken so the image, we only got the top part of the border, maybe because of the way the alternative data stream was built.

[![](/assets/images/jack_of_diamonds_broken.jpg)](/assets/images/jack_of_diamonds_broken.jpg)

Broken Jack of Diamonds

And it's supposed to look like this

[![](/assets/images/jack_of_diamonds.png)](/assets/images/jack_of_diamonds.png)

Correct card

The issue with how this card was built is that the ***install_flags.bat*** tries to build it using an ***echo*** command of the whole base64 string into the alternative data stream, this not just doesn't work, it also makes the ***install_flags.bat*** file hard to edit. To fix this, the team at ***Rapid7*** could put the base64 content in a separate file and use the ***type*** command instead of ***echo*** in the ***install_flags.bat***.


```text
C:\Users\iguar\blog\metasplotable3>type jack_of_diamonds.b64 > jack_of_diamonds-test.png:jack_of_diamonds.txt

C:\Users\iguar\blog\metasplotable3>dir /r jack_of_diamonds-test.png
```


 Volume in drive C is Windows

 Volume Serial Number is 4085-5A70

 Directory of C:\\Users\\iguar\\blog\\metasplotable3

2017-01-05  11:57 AM                 0 jack_of_diamonds-test.png

                               **834,089** jack_of_diamonds-test.png:jack_of_diamonds.txt:$DATA

               1 File(s)              0 bytes

               0 Dir(s)  675,664,252,928 bytes free

834,089 Bytes is the same size of the base64 text file.
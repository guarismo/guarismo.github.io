---
layout: post
title: "The Ace of Hearts"
date: 2016-12-09T02:16:00.001Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

## **Metasploitable 3**

### The Ace of Hearts

With the Ten of Diamonds, we also downloaded the Ace of Hearts, this time a JPEG file, let's take a look

[![](/assets/images/ace_of_hearts.jpg)](/assets/images/ace_of_hearts.jpg)

Not the card we are looking for

Ok, this is not what we're looking for, I guess we want PNG files, let's take a closer look to the file with binwalk
﻿


```bash
root@igor-kali:~# binwalk ace_of_hearts.jpg 

DECIMAL       HEXADECIMAL     DESCRIPTION

--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
20087         0x4E77          Zip archive data, at least v1.0 to extract, compressed size: 459917, uncompressed size: 459917, name: ace_of_hearts.png
480150        0x75396         End of Zip archive
```

```


Bingo, there's a Zip file attached to the JPEG file that has a ace_of_hearts.png file inside
Some people will extract the ZIP file with 'dd' or with 'foremost' but I'll just make a copy and rename to zip and unzip it (you can also unzip directly).
﻿


```bash
root@igor-kali:~# cp ace_of_hearts.jpg ace_of_hearts.jpg.zip
root@igor-kali:~# unzip ace_of_hearts.jpg.zip 
```

Archive:  ace_of_hearts.jpg.zip
warning [ace_of_hearts.jpg.zip]:  20087 extra bytes at beginning or within zipfile
  (attempting to process anyway)
 extracting: ace_of_hearts.png       
```


[![](/assets/images/ace_of_hearts-png.jpg)](/assets/images/ace_of_hearts-png.jpg)

The Ace of Hearts
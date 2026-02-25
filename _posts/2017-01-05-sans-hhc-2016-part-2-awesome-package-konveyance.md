---
layout: post
title: "SANS HHC 2016 - Part 2: Awesome Package Konveyance"
date: 2017-01-05T19:27:00.001Z
author: gu4r15sm0
category: sans-hhc
tags: [sans, hhc-2016, ctf]
---

## SANS Holiday Hack Challenge 2016

### Part 2: Awesome Package Konveyance

[![](/assets/images/apktool - Bushy Evergreen.jpg)](/assets/images/apktool - Bushy Evergreen.jpg)

Bushy Evergreen talking about APKTool

3) What username and password are embedded in the APK file?

My new friend Bushy Evergreen told me about this tool called APKTOOL and point me to a nice [video](https://www.youtube.com/watch?v=mo2yZVRicW0) by none other than Joshua Wright himself about working with apk files and making changes in the smali code.

I moved all my APK work to Kali Linux because... well… that’s where the good stuff is J

root@igor-kali:~/hhack2016# apktool d SantaGram_4.2.apk

I: Using Apktool 2.2.0-dirty on SantaGram_4.2.apk

I: Loading resource table...

I: Decoding AndroidManifest.xml with resources...

I: Loading resource table from file: /root/.local/share/apktool/framework/1.apk

I: Regular manifest package...

I: Decoding file-resources...

I: Decoding values \*/\* XMLs...

I: Baksmaling classes.dex...

I: Copying assets and libs...

I: Copying unknown files...

I: Copying original files...

Ok, now we have decoded values in the XML files, let’s do that search again

root@igor-kali:~/hhack2016/SantaGram_4.2# grep -r password \*

…

…

﻿smali/com/northpolewonderland/santagram/b.smali:    const-string v1, "password"

smali/com/northpolewonderland/santagram/SplashScreen.smali:    const-string v1, "password"

Doesn’t show me any string that could be a password but it's showing me smali code with variables with the string “password” next to them, this means that the password might be hardcoded in the smali files and not in some XML configuration file.


```bash
root@igor-kali:~/hhack2016/SantaGram_4.2# grep -r password -A 3 -B 6 smali/com/northpolewonderland/santagram/SplashScreen.smali

    const-string v1, "username"

    const-string v2, "guest"

    invoke-virtual {v0, v1, v2}, Lorg/json/JSONObject;->put(Ljava/lang/String;Ljava/lang/Object;)Lorg/json/JSONObject;

    const-string v1, "password"

    const-string v2, "busyreindeer78"
```


Done! 

4) What is the name of the audible component (audio file) in the SantaGram APK file?

Wellm since we have the APK decompressed and decompiled, let’s look for known audio file extensions:


```bash
root@igor-kali:~/hhack2016/SantaGram_4.2# find . | egrep mp3|wav|flac|ogg

./res/raw/discombobulatedaudio1.mp3
```


Done! That was easy J
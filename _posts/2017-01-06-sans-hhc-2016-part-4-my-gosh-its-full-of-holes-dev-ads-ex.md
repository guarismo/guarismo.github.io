---
layout: post
title: "SANS HHC 2016 - Part 4: My Gosh... It's Full of Holes - Dev / Ads / Ex"
date: 2017-01-06T16:45:00.002Z
author: gu4r15sm0
---

## SANS Holiday Hack Challenge

### Continuing with Part 4: My Gosh... It's Full of Holes

To continue examining and exploiting vulnerabilities was necessary to finish the environment.

Here you can find [my setup](http://gu4r15m0.blogspot.com/p/sans-holiday-hack-2016-my-setup-and.html)

-   The Debug Server

For this one, nmap only shows TCP port 80 and the content of the APK file shows an index.php URL.

Visiting the URL shows nothing, so we have to find out how the SantaGram app interact with it.

Time to use my emulator and BurpSuite to look at the traffic.

At first I didn’t see anything interacting with the debug server (dev.northpolewonderland.com), so debug mode must be disabled.

I look for debugging parameters in the APK file decoded by apktool.

[![](/assets/images/debug-enabler.JPG)](/assets/images/debug-enabler.JPG)

Searching for Debug parameters

It seems the res/values/strings.xml file has the parameter we need to change and also need to interact with the EditProfile part of the app, but I changed the ***debug_data_enabled*** value to ***true***, recompiled and signed as explained by Josh Wright [here](https://pen-testing.sans.org/blog/2015/06/30/modifying-android-apps-a-sec575-hands-on-exercise-part-1), and also here in this [video](https://www.youtube.com/watch?v=mo2yZVRicW0), but no luck, editing the profile didn’t trigger any communication with the debug server.

So I went back and decided to attack the logic of the ***EditProfile.smali*** file, tracing the ***debug_data_enabled*** variable from the ***strings*** XML to the ***public*** XML I found an ***id=0x7f07001e***.

[![](/assets/images/debug-original-logic.JPG)](/assets/images/debug-original-logic.JPG)

Traced the variable 0c7f07001e - debug_data_enabled in EditProfile.smali

The code says that it’s compering v0 (from the strings.xml) with v3 (constant with value “true”). I decided just to change the const-string v3 value from “true” to “false”, in that way the default behavior will be to enable debug. Recompiled, resigned and push to my emulator again.

[![](/assets/images/debug-emulator+burp.JPG)](/assets/images/debug-emulator+burp.JPG)

Debug enabled - Traffic going from SantaGram to Dev

Great, we have a connection and a JSON to play with as recommended by my greatest friend in the North Pole, Alabaster Snowball J

[![](/assets/images/JSON-Cheat_dungeon.jpg)](/assets/images/JSON-Cheat_dungeon.jpg)

Alabaster talking about JSON parameters

Using BurpSuite repeater, we repeat the request and find that it response with exactly the same data plus an extra field called verbose that it’s set to false.

[![](/assets/images/dev-jason-no-verbose.jpg)](/assets/images/dev-jason-no-verbose.jpg)

BurpSuite repeater showing verbose parameter

 I included that parameter in my request and set the value to true, this made the server return a list of files present in the same path as the index.php file, including the MP3 file.

[![](/assets/images/dev-json-verbose-mp3.jpg)](/assets/images/dev-json-verbose-mp3.jpg)

JSON output with verbose=true shows MP3 file

root@igor-kali:~/hhack2016/dev.northpolewonderland.com# wget dev.northpolewonderland.com/debug-20161224235959-0.mp3

\--2016-12-26 14:10:55--  http://dev.northpolewonderland.com/debug-20161224235959-0.mp3

Resolving dev.northpolewonderland.com (dev.northpolewonderland.com)... 35.184.63.245

Connecting to dev.northpolewonderland.com (dev.northpolewonderland.com)|35.184.63.245|:80... connected.

HTTP request sent, awaiting response... 200 OK

Length: 218033 (213K) [audio/mpeg]

Saving to: ‘debug-20161224235959-0.mp3’

debug-20161224235959-0.mp3         100%[===================================>] 212.92K  --.-KB/s    in 0.1s   

2016-12-26 14:10:55 (1.54 MB/s) - ‘debug-20161224235959-0.mp3’ saved [218033/218033]

-   The Banner Ad Server

And nmap scan shows that the server has TCP port 80 listening and accepting connection, and I point a web browser there and find the Ads banner server.

By the way, if you have an ad blocker, the site won’t display on your browser, disable it before proceeding.

I noticed a Login option in the top right corner, but also decided to look into the HTML source of the website for clues.

[![](/assets/images/ads-server.JPG)](/assets/images/ads-server.JPG)

Ads main website

[![](/assets/images/ads-source.jpg)](/assets/images/ads-source.jpg)

Ads HTML Source showing Meteor Framework

On the source code I saw that it was using the [meteor framework](https://www.meteor.com/) that was mentioned by one of the elves.

[![](/assets/images/meteor - Pepper Minstix.jpg)](/assets/images/meteor - Pepper Minstix.jpg)

 Pepper Minstix talks about Meteor

I read [Tim Medin’s blog post from HackFest 2016](https://pen-testing.sans.org/blog/2016/12/06/mining-meteor) and installed [Tampermonkey](http://tampermonkey.net/) and his [Meteor Miner script](https://github.com/nidem/MeteorMiner) to start looking for subscriptions and collections.

This way I found an URL that didn’t have a link from the main website [http://ads.northpolewonderland.com/admin/quotes](http://ads.northpolewonderland.com/admin/quotes)

[![](/assets/images/ads-found-admin-quotes.JPG)](/assets/images/ads-found-admin-quotes.JPG)

Spotted /admin/quotes with Meteor Miner

I looked at the collections and click on the HomeQuotes one to find 1 record with audio which is what I’m looking for.

[![](/assets/images/ads-homequotes-audio.jpg)](/assets/images/ads-homequotes-audio.jpg)

HomeQuotes is showing an audio record 

I opened a Web Console and ran HomeQuotes.find().fetch() and it shows me an array of 5 objects, I clicked on the 5th object and found the URL for the MP3 file, [http://ads.northpolewonderland.com/ofdAR4UYRaeNxMg/discombobulatedaudio5.mp3](http://ads.northpolewonderland.com/ofdAR4UYRaeNxMg/discombobulatedaudio5.mp3)

![](/assets/images/ads-tampermonkey.jpg)

Web console - Result of HomeQuotes.find().fetch()

[](/assets/images/ads-tampermonkey.jpg)

-   The Uncaught Exception Handler Server

As I used the SantaGram app I noticed that it would crash every time I tried to use the Search option and scroll down the posts, and this will trigger a message to the [http://ex.northpolewonderland.com/exception.php](http://ex.northpolewonderland.com/exception.php) URL.

[![](/assets/images/santagram-crash.JPG)](/assets/images/santagram-crash.JPG)

SantaGram Crashes in Search 

[![](/assets/images/ex-writecrashdump.JPG)](/assets/images/ex-writecrashdump.JPG)

JSON Request to ex server

The respond to this JSON request was a JSON indicating the a crashdump PHP file was created in the docs directory.

[![](/assets/images/crashdump-created.JPG)](/assets/images/crashdump-created.JPG)

crashdump php file created

Using BurpSuite repeater I made changes to the operation parameter to find a verbose message that gave me the only two options that were acceptable, WriteCrashDump and ReadCrashDump.

[![](/assets/images/ex-change-opration.JPG)](/assets/images/ex-change-opration.JPG)

Exception JSON operations

I changed the operation to ReadCrashDump to see what else can I find out and it tells me that I need a crashdump key set in the JSON for it to work, so this means this can read the crashdump-XXXXX.php files it creates and I try that without all the other values and got an HTTP 500 errors that I can interpret as it tried to execute the PHP but failed.

[![](/assets/images/ex-Read-crashdump-key-needed.JPG)](/assets/images/ex-Read-crashdump-key-needed.JPG)

ReadCrashDump needs crashdump JSON key

[![](/assets/images/ex-crashdump-read-http-500.JPG)](/assets/images/ex-crashdump-read-http-500.JPG)

HTTP 500 trying to read a crashdump

This looks like a Local File Inclusion vulnerability that **[Sugarplum Mary](http://gu4r15m0.blogspot.com/2016/12/sans-holiday-hack-challenge-2016-tips.html)** talked about, and I managed to extract the source code of the ***exception.php*** file by using [Jeff McJunkin’s blog post](https://pen-testing.sans.org/blog/2016/12/07/getting-moar-value-out-of-php-local-file-include-vulnerabilities)

[![](/assets/images/ex-dump.jpg)](/assets/images/ex-dump.jpg)

Base64 Dump of the exception.php file

[![](/assets/images/ex-php.jpg)](/assets/images/ex-php.jpg)

MP3 reference found in exception.php

The file can be downloaded from [http://ex.northpolewonderland.com/discombobulated-audio-6-XyzE3N9YqKNH.mp3](http://ex.northpolewonderland.com/discombobulated-audio-6-XyzE3N9YqKNH.mp3)

NOTE: If you noticed, the crashdump files are PHP and are executed by the server if you point a browser to it or curl it, it just prints back the data you sent to the ***exception.php***. If you look at the code, you can inject your own PHP code to it, actually, other players used a print_r(scandir('..')) to list the files and found the MP3 instead of the base64 exfiltration. You can do more than that 😏
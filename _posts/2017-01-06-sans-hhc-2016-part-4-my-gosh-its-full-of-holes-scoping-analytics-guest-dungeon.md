---
layout: post
title: "SANS HHC 2016 - Part 4: My Gosh... It's Full of Holes - Scoping / Analytics (guest) / Dungeon"
date: 2017-01-06T03:44:00Z
author: gu4r15sm0
category: sans-hhc
tags: [sans, hhc-2016, ctf]
---

## SANS Holiday Hack Challenge 2016

### Part 4: My Gosh... It's Full of Holes

7) ONCE YOU GET APPROVAL OF GIVEN IN-SCOPE TARGET IP ADDRESSES FROM TOM HESSMAN AT THE NORTH POLE, ATTEMPT TO REMOTELY EXPLOIT EACH OF THE FOLLOWING TARGETS:

-   The Mobile Analytics Server (via credentialed login access)
-   The Dungeon Game
-   The Debug Server
-   The Banner Ad Server
-   The Uncaught Exception Handler Server
-   The Mobile Analytics Server (post authentication)

### [Scoping and recon](https://www.blogger.com/null)

While looking into the SantaGram apk file, I found these URLs in the strings.xml file:

·         ﻿﻿[https://www.northpolewonderland.com](https://www.northpolewonderland.com/)

·         [https://analytics.northpolewonderland.com/](https://analytics.northpolewonderland.com/)

·         [http://ads.northpolewonderland.com/](http://ads.northpolewonderland.com/)

·         [http://dev.northpolewonderland.com/](http://dev.northpolewonderland.com/)

·         [http://dungeon.northpolewonderland.com](http://dungeon.northpolewonderland.com/)

·         [http://ex.northpolewonderland.com/](http://ex.northpolewonderland.com/)

Then translated them to IP addresses

www.northpolewonderland.com -  130.211.124.143

analytics.northpolewonderland.com -  104.198.252.157

ads.northpolewonderland.com -  104.198.221.240

dev.northpolewonderland.com -  35.184.63.245

dungeon.northpolewonderland.com -  35.184.47.139

ex.northpolewonderland.com -  104.154.196.33

And proceeded to ask The Oracle, ***Tom Hessman***, inside the tree at the North Pole for each of the IP addresses to confirm if they were in Scope.

[![](/assets/images/in-scope.JPG)](/assets/images/in-scope.JPG)

 Tom Hessman confirms IP address in scope

Anyone else expecting ***Barbara Gordon***?

And this were the results of the scoping

 Scoping results:

**FQDN**

**IP Address**

**In scope (Yes/No)**

**www.northpolewonderland.com**

130.211.124.143

No

**ads.northpolewonderland.com**

104.198.221.240

Yes

**dev.northpolewonderland.com**

35.184.63.245

Yes

**dungeon.northpolewonderland.com**

35.184.47.139

Yes

**ex.northpolewonderland.com**

104.154.196.33

Yes

**analytics.northpolewonderland.com**

104.198.252.157

Yes

For reconnaissance I used ***nmap*** on each server on the default ports only, and it was more than enough to find the information needed to complete the challenge: ﻿

[![](/assets/images/NMAP-John-Rockyou - Minty Candycane.jpg)](/assets/images/NMAP-John-Rockyou - Minty Candycane.jpg)

Minty Candycane gives NMAP tip

[![](/assets/images/nmap-sample-git.JPG)](/assets/images/nmap-sample-git.JPG)

Sample result from NMAP

 Recon results

**FQDN**

**Ports opened found**

**Interesting findings**

**ads.northpolewonderland.com**

TCP: 22, 80

Ads management website in TCP port 80.

**dev.northpolewonderland.com**

TCP: 22, 80

index.php referenced in the APK

**dungeon.northpolewonderland.com**

TCP: 22, 80 , 11111

TCP Port 11111 is hosting the dungeon game

**ex.northpolewonderland.com**

TCP: 22, 80

exception.php referenced by the APK

**analytics.northpolewonderland.com**

TCP: 22, 443

﻿104.198.252.157:443/.git/

Git repository found!

-   The Mobile Analytics Server (via credentialed login access)

Logged in with the hardcoded credentials from the SantaGram APK (guest/busyreindeer78) and was able to download the MP3 from the MP3 link in the menu which I noticed was pulled by the getaudio.php with a GET HTTP request, and the user id.

[![](/assets/images/analytics-login.jpg)](/assets/images/analytics-login.jpg)

Username: guest Password: busyreindeer78

[![](/assets/images/analytics-mp3.jpg)](/assets/images/analytics-mp3.jpg)

Click on MP3 and it calls getaudio.php and starts downloading discombobulatedaudio2.mp3

-   The Dungeon Game

While walking around the North Pole I found an elf, Pepper Minstix, who gave me an old copy of the game and mentioned another elf was known to beat it.

[![](/assets/images/dungeon-zip - Pepper Minstix.jpg)](/assets/images/dungeon-zip - Pepper Minstix.jpg)

Pepper Minstix has a copy of Dungeon

For this server, **nmap** revealed that TCP ports 80 and 11111 were Listening for connections.

Port 80 is hosting a website with the instructions on how to play dungeon and gives a tip of what’s the goal of playing the game: ***“Recent adventurers report a new passage has been installed which leads to the North Pole and the lair of a mischievous Elf who will trade for secrets he holds that may aid your quest.”***

[![](/assets/images/dungeon-commands.jpg.png)](/assets/images/dungeon-commands.jpg.png)

Game's goal and commands

Port 11111 has an online version of the game I can play using **netcat** as a client without further authentication.

[![](/assets/images/dungeon - online.JPG)](/assets/images/dungeon - online.JPG)

Dungeon Online

What was that game that Sheldon Cooper played in “The Bing Bang Theory” TV show?

Zork:

[![](/assets/images/dungeon -zork.JPG)](/assets/images/dungeon -zork.JPG)

Wikipedia says that Dungeon was the original Zork

And if we keep reading we found a great tip from the Fortran port version.

[![](/assets/images/zork-gdt.jpg)](/assets/images/zork-gdt.jpg)

GDT Command

Let’s try that

[![](/assets/images/gdt-help.JPG)](/assets/images/gdt-help.JPG)

GDT Command in action

That seems to be useful but the only thing I know is that I have to find an elf, so I don’t have enough information and I’m not really interested in playing this game, so let’s go and do more research.

From the offline version provided by the elf, I took a look at the dtextc.dat file, if I remove the file the game won’t run and the message suggest this is where the map of the game is.

[![](/assets/images/data-removed.JPG)](/assets/images/data-removed.JPG)

 Broken Dungeon

So now I want to know if I can read the dtextc.dat file somehow.

We know the MIT has a website ([web.mit.edu](http://web.mit.edu/)) where their personnel have blogs, share notes and sometime source code, let’s start there, google is our friend:

[![](/assets/images/dungeon-data-reader.JPG)](/assets/images/dungeon-data-reader.JPG)

Crazy stuff found!

[http://web.mit.edu/jhawk/src/cdungeon-decode.c](http://web.mit.edu/jhawk/src/cdungeon-decode.c)

[![](/assets/images/dungeon-data-reader-2.jpg)](/assets/images/dungeon-data-reader-2.jpg)

Email with Source Code of a decoder for the Dungeon data file

How lucky!?!? Let’s use that

root@igor-kali:~/hhack2016/dungeon# ./read_dungeon -b dtextc.dat -a dungeon.txt

The output in the dungeon.txt is all readable ASCII now, and since we’re looking for an elf, let’s search for that string


```bash
root@igor-kali:~/hhack2016/dungeon# grep -i " elf" dungeon.txt

Room: 192: Elf Room

    Your mission is to find the elf at the North Pole and barter with him
```


Object: 217: Elf

 The elf is facing you keeping his back warmed by the fire.

 leads to the North Pole and the lair of a mischevious elf who will trade

 The elf, willing to bargain, says "What’s in it for me?"

 The elf, satisified with the trade says -

 The elf appears increasingly impatient.

 The elf says - you have conquered this challenge - the game will now end.

There’s a room number (192) and an Object (217) with the elf and also some dialog with spelling mistakes, but is chopped, so I expanded the grep command

root@igor-kali:~/hhack2016/dungeon# grep -i " elf" dungeon.txt -A 3 -B 3

…

…

Message: 1024

 The elf, satisified with the trade says -

 Try the online version for the true prize

…

Earlier we saw in the GDT console a command called **DT - Display text**, and it asks for an entry number, so I entered 1024.

[![](/assets/images/dungeon-email.jpg)](/assets/images/dungeon-email.jpg)

Email address found

 I've emailed the elf and got back an MP3 file

[![](/assets/images/dungeon-audiofile-3.jpg)](/assets/images/dungeon-audiofile-3.jpg)

MP3 file attached to email response
---
layout: post
title: "SANS HHC 2016 - Part 1: A Most Curios Business Card"
date: 2017-01-04T16:42:00Z
author: gu4r15sm0
---

## **SANS Holiday Hack Challenge 2016**

### Part 1: A Most Curios Business Card

So Santa was kidnapped and our mission is to rescue him, lucky us he left us the first clue, he's business card

[![](/assets/images/santas-card.JPG)](/assets/images/santas-card.JPG)

Santa's Business Card

#### 
1) What is the secret message in Santa's tweets?

With the business card we go to Twitter and look and Santa's tweets, probably full of fun and magic, right? :)

[![](/assets/images/santas tweets.JPG)](/assets/images/santas tweets.JPG)

Not so magical Tweet

Then I remembered Josh mentioned something called the Twime Machine: 

*"I almost wish we had a Twime Machine to relive all those great Christmases of the past"*

And guess what? there's actually a website called [twime machine](http://www.twimemachine.com/user/santawclaus/) that let's you look at old tweets from any public account and display them in a very simple format.

[![](/assets/images/twime-santa.JPG)](/assets/images/twime-santa.JPG)

Twime Machine - @Santawclaus - Screen Captured and rotated 90 degrees to the left

[![](/assets/images/BUG.JPG)](/assets/images/BUG.JPG)

 [![](/assets/images/BOUN.JPG)](/assets/images/BOUN.JPG)

[![](/assets/images/NTY.jpg)](/assets/images/NTY.jpg)

Copy Pasting into a Notepad

So there you go, the answer is

**BUG BOUNTY**

#### 2) What is inside the ZIP file distributed by Santa's team?

The elves in the North Pole village are talking about something called SantaGram

[![](/assets/images/elfe-santagram.JPG)](/assets/images/elfe-santagram.JPG)

Elf talking about SantaGram

Now we look at Santa's instagram account and find an interesting photo

[![](/assets/images/instagram-santagram.JPG)](/assets/images/instagram-santagram.JPG)

@santawclaus Instagram - Zoom In for details

In this image we can see one of the Bug Bounty elves was working on a domain called [northpolewonderland.com](http://northpolewonderland.com/) and the Laptop computer screen is showing a file called SantaGram\_v4.2.zip

We can just point our browser to [http://northpolewonderland.com/SantaGram\_v4.2.zip](http://northpolewonderland.com/SantaGram_v4.2.zip) and download the file, or use wget

igor@KYLO-REN:~$ wget http://northpolewonderland.com/SantaGram\_v4.2.zip

\--2016-12-24 01:27:05--  http://northpolewonderland.com/SantaGram\_v4.2.zip

Resolving northpolewonderland.com (northpolewonderland.com)... 130.211.124.143

Connecting to northpolewonderland.com (northpolewonderland.com)|130.211.124.143|:80... connected.

HTTP request sent, awaiting response... 200 OK

Length: 1963026 (1.9M) \[application/zip\]

Saving to: ‘SantaGram\_4.2.zip’

100%\[===================================================================>\] 1,963,026   3.33MB/s   in 0.6s

2016-12-24 01:27:06 (3.33 MB/s) - ‘SantaGram\_v4.2.zip’ saved \[1963026/1963026\]

And Unzip…

Can you guess the password? ;)

igor@KYLO-REN:~$ unzip SantaGram\_v4.2.zip
Archive:  SantaGram\_v4.2.zip
\[SantaGram\_v4.2.zip\]
SantaGram\_4.2.apk password: ß bugbounty worked!  
inflating: SantaGram\_4.2.apk

By the way, there are two copies of the file in the server, one called **SantaGram\_v4.2.zip** like the Instagram says, and one called **SantaGram\_4.2.zip**, found it with a typo.

We have an android app!
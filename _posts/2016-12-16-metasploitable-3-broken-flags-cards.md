---
layout: post
title: "Metasploitable 3 - Broken flags (cards)"
date: 2016-12-16T19:20:00.002Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

Hello, I just got this from the Metasploitable 3 team at Rapid7:
CaptureTheFlag                                                                    1:57 PM (20 minutes ago)
to CaptureTheFlag
Hello,

You are receiving this e-mail because according to our record, you are an active participant for the Metasploitable3 CTF.
The CTF team would like to address two issues with these flags: Seven of Hearts and Jack of Diamonds,please read carefully.
**Seven of Hearts**
The Seven of Hearts is probably missing in your Metasploitable3 image, because we made a typo in the installation script. To make sure you have this card, you can either:
Rebuild the image by doing:
1\. In your repository, do "git pull" to update
2\. Do: "vagrant destroy"
3\. Do: "vagrant up", and this should rebuild the image. It will take a while. 

Or update and run install_flags.bat (without the need to rebuild the image, so this is quicker)
1\. wget [https://raw.githubusercontent.com/rapid7/metasploitable3/master/scripts/installs/install_flags.bat](https://raw.githubusercontent.com/rapid7/metasploitable3/master/scripts/installs/install_flags.bat)
2\. In the Metasploitable3 repo, move the new install_flags.bat file to the scripts/installs/ directory. This should replace the old one.
3\. On Virtual Box, log on to Metasploitable3 as vagrant:vagrant.
4\. Go to C:\\vagrant\\scripts\\installs. Right click on the install_flags.bat file, and run it as an administrator, and Seven of Hearts should be installed. 

**Jack of Diamonds**
You are only able to extract the Jack of Diamonds image partially. However, if the approach to finding this flag is correct, we will still give you full credit. So please don't ignore this flag.
Other flags are in good working condition. We are sorry for the inconvenience. If you have any questions, please let us know.
Thank you!
The Metasploitable3 CTF Team
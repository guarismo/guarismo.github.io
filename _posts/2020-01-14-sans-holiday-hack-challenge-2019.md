---
layout: post
title: "SANS Holiday Hack Challenge 2019"
date: 2020-01-14T05:11:00Z
author: gu4r15sm0
---

## KringleCon 2 - Obj 7

## 7) Get Access To The Steam Tunnels

Difficulty: 🎄🎄🎄 (3/5)
Gain access to the steam tunnels. Who took the turtle doves? Please tell us their first and last name. *For hints on achieving this objective, please visit Minty's dorm room and talk with Minty Candy Cane.*
In our way to the dorm to talk to Minty Candy we found a little inconvenience...

[![](https://2019.kringlecon.com/images/avatars/elves/elf12.png)](https://2019.kringlecon.com/images/avatars/elves/elf12.png)

*Tangle Coalbox*
*Hey kid, it's me, Tangle Coalbox.*
*I'm sleuthing again, and I could use your help.*
*Ya see, this here number lock's been popped by someone.*
*I think I know who, but it'd sure be great if you could open this up for me.*
*I've got a few clues for you.*

1.  *One digit is repeated once.*
2.  *The code is a prime number.*
3.  *You can probably tell by looking at the keypad which buttons are used*.

We are presented with the Key Lock, and we can clearly see that the code consist of 3 numbers, 1,3 and 7 because they are the mostly used on the pad. First we need a list of all permutations with repetitions of the 3 numbers in combinations of 4
We use [this code](https://code.luasoftware.com/tutorials/coding-interview/permutations-with-repeating-characters/) we found for that to get all these 81 possible combinations: 1111,1113,1117,1131,1133,1137,1171,1173,1177,1311,1313,1317,1331,1333,1337,1371,1373,1377,1711,1713,1717,1731,1733,1737,1771,1773,1777,3111,3113,3117,3131,3133,3137,3171,3173,3177,3311,3313,3317,3331,3333,3337,3371,3373,3377,3711,3713,3717,3731,3733,3737,3771,3773,3777,7111,7113,7117,7131,7133,7137,7171,7173,7177,7311,7313,7317,7331,7333,7337,7371,7373,7377,7711,7713,7717,7731,7733,7737,7771,7773,7777, but not all match prerequisite 1
Now we need to know which one is a [prime number](https://en.wikipedia.org/wiki/Prime_number).
We need a [quick code](https://www.programiz.com/python-programming/examples/prime-number) to do this for us fast
We found 15 numbers and remove those that doesn't match the conditions: 1117, 1171, **1373**, **1733**, 1777, **3137**, 3313, 3331, **3371** 3373, 3733, 7177, **7331**, 7333, 7717
Using the Browser's developers console on the [https://keypad.elfu.org/](https://keypad.elfu.org/) website and trying to entering some values we find the URL to use in a script instead of trying each manually

[![](/assets/images/keypad-devcon.jpg)](/assets/images/keypad-devcon.jpg)

Finally, a bash for loop to a curl gives us the answer
 ~$ for code in 1373 1733 3137 3371 7331; do echo $i; curl "https://keypad.elfu.org/checkpass.php?i=$code&resourceId=undefined"; echo -e; done
1373
{"success":false,"message":"Invalid  Code!"}
1733
{"success":false,"message":"Invalid  Code!"}
3137
{"success":false,"message":"Invalid  Code!"}
3371
{"success":false,"message":"Invalid  Code!"}
**7331**
**{"success":true,"resourceId":"undefined","hash":"677ec7081212bd3d29ec906a77f20760fb33c9c89f6955438ebbe3f10202610d","message":"Valid  Code!"}**
![](https://lh6.googleusercontent.com/yZfWFfuL_tL7LrIzt-8uvHwZGrhXGtgr-5jcHCtZJJ-u5wbNADH5Ev9ebia7I6dcfTQZqpDe1MwQ96ktw_4Y_t3l2RTSH_1XnuVOqvqiQblZnB4hwhGij99VVgyCv9wc7iT7JHm1)
*Tangle Coalbox:*
*Yep, that's it. Thanks for the assist, gumshoe.*
*Hey, if you think you can help with another problem, Prof. Banas could use a hand too.*
*Head west to the other side of the quad into Hermey Hall and find him in the Laboratory.*
We've already solved Prof Banas problem on Obj 6 Splunk... we continue into the dorm to look for Minty Candy.

[![](https://2019.kringlecon.com/images/avatars/elves/mintycandycane.png)](https://2019.kringlecon.com/images/avatars/elves/mintycandycane.png)

*Minty Candycane*
*Hi! I'm Minty Candycane!*
*I just LOVE this old game!*
*I found it on a 5 1/4" floppy in the attic.*
*You should give it a go!*
*If you get stuck at all, check out this year's talks.*
*One is about web application penetration testing.*
*Good luck, and don't get dysentery!*
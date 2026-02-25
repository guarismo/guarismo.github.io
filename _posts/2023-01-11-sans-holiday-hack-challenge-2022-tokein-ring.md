---
layout: post
title: "SANS Holiday Hack Challenge 2022 - Tokein RIng"
date: 2023-01-11T01:14:00.002Z
author: gu4r15sm0
---

# The Tolkein Ring

As we walk into the Talkain Ring area we found our old friend Sparkle Redberry, and like always, he needs our help.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEinJVpHTTRodcW0WcGVvvjknUXrcTkyl9LkwEixMUQkBmsNw6qI0xo17JJyqljUrC3B5ULX0elYiPi7mfQEdMzriO63D8IwR2NwrK5e6HYOuGHb4zsmUNuOurleKYfFyxziYN0q8eZMOAAhyOfe1WwQ9a6YKECky4lbKnxP_AU0eo6gaifqAxAvvw3H=w640-h374)](https://blogger.googleusercontent.com/img/a/AVvXsEinJVpHTTRodcW0WcGVvvjknUXrcTkyl9LkwEixMUQkBmsNw6qI0xo17JJyqljUrC3B5ULX0elYiPi7mfQEdMzriO63D8IwR2NwrK5e6HYOuGHb4zsmUNuOurleKYfFyxziYN0q8eZMOAAhyOfe1WwQ9a6YKECky4lbKnxP_AU0eo6gaifqAxAvvw3H)

We download the PCAP through the link he provides and we enter the terminal to find more instructions

[![](https://blogger.googleusercontent.com/img/a/AVvXsEi6RGd1Qvk6UAsMfOFJVeH1urHtrzIZivYW4dIEVZaEq9wLqq81a5N2dV1OLlee-Un6S1_RuUQJaIyr0nTuIjHw95I6Gaxc7v-8Oz4bkYcPpfjo8gE5tnNWQIvWWUK74Pg5Jai8Na7AfTqQVr-Q8afPK41ijqzeIkI-vCC4oSnel8G1O09jBjs7_hEg=w640-h610)](https://blogger.googleusercontent.com/img/a/AVvXsEi6RGd1Qvk6UAsMfOFJVeH1urHtrzIZivYW4dIEVZaEq9wLqq81a5N2dV1OLlee-Un6S1_RuUQJaIyr0nTuIjHw95I6Gaxc7v-8Oz4bkYcPpfjo8gE5tnNWQIvWWUK74Pg5Jai8Na7AfTqQVr-Q8afPK41ijqzeIkI-vCC4oSnel8G1O09jBjs7_hEg)

First question

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiKMLDE-vgztv4AIUWseVYhmZmbYwa5zRomqAvU39uGPUDwCN4GwKGuWYwauqMYOJ-2cKlZGwgpsnnGIvtVoCZH2xwjC6-d2FnaSTMBsXk6q9yKA5kl7NxJ9Z7wUShAP71sZdoEyFpVgBWZNYvcOMaFlVazrG9pGeQoUmEq-vmI3AR2KKKafS00oJaA=w640-h466)](https://blogger.googleusercontent.com/img/a/AVvXsEiKMLDE-vgztv4AIUWseVYhmZmbYwa5zRomqAvU39uGPUDwCN4GwKGuWYwauqMYOJ-2cKlZGwgpsnnGIvtVoCZH2xwjC6-d2FnaSTMBsXk6q9yKA5kl7NxJ9Z7wUShAP71sZdoEyFpVgBWZNYvcOMaFlVazrG9pGeQoUmEq-vmI3AR2KKKafS00oJaA)

We open the PCAP with Wireshark and go to the menu File --> Export Objects and choose **HTTP**... (that's the answer, HTTP)

We immediately see the files downloaded, this also give us the answer to the next question

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiZUcRDcacKACqutOXoV22jcrFIUC6wBiUfo34gWOKC2-LzNcF-F_aDMJbIxRgMZLpy7UjLBVw7BMGY8C3ysVofIfL3vLpD4C3uf1fepPT_2lK3YawBtyirLoFbBD3C2wz6ji2MfqqJqY0HRs1Ie_mUbUGRzx-Oz6Sb-QI3gFBzQQleX9kol_IXuW02)](https://blogger.googleusercontent.com/img/a/AVvXsEiZUcRDcacKACqutOXoV22jcrFIUC6wBiUfo34gWOKC2-LzNcF-F_aDMJbIxRgMZLpy7UjLBVw7BMGY8C3ysVofIfL3vLpD4C3uf1fepPT_2lK3YawBtyirLoFbBD3C2wz6ji2MfqqJqY0HRs1Ie_mUbUGRzx-Oz6Sb-QI3gFBzQQleX9kol_IXuW02)

HTTP Objects Downloaded

Using the screenshot above we can answer question 2.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhcD6VcRgQcO9y_EZoaI4SE0TjYQUw-7Vne_goW9BStB3MbtpQ0RMC3CziuizxpxlXz0dObWYfECpUMk_CY4hBBmmfdzSmRtNQzVrWqRTwz8WDtrDiZ_al5lG7Ig5tzQj3Hue1gGuoNlq9PZ1XKngKtJR1bXjCPsAl4E2gfui6Ph5QuBvH3y1IJ7Vc_=w640-h168)](https://blogger.googleusercontent.com/img/a/AVvXsEhcD6VcRgQcO9y_EZoaI4SE0TjYQUw-7Vne_goW9BStB3MbtpQ0RMC3CziuizxpxlXz0dObWYfECpUMk_CY4hBBmmfdzSmRtNQzVrWqRTwz8WDtrDiZ_al5lG7Ig5tzQj3Hue1gGuoNlq9PZ1XKngKtJR1bXjCPsAl4E2gfui6Ph5QuBvH3y1IJ7Vc_)

and 3

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiPQ47ifTXKxebQO-wunixEA9o0UR9vyJQipB6sFXui7rhRlm3ABQdY_zSRiKt0ZM8upjWYSKqoJkVjowz-15CSO4-7MzEvlNfMXEJtxGhEgUjHzH9VTmpkP_-c0qzQAItw8lnHqKqPkynRkE-ZyV_NoJIugX6sYsK0Yb_jgOvSoM0MF3Vlu5E6ucHT=w640-h212)](https://blogger.googleusercontent.com/img/a/AVvXsEiPQ47ifTXKxebQO-wunixEA9o0UR9vyJQipB6sFXui7rhRlm3ABQdY_zSRiKt0ZM8upjWYSKqoJkVjowz-15CSO4-7MzEvlNfMXEJtxGhEgUjHzH9VTmpkP_-c0qzQAItw8lnHqKqPkynRkE-ZyV_NoJIugX6sYsK0Yb_jgOvSoM0MF3Vlu5E6ucHT)

Now for question 4 we have to go a bit deeper and choose one of the HTTP streams to see that the responding HTTP headers says Server: Apache

[![](https://blogger.googleusercontent.com/img/a/AVvXsEh8oJMQjluioPsAtdgX0ABG0fToAdpGxyyoAen7ekci2mhceY_kR8i1GuDpGiwVAJtWg7ooSSlMP2nzwl7DKtpD3bkHpnQa1XusUhlq0diNbbfZUQlRfxCL53ki5Ys5i5IOh80Ers4jEdrSjjNLPq7VyyciTg-tILr1nU-5BYLSDnuDw4-Vwb2jnaLw=w640-h226)](https://blogger.googleusercontent.com/img/a/AVvXsEh8oJMQjluioPsAtdgX0ABG0fToAdpGxyyoAen7ekci2mhceY_kR8i1GuDpGiwVAJtWg7ooSSlMP2nzwl7DKtpD3bkHpnQa1XusUhlq0diNbbfZUQlRfxCL53ki5Ys5i5IOh80Ers4jEdrSjjNLPq7VyyciTg-tILr1nU-5BYLSDnuDw4-Vwb2jnaLw)

And there we can see the IP address to answer the question

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiqCSQtFtHVzPiiJ9QZMbt0yXpx4KWcYeV2fa_eIFQTWB8COUp5dqqssntMSsxuiEWt48i-_irwPY0En-rw8QTDMsgv3NjNKUC-OehOl26WrELGwug6YCz258RB6__sJexgvTNdEtgvTdHy4P-2qrDffpQ6zGHU7m7XjwNsbwgTdzT2q-tBdC9uSpVB=w640-h248)](https://blogger.googleusercontent.com/img/a/AVvXsEiqCSQtFtHVzPiiJ9QZMbt0yXpx4KWcYeV2fa_eIFQTWB8COUp5dqqssntMSsxuiEWt48i-_irwPY0En-rw8QTDMsgv3NjNKUC-OehOl26WrELGwug6YCz258RB6__sJexgvTNdEtgvTdHy4P-2qrDffpQ6zGHU7m7XjwNsbwgTdzT2q-tBdC9uSpVB)

Now for question file, we can save the app.php file to our computer from the Wireshark File menu using the same Export Object option as above.

To look at the last few lines of the file we use the tail command and we can see there's a blob being saved as **Ref_Sept21-2020.zip**, this is the answer.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhQaB83zMkclLzdUejy-2f7s-NZtIqfgH_9e0TAye1EGb1CU6McWHAdMx2oIWuAgHNrgL6ILdSvqc4gyqW92lYYL1vXhDs2tO7ew2lfioQlYXwyKBOmbiTkHcGaZSKb-6RV3PscjjImu6VugpS2NggaHFNbrPpwH2XuX4veep7TvYnU6PorLJ5uvcEU=w640-h196)](https://blogger.googleusercontent.com/img/a/AVvXsEhQaB83zMkclLzdUejy-2f7s-NZtIqfgH_9e0TAye1EGb1CU6McWHAdMx2oIWuAgHNrgL6ILdSvqc4gyqW92lYYL1vXhDs2tO7ew2lfioQlYXwyKBOmbiTkHcGaZSKb-6RV3PscjjImu6VugpS2NggaHFNbrPpwH2XuX4veep7TvYnU6PorLJ5uvcEU)

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjwuXPvhr6LUfQ9OpFLmJhNjxKa3QVcMEBR4jvvvrMjCAPnuWHh6jOY7HUJDwAhhlfamo_MtbL7Z_45sjgHnMWAc7QJNSvFqgJmd9gVeDtovI4IxvcdMKse0QGzrZqU_BP5OYpeyY8EqeHwv5FsSe-W_-7IwY-tYGeLA1m07PMXRf9yzayabCHJbNNp=w640-h314)](https://blogger.googleusercontent.com/img/a/AVvXsEjwuXPvhr6LUfQ9OpFLmJhNjxKa3QVcMEBR4jvvvrMjCAPnuWHh6jOY7HUJDwAhhlfamo_MtbL7Z_45sjgHnMWAc7QJNSvFqgJmd9gVeDtovI4IxvcdMKse0QGzrZqU_BP5OYpeyY8EqeHwv5FsSe-W_-7IwY-tYGeLA1m07PMXRf9yzayabCHJbNNp)

For question 6, things get a little bit more complicated, we needed to find TLS certificates exchanged in the traffic, to do that we use binwalk on the pcap

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjcuqw4SSV-qY-4LXEuekqAgTOjfar-kVth4Pv9909v_l82XyR66hex_x-X8VqCLhuAMV-8-XLucGms3Ru6qfhhgzzUJKo44cCrXSOD0iL4jZsKo37qu1biL26JJlSE6twolMhL9UVlfFGSdKxW0j_5R2SXl6A1iCqdBkA_qjB7IEc5Jg9DUbP7ZufN=w640-h505)](https://blogger.googleusercontent.com/img/a/AVvXsEjcuqw4SSV-qY-4LXEuekqAgTOjfar-kVth4Pv9909v_l82XyR66hex_x-X8VqCLhuAMV-8-XLucGms3Ru6qfhhgzzUJKo44cCrXSOD0iL4jZsKo37qu1biL26JJlSE6twolMhL9UVlfFGSdKxW0j_5R2SXl6A1iCqdBkA_qjB7IEc5Jg9DUbP7ZufN)

There are a lot of certificates in the file so we need to extract them to examine them

To examine the extracted files we can use find with exec to use openssl to make sure they are certs in DER format

find . -type f -exec openssl x509 -inform der -in {} -noout -text \\; 2>/dev/null| grep Issuer

[![](https://blogger.googleusercontent.com/img/a/AVvXsEj0hC305QzSAqFgFRE_IFW6fdfJx_z4x2qNasqU0qQFIypt0kkLxF2ENwXrvJc8ud_EV7QQSh6rT_-AOq7vO5tdNB-8ezB4zOjUurxWgAjcUVxCo9_9KMyLD07LO0GN44n7W1ur7Us27CuOuogBHbMYiQWoqh1TqB70TEfSKPcfacb4w7TdBvwiel7A=w640-h452)](https://blogger.googleusercontent.com/img/a/AVvXsEj0hC305QzSAqFgFRE_IFW6fdfJx_z4x2qNasqU0qQFIypt0kkLxF2ENwXrvJc8ud_EV7QQSh6rT_-AOq7vO5tdNB-8ezB4zOjUurxWgAjcUVxCo9_9KMyLD07LO0GN44n7W1ur7Us27CuOuogBHbMYiQWoqh1TqB70TEfSKPcfacb4w7TdBvwiel7A)

This gives us all the Issuers of the certificates found including the Country (C), State (ST), City/Location (L).

We can see 3 main countries:

C = IL for Israel

C = SS for South Sudan

C = US for United States

And we enter those as our answer, completing our first objective to find the Tolkein Ring.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjfNmBBqGktjG9ikeWoA8yjEJL06FIJTnU-gnDCBXWAf5_yJb4WNVfT0POcI2CyvtE7j8C9qtDJLU4TbgMa4dBHK9qvcmdrnAVb3Q9OoMBWWRsB74X6W9JP5ZdN91ubP6OFahtyXBOhWDE5w6Vr9g8R5Z9iKNrfw5pYRs7HzSYdmpDK_gkLvlotq8k8=w640-h154)](https://blogger.googleusercontent.com/img/a/AVvXsEjfNmBBqGktjG9ikeWoA8yjEJL06FIJTnU-gnDCBXWAf5_yJb4WNVfT0POcI2CyvtE7j8C9qtDJLU4TbgMa4dBHK9qvcmdrnAVb3Q9OoMBWWRsB74X6W9JP5ZdN91ubP6OFahtyXBOhWDE5w6Vr9g8R5Z9iKNrfw5pYRs7HzSYdmpDK_gkLvlotq8k8)

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhKnkX63ZCHIU_aqFK-wEKHTo8zc0V0z0E65n6L2dJyvlRLYq1n47iUhqFr8Q8T9R77uVSPTWZBxgWIeFgVeJKrqtQw9wQNfq2LLR4SBOnpWeyHtCF7MYQh9JzENxcCUGYvhKfDsD682vV5SiMkXxU7y1k1EldI1d8E5M9oktw2xURyL8FXAdAJVIqe=w640-h254)](https://blogger.googleusercontent.com/img/a/AVvXsEhKnkX63ZCHIU_aqFK-wEKHTo8zc0V0z0E65n6L2dJyvlRLYq1n47iUhqFr8Q8T9R77uVSPTWZBxgWIeFgVeJKrqtQw9wQNfq2LLR4SBOnpWeyHtCF7MYQh9JzENxcCUGYvhKfDsD682vV5SiMkXxU7y1k1EldI1d8E5M9oktw2xURyL8FXAdAJVIqe)

As we walk to the right on the same room we find Fitzy Shortstack and, of course, he has a problem to solve

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhqxotUgY_AEd8A7vMDPo_118tmO2eiLAJMIVm4o12FXwb3hcZE94NmwPAlyMN0GuODdbIuf150xdNy4kdXnzf5A6P1Le0GD88oJKOWblOqxj-pEUdaPOwahHFpS0-W6GH2EZeBQ4m3q4QWFs2Y2akedajwJUfk5G1F1AgKwnQjh62VwQTJOqnjUnqZ=w640-h254)](https://blogger.googleusercontent.com/img/a/AVvXsEhqxotUgY_AEd8A7vMDPo_118tmO2eiLAJMIVm4o12FXwb3hcZE94NmwPAlyMN0GuODdbIuf150xdNy4kdXnzf5A6P1Le0GD88oJKOWblOqxj-pEUdaPOwahHFpS0-W6GH2EZeBQ4m3q4QWFs2Y2akedajwJUfk5G1F1AgKwnQjh62VwQTJOqnjUnqZ)

We enter the terminal and find that we've been tasks with creating [suricata](https://suricata.readthedocs.io/en/suricata-6.0.0/what-is-suricata.html) rules to alert further communication with the Command and Control we discovered before

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiAZ-MbVHfmBBCFGrc77hl9oeziCBKC2edQCqnwxxF7TQ3A4AmzIxjE_pFP8B02UI-Eu2GcyqfzPW7RjEbPErI1sHeOhAyYqGVxG8vhNYHmUGHrXv7o4Ye53W3hpPkXaLfO-7-i7_wS49uN5fh345B9OID50S51FpzRZwCzlEcPU8zsd7hzWnXCZot3=w640-h380)](https://blogger.googleusercontent.com/img/a/AVvXsEiAZ-MbVHfmBBCFGrc77hl9oeziCBKC2edQCqnwxxF7TQ3A4AmzIxjE_pFP8B02UI-Eu2GcyqfzPW7RjEbPErI1sHeOhAyYqGVxG8vhNYHmUGHrXv7o4Ye53W3hpPkXaLfO-7-i7_wS49uN5fh345B9OID50S51FpzRZwCzlEcPU8zsd7hzWnXCZot3)

following the syntax we come up with arule for the first alert:

alert dns any any -> any any (msg:"Known bad DNS lookup, possible Dridex infection"; dns.query; content:"adv.epostoday.uk"; nocase; sid:20221211;rev:1;)

always changing the sid to avoid conflicts

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiUL0nqkligafXJlOemRhCxPpqr5MMxB6_kyE3cUxRY8Gd6wfn54Lvtg7tFyqYeTFK7luUpAxKmFHVE04ohnMxgUgoyWZlE1Rx8XZTxHoIuU_ab9W7XujnahNe8VUxClm7XEBPiuaFd0WQOqQ7KXfPMtJCSamrCR9kFh4tGLLoPvXztbiWxvbipYAIg=w640-h384)](https://blogger.googleusercontent.com/img/a/AVvXsEiUL0nqkligafXJlOemRhCxPpqr5MMxB6_kyE3cUxRY8Gd6wfn54Lvtg7tFyqYeTFK7luUpAxKmFHVE04ohnMxgUgoyWZlE1Rx8XZTxHoIuU_ab9W7XujnahNe8VUxClm7XEBPiuaFd0WQOqQ7KXfPMtJCSamrCR9kFh4tGLLoPvXztbiWxvbipYAIg)

We check our rule and get our next task

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhjMxWy4wtra476gFC88O6FHdkE5XhEQN-O0YbgrU-8Jr2pkfD4h8kkfizLI73k8GEftdpQ0zCsTVXujaynQaS4kYsUlJfCIYPZ6pTRx0P20ClRL_BRgWy19r_ngAWPoFjjV82484VXQScd09T9LUAdKKhFWxTLOVhNUlvn6G4-TDXhGAQDEdW244fP=w640-h382)](https://blogger.googleusercontent.com/img/a/AVvXsEhjMxWy4wtra476gFC88O6FHdkE5XhEQN-O0YbgrU-8Jr2pkfD4h8kkfizLI73k8GEftdpQ0zCsTVXujaynQaS4kYsUlJfCIYPZ6pTRx0P20ClRL_BRgWy19r_ngAWPoFjjV82484VXQScd09T9LUAdKKhFWxTLOVhNUlvn6G4-TDXhGAQDEdW244fP)

This one is bit trickier, we need 2 rules to catch of ways of communication, or at least with the little knowledge I have on suricata this is the way I found that worked find

alert http any any -> 192.185.57.242 any (msg:"Investigate suspicious connections, possible Dridex infection";sid:2022121122;rev:1;)

alert http 192.185.57.242 any -> any any (msg:"Investigate suspicious connections, possible Dridex infection";sid:2022121123;rev:1;)

[![](https://blogger.googleusercontent.com/img/a/AVvXsEheOIZlhgEszc2lNFYixQ3Nnf5R7rvt5X5sBzwDwtFOnvNcHsBug7YLH3aol4rLCFNmrJucNswollkfCItYtyBYRE-JsdJ_ewRb5rUqFWsU1Q_wioFv7uYfgesOpa1nxAYvs85Eoi9V7JrVBzRJ2BBpUOki2PR-CxrNmtI8hZpNd3vt1AHqiMNdTsyX=w640-h60)](https://blogger.googleusercontent.com/img/a/AVvXsEheOIZlhgEszc2lNFYixQ3Nnf5R7rvt5X5sBzwDwtFOnvNcHsBug7YLH3aol4rLCFNmrJucNswollkfCItYtyBYRE-JsdJ_ewRb5rUqFWsU1Q_wioFv7uYfgesOpa1nxAYvs85Eoi9V7JrVBzRJ2BBpUOki2PR-CxrNmtI8hZpNd3vt1AHqiMNdTsyX)

We verify again, and get our new task to identify bad certificates in the traffic

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiGsPSgl39Iq394tF0f-VztG7J-uoc6h-24lazY4oTzf6G0oI1UnG-gcliCnnJVc_z6nvDI-MtP4IJ9_amf9WGHuFm5ba7hfmpwY-IezPJUbELz0a98i3CWzLBG_vHRp02aw3i_pHbfq2AiCQKvdx4LkVVLaLY1ZHBEEYqmQ8m5ziaMPzoKN-SiDMIU=w640-h270)](https://blogger.googleusercontent.com/img/a/AVvXsEiGsPSgl39Iq394tF0f-VztG7J-uoc6h-24lazY4oTzf6G0oI1UnG-gcliCnnJVc_z6nvDI-MtP4IJ9_amf9WGHuFm5ba7hfmpwY-IezPJUbELz0a98i3CWzLBG_vHRp02aw3i_pHbfq2AiCQKvdx4LkVVLaLY1ZHBEEYqmQ8m5ziaMPzoKN-SiDMIU)

To accomplish this, we need suricata to examine the certificate that is used in the TLS handshake by verifying the CommonName (CN) in it:

alert tls any any -> any any (msg:"Investigate bad certificates, possible Dridex infection";tls.cert_subject; content:"CN=heardbellith.Icanwepeh.nagoya";sid:202212114;rev:1;)

[![](https://blogger.googleusercontent.com/img/a/AVvXsEg4qZL7E8JaAqU9iqMks_NZfY2hgiDtD3SnYmNpHUgmZMkA5-An1pw-rWEFCDxsSC0AWMdwtMJ5Mhy26XlVijn7Ps7aKqi7ywxwgcF10zqH9IcZ5PLqMHOEXPJegG1m-teXcC6HiLaGFl7lKXflVsPFSv91YuFNh8iBIU4xCMzDiKmrZXF-_TmVbClt=w640-h186)](https://blogger.googleusercontent.com/img/a/AVvXsEg4qZL7E8JaAqU9iqMks_NZfY2hgiDtD3SnYmNpHUgmZMkA5-An1pw-rWEFCDxsSC0AWMdwtMJ5Mhy26XlVijn7Ps7aKqi7ywxwgcF10zqH9IcZ5PLqMHOEXPJegG1m-teXcC6HiLaGFl7lKXflVsPFSv91YuFNh8iBIU4xCMzDiKmrZXF-_TmVbClt)

Once again, we verify our new rule and get one more task

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjoU2JKfe-dCF3NC_mSq9s-0rz3ilw2MWUr5yHSf8j6IYFKGSBy06H7eudRx7JWA9PLEPoWqECdAzigC9pGzdCrh9BMveEUTekDd16wwiQAiV1KVzlPb671tpY3DQeYtLFQYRUd4M-WBAFp8USH6mXza2Dz-P-NbaJkew8xCLC1V674l9odKhMh3-PI=w640-h304)](https://blogger.googleusercontent.com/img/a/AVvXsEjoU2JKfe-dCF3NC_mSq9s-0rz3ilw2MWUr5yHSf8j6IYFKGSBy06H7eudRx7JWA9PLEPoWqECdAzigC9pGzdCrh9BMveEUTekDd16wwiQAiV1KVzlPb671tpY3DQeYtLFQYRUd4M-WBAFp8USH6mXza2Dz-P-NbaJkew8xCLC1V674l9odKhMh3-PI)

We can find the malicous JavaScript by checking the body of the HTTP response to match the string we're looking for

alert http any any -> any any (msg:"Suspicious JavaScript function, possible Dridex infection"; flow:to_client;http.response_body;content:"let byteCharacters = atob";sid:5;)

And that's it! 

Now we can walk to tend of the hall and find Snowrog (any similarities with a Balrog is just coincidence) 

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjGqmgupSx66KsHn42ZA-P8G32MtRr4tlr4k8jqs3EAWZApFgal9AoBwJEkO_oi8OyaCSYty5csjRwOMez6W_WggsNNHWN5voUuNKXaDez1l-L7vEV60ReJUaXf25RErWRj8n31OJD8OTSj5wqAUk_pzGQOBLJ1v9x0OFppX0xJgn1D2sbZIjyXrbbu=w544-h640)](https://blogger.googleusercontent.com/img/a/AVvXsEjGqmgupSx66KsHn42ZA-P8G32MtRr4tlr4k8jqs3EAWZApFgal9AoBwJEkO_oi8OyaCSYty5csjRwOMez6W_WggsNNHWN5voUuNKXaDez1l-L7vEV60ReJUaXf25RErWRj8n31OJD8OTSj5wqAUk_pzGQOBLJ1v9x0OFppX0xJgn1D2sbZIjyXrbbu)

And here's the long lost Tolkein Ring

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiFowOLn2cw8nAzoxnfND7HHCx_0ibpr16qOWtbIu893KsgHV-IKzQc1_sk42TIOKPVq6uusl4O7hwtrUB0KJeFsqsoBGDJQPKXcSlYjgnbLqsDbSHtWKOC02mNjLoImsuVX14dZF5G6PFX__Q1XPaTeiHetlUc52Rk7aeI9EVuaXn1SwcGHZSinN6Q=w640-h386)](https://blogger.googleusercontent.com/img/a/AVvXsEiFowOLn2cw8nAzoxnfND7HHCx_0ibpr16qOWtbIu893KsgHV-IKzQc1_sk42TIOKPVq6uusl4O7hwtrUB0KJeFsqsoBGDJQPKXcSlYjgnbLqsDbSHtWKOC02mNjLoImsuVX14dZF5G6PFX__Q1XPaTeiHetlUc52Rk7aeI9EVuaXn1SwcGHZSinN6Q)
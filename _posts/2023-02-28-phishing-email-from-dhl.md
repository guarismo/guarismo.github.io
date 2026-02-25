---
layout: post
title: "Phishing email from \"DHL\""
date: 2023-02-28T20:50:00.002Z
author: gu4r15sm0
category: misc
tags: [phishing, email-security]
---

\*\*\* DON'T TRY THIS AT HOME/WORK\*\*\*\*

Today I got a wonderful Phisihng email, the attacker sent me a DHL bill for an account I don't have so that's the first red flag, then the attachment was an HTML instead of a PDF as stated in the email

[![](https://blogger.googleusercontent.com/img/a/AVvXsEgyFuxra6-QaHnGYGDvAyiDryAyfTA1GfjxYpN7DYiqESHKcZB6CDOATVHHzk1ZbR9kT5I55ZpubraIPrjpVszZYq4QzIrEhDlGs2nY4CotCaQKSglRqoBfM5VQm72ixF8sAMSX_ooeEGoAK-2x3lLoTPp2ijh_ZPrxmZ_uC3oX1hlMqviappUrUSEa=w555-h640)](https://blogger.googleusercontent.com/img/a/AVvXsEgyFuxra6-QaHnGYGDvAyiDryAyfTA1GfjxYpN7DYiqESHKcZB6CDOATVHHzk1ZbR9kT5I55ZpubraIPrjpVszZYq4QzIrEhDlGs2nY4CotCaQKSglRqoBfM5VQm72ixF8sAMSX_ooeEGoAK-2x3lLoTPp2ijh_ZPrxmZ_uC3oX1hlMqviappUrUSEa)

So I decided to take a look...

I downloaded the attachment and took a pick form my WSL Linux console

[![](https://blogger.googleusercontent.com/img/a/AVvXsEgfn1cSWL9FY3EDUvmQ8FX6Qch2WAtOhc4CoKbplHaQ0FX6CxW5WVQ-799q48t28uq2jb6T0f_WHg9AmOhbHTCYScUdFvogdUdWt6wxnxX_y0QmwuroKKYqi7GeVlmkWDQWdMEAehav8Xm02gVe5cemwvbkWNHNW2_lhFVcslwV1bN5ps-mLxdCzGKv=w547-h640)](https://blogger.googleusercontent.com/img/a/AVvXsEgfn1cSWL9FY3EDUvmQ8FX6Qch2WAtOhc4CoKbplHaQ0FX6CxW5WVQ-799q48t28uq2jb6T0f_WHg9AmOhbHTCYScUdFvogdUdWt6wxnxX_y0QmwuroKKYqi7GeVlmkWDQWdMEAehav8Xm02gVe5cemwvbkWNHNW2_lhFVcslwV1bN5ps-mLxdCzGKv)

Very interesting:

 
The HAKWELOTANIYDEK variable contains my email address
The Stivenkalvin variable has a base64 value that decoded becomes

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjRvnFYp3gnfXA7UHbb2hZeKbHXJ4UHVxCU0FbK1OyHlttBUe4VFz9CfSCQCNCq6Mv2ikA0OdPxJjOzyT__VYoN1Vv7cHskLywqYp5HB-Jn9RPIQ38udRrivpLNZtpFOWvqKRTPTrFh7pqIoWMF6cdG2YMAc7rwIPE58i22pgV88P96HdUV8qFdE_dh=w640-h70)](https://blogger.googleusercontent.com/img/a/AVvXsEjRvnFYp3gnfXA7UHbb2hZeKbHXJ4UHVxCU0FbK1OyHlttBUe4VFz9CfSCQCNCq6Mv2ikA0OdPxJjOzyT__VYoN1Vv7cHskLywqYp5HB-Jn9RPIQ38udRrivpLNZtpFOWvqKRTPTrFh7pqIoWMF6cdG2YMAc7rwIPE58i22pgV88P96HdUV8qFdE_dh)

 hXXp://ocbpremium.org/app/loi1hn.php so this website has been probably hacked and now host this PHP creds collector.

BTW, I try putting HAKWELOTANIYDEK in Google translate and the best match was a romanization of Arab 

[![](https://blogger.googleusercontent.com/img/a/AVvXsEg2bo71ZbmQZltzaO_aUETxoQVe6bFDpvH0B2N8jYe2jzQvqicGvZOUjKUj76APRNiYqGQUyJOJwLNZ_gh3k8ski67wOmwzUvftHAXS69BmaE8n8UYkY0uAajjt_qvUX7IdZFId0zX7f4fdxJJcdlujq7aILoF6d-KRcqsllLWJ7-4xvzbOx6dUZZeF=w400-h216)](https://blogger.googleusercontent.com/img/a/AVvXsEg2bo71ZbmQZltzaO_aUETxoQVe6bFDpvH0B2N8jYe2jzQvqicGvZOUjKUj76APRNiYqGQUyJOJwLNZ_gh3k8ski67wOmwzUvftHAXS69BmaE8n8UYkY0uAajjt_qvUX7IdZFId0zX7f4fdxJJcdlujq7aILoF6d-KRcqsllLWJ7-4xvzbOx6dUZZeF)

When trying that URL we get redirected to office.com, probably because we don't have the right parameters to give the PHP, making people think this is a legit website.

With curl -L we can follow any redirects, HTTP code 302 indicates a redirection and the Location where are we going.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjsaBT3pC7oGwpKngJ8DyP-8Vfj9b5gFTyyCKIY1D0iyP45scKDEeClcerFnT0LgY2pPrcDIGn-RejWa0eNosJVa8bXWiRYFelBxB7nyNmDqEzsiPmHN39ibkFcpFGFxp6mYeOdIXlFi4OoRmFwAhVxf7RhJB7GnglFZWWzOCdoUgVIlFUpu7CrwiiK=w563-h640)](https://blogger.googleusercontent.com/img/a/AVvXsEjsaBT3pC7oGwpKngJ8DyP-8Vfj9b5gFTyyCKIY1D0iyP45scKDEeClcerFnT0LgY2pPrcDIGn-RejWa0eNosJVa8bXWiRYFelBxB7nyNmDqEzsiPmHN39ibkFcpFGFxp6mYeOdIXlFi4OoRmFwAhVxf7RhJB7GnglFZWWzOCdoUgVIlFUpu7CrwiiK)

Now we have the rest of the base64 encoded JavaScript:

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjUDY8LF5G5Sb7jhmRIEIBI7Kkou2rmgsXxKp3ACCXw1EKabG2cD1x9yB1irgyGadpRgtfGY_eUYw9pvEB60XtdUnTO7q28bQFQVH1w0MtLtiXPJE5aZRZxdi7HH2WdNWXVkoQ10P7XBtA45F_MUvaroINSpmKgLMPr6lFxEzoveuZdwAmmXePHWZe7=w640-h350)](https://blogger.googleusercontent.com/img/a/AVvXsEjUDY8LF5G5Sb7jhmRIEIBI7Kkou2rmgsXxKp3ACCXw1EKabG2cD1x9yB1irgyGadpRgtfGY_eUYw9pvEB60XtdUnTO7q28bQFQVH1w0MtLtiXPJE5aZRZxdi7HH2WdNWXVkoQ10P7XBtA45F_MUvaroINSpmKgLMPr6lFxEzoveuZdwAmmXePHWZe7)

The atob() method decodes base64 to ASCII and document.write() populates the DOM document with the result loading all the code into the browser.

If we decode the base64 text we get HTML content with JavaScript that imitates a Microsoft login page, prepopulates the username with the value in the HAKWELOTANIYDEK, and even turns off autocomplete, no matter what you type for the password it will always return wrong password and send the information to their server.

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjRndosfBkq0GsckE6F3L6z5GtFfBY1MgYK2hdKfrwFOLAjpEcuXj00a-AZj5-PM8y7riGdytVWUcpOQ3AwvKFTyg-5Dw6gBBKv79wXwsX3I-EQLwRYvtQGsRp_mTBi16hAoY9BLTHzraB4e2nKlJNngqOFvRAGp1YcTFDTe40KS0OR0pp8ZaNAFZCW)](https://blogger.googleusercontent.com/img/a/AVvXsEjRndosfBkq0GsckE6F3L6z5GtFfBY1MgYK2hdKfrwFOLAjpEcuXj00a-AZj5-PM8y7riGdytVWUcpOQ3AwvKFTyg-5Dw6gBBKv79wXwsX3I-EQLwRYvtQGsRp_mTBi16hAoY9BLTHzraB4e2nKlJNngqOFvRAGp1YcTFDTe40KS0OR0pp8ZaNAFZCW)

Keeps replacing the URL on the browser with office.com with atob("aHR0cHM6Ly9vdXRsb29rLm9mZmljZS5jb20vbWFpbC8")

[![](https://blogger.googleusercontent.com/img/a/AVvXsEgCT6tG3fyq10LLdyvNbUOoWoK2IgFcWyEe4AaHkKGXhV-fCdzEESwEerF__D5xxkPUz-kb7V7q1Ohk5xrrCB69tbhWmhXLoaSaXMHECfFJcVdzyUy4j1mFL38nN-zs8A3yNYs8GK0o-X_HCh2TF1sLCwdBsnbiV4LktWm9Ft8PQVXkTugt2Jv-5IGI=s16000)](https://blogger.googleusercontent.com/img/a/AVvXsEgCT6tG3fyq10LLdyvNbUOoWoK2IgFcWyEe4AaHkKGXhV-fCdzEESwEerF__D5xxkPUz-kb7V7q1Ohk5xrrCB69tbhWmhXLoaSaXMHECfFJcVdzyUy4j1mFL38nN-zs8A3yNYs8GK0o-X_HCh2TF1sLCwdBsnbiV4LktWm9Ft8PQVXkTugt2Jv-5IGI)

[![](https://blogger.googleusercontent.com/img/a/AVvXsEhGcmjiFASJLHIYfRWZtTBi3c95b89OXxJXvVsTpVWfDBIfcLOr3fV-JyevH3e9Dr41McyVP2Ixg0GuYY5ZZHRFZMcZ6VnXWaEYu1tilS8fW-77qb_f7zCKPYOOMZZTzXdJ9vqZkJGWewKk6KXh1OYwiNYaftS4Uat7_b70EtOqRwBzmm2Gjx51J2Xa=w384-h640)](https://blogger.googleusercontent.com/img/a/AVvXsEhGcmjiFASJLHIYfRWZtTBi3c95b89OXxJXvVsTpVWfDBIfcLOr3fV-JyevH3e9Dr41McyVP2Ixg0GuYY5ZZHRFZMcZ6VnXWaEYu1tilS8fW-77qb_f7zCKPYOOMZZTzXdJ9vqZkJGWewKk6KXh1OYwiNYaftS4Uat7_b70EtOqRwBzmm2Gjx51J2Xa)

QUICK UPDATE, the hXXp://ocbpremium.org/ is a fake website 

[![](https://blogger.googleusercontent.com/img/a/AVvXsEiz9yv7nilbsQXQtN5ymBO8X7-EptfkR2Ctc_vxc3iYs1RRh5l9SYLmj8RlRIJUXFzifRRk1ndsVL_BFIQKqmShDHyVPEYPyoOTGrm3zCcfBhK0bkwifru9oau42tXoJSqy2LQusD-6VU3LX-dKdtYEEYk-ZKhZ5Xy236Y6d138OTholJBJW-H1ddcr=w640-h551)](https://blogger.googleusercontent.com/img/a/AVvXsEiz9yv7nilbsQXQtN5ymBO8X7-EptfkR2Ctc_vxc3iYs1RRh5l9SYLmj8RlRIJUXFzifRRk1ndsVL_BFIQKqmShDHyVPEYPyoOTGrm3zCcfBhK0bkwifru9oau42tXoJSqy2LQusD-6VU3LX-dKdtYEEYk-ZKhZ5Xy236Y6d138OTholJBJW-H1ddcr)

The DNS is hosted by NameCheap 🚩🚩🚩🚩

[![](https://blogger.googleusercontent.com/img/a/AVvXsEjsU85EejkmgqGS0lFFyJjkuN6sO77Dypd_dxWjz9uZhhiGkhye8Y6OZP6TPoc7d_WXowUQ_V9BJ_nXZz_t9ogjl3a8XIApPSWOg2hnbD7erzTGwwxAzhv6wT-M14X3_FUz892Y0aLKA_6ykozEdjpScD5Jd2ugWgQxy6r2D92gm2QT8mPpPK9VYw89=w640-h567)](https://blogger.googleusercontent.com/img/a/AVvXsEjsU85EejkmgqGS0lFFyJjkuN6sO77Dypd_dxWjz9uZhhiGkhye8Y6OZP6TPoc7d_WXowUQ_V9BJ_nXZz_t9ogjl3a8XIApPSWOg2hnbD7erzTGwwxAzhv6wT-M14X3_FUz892Y0aLKA_6ykozEdjpScD5Jd2ugWgQxy6r2D92gm2QT8mPpPK9VYw89)
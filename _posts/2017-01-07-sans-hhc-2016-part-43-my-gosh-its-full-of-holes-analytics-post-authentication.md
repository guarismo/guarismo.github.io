---
layout: post
title: "SANS HHC 2016 - Part 4.3: My Gosh... It's Full of Holes - Analytics (post authentication)"
date: 2017-01-07T02:14:00.001Z
author: gu4r15sm0
category: sans-hhc
tags: [sans, hhc-2016, ctf]
---

## SANS Holiday Hack Challenge 2016

### Continuing Part 4: My Gosh... It's Full of Holes - Analytics (post authentication)

Nmap shows that a .git directory is available for download in [https://analytics.northpolewonderland.com/.git](https://analytics.northpolewonderland.com/.git)

I get a copy of it with a ﻿

wget -r https://analytics.northpolewonderland.com/.git/

﻿cd analytics.northpolewonderland.com/

[![](/assets/images/git-status.JPG)](/assets/images/git-status.JPG)

git status

[![](/assets/images/git-checkout.JPG)](/assets/images/git-checkout.JPG)

git checkout for all the PHP source code

 And I look at the commit history and found interesting messages about the database dump sprusage.sql

[![](/assets/images/sql-dump.jpg)](/assets/images/sql-dump.jpg)

git log interesting comments

I get an old version of the ***sprusage.sql*** file to find the administrator user and password

[![](/assets/images/analytics-admin-pass_LI.jpg)](/assets/images/analytics-admin-pass_LI.jpg)

Old version of db dump has the administrator's password

From the commits messages I also noticed that it seems the application is storing SQL queries in the database.

The ***edit.php*** file has a warning about being experimental, this PHP file enables the user with access to change the values in the reports table, where the “***query***” field can be injected into the HTTP GET request and since it’s not sanitized before it’s stored in the ***reports*** table, it can be executed by the ***view.php*** if a correct id is entered.

[![](/assets/images/analytics-edit-warning.php.jpg)](/assets/images/analytics-edit-warning.php.jpg)

edit.php WARNING!!

[![](/assets/images/edit-sql-vuln.JPG)](/assets/images/edit-sql-vuln.JPG)

edit.php vulnerability

[![](/assets/images/sql-exec-query.JPG)](/assets/images/sql-exec-query.JPG)

view.php executes the content of the "query"row from the reports table

[![](/assets/images/db-php.jpg)](/assets/images/db-php.jpg)

administrator is a valid user account

The ***sprusage.sql*** shows that there’s an audio table that contains an mp3 file for each user id, this is where the ***getaudio.php*** get’s the mp3 files from, but only to the **guest** user is allowed to download this way, so it won’t work for the **administrator** user.

[![](/assets/images/sql-audio.jpg)](/assets/images/sql-audio.jpg)

'audio' table contains mp3 blobs

First I need to login as administrator to create a report to obtain a report ***id***.

[![](/assets/images/analytics-report-created.jpg)](/assets/images/analytics-report-created.jpg)

Report created and got valid id to use in edit.php and view.php

 Then I can modify it with the ***edit.php*** using the ***id*** and add a new query to it.

[![](/assets/images/analytics-edit.JPG)](/assets/images/analytics-edit.JPG)

Testing edit.php normal functionality

[![](/assets/images/analytics-edit-normal.jpg)](/assets/images/analytics-edit-normal.jpg)

Result of edit.php shows query being exectuted

Here’s the URL with the SQL injection to list the content of the audio table. 

https://analytics.northpolewonderland.com/edit.php?id=700ff62d-cd01-4031-a52e-c6df60a0eb31&name=HACK&description=HACK+SQLI&query=SELECT%20\*%20from%20audio

[![](/assets/images/analytics-edit-select-audio.jpg)](/assets/images/analytics-edit-select-audio.jpg)

Result of edit.php with SQL injection adding the query field to list the content of the audio table

[![](/assets/images/audio-admin-found.JPG)](/assets/images/audio-admin-found.JPG)

Using view.php and the report id it shows the file we want but the blob doesn't show

I use the ***edit.php*** to insert a new query that will return the content of the mp3 file in base64 format:

*SELECT TO_BASE64(**mp3**) from **audio** where **username**\=’administrator’*

[![](/assets/images/sql-base64-audio.jpg)](/assets/images/sql-base64-audio.jpg)

Use edit.php again to extract the mp3 blob to base64

And use the ***view.php*** with the ***id*** again to execute my new query, which shows the presence of another audio file in the audio table, so now it’s just a matter of running a query to extract the mp3 field in some text format.

[![](/assets/images/sql-result-base64-mp3.JPG)](/assets/images/sql-result-base64-mp3.JPG)

I selected the base64 result and store it in my Kali linux to be decoded back to mp3 using the base64 command.


```bash
root@igor-kali:~/hhack2016/analytics.northpolewonderland.com# cat mp3.b64 |sed ‘s/ //g’ | base64 -d > discombobulatedaudio7.mp3

root@igor-kali:~/hhack2016/analytics.northpolewonderland.com# file discombobulatedaudio7.mp3
```
```


discombobulatedaudio7.mp3: Audio file with ID3 version 2.3.0, contains: MPEG ADTS, layer III, v1, 128 kbps, 44.1 kHz, JntStereo

Done!
We have all 7 MP3 files
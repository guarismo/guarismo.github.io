---
layout: post
title: "Python -  HTTPS Client using Client certificate"
date: 2017-02-14T02:10:00.001Z
author: gu4r15sm0
category: misc
tags: [python, ssl, certificates]
---

## Python HTTPS Client using a Client certificate

My client has requested a way to send data from Splunk to another application, using HTTPS POST, but there's also a requirement to authenticate the HTTPS Client using a certificate.
In Splunk you can create a [Custom Alert action](http://docs.splunk.com/Documentation/Splunk/latest/AdvancedDev/ModAlertsIntro) app, this is perfect for this solution because packaging everything in an app and deploying it to the Search Head clusters takes care of the High Availability and management of the application, all these features are already built-in in Splunk.
I've decided to use Python because Splunk includes an interpreter already and it provides a lot of useful environment variables that can be passed as arguments to my functions to extract the data and formatted the way the Web Service understands it.
For this post I'm not sharing the Splunk alert app because it's very particular for my client, I'm only going to talk about building a quick test environment an a python script to send an HTTPS POST request with some data.
I'm using:
OpenSSL: to create a self-signed CA for my client and server certs
nginx: a very simple webserver to host the Server cert and authenticate the client, no application is going to be hosted to understand the data.
Python 2.7.x: since this is the version included with Splunk.
Creating a Certificate Authority for testing purposes:
First we need a Private key for our local CA, using OpenSSL I select RSA to create a 4096 bit long key:
[igor@splunk01 ca]$ openssl genrsa -des3 -out ca.key 4096

```text
Generating RSA private key, 4096 bit long modulus
............................................................................................................................................................................................................................................................................................................................................................................++
...........................................................++
e is 65537 (0x10001)

```text
Enter pass phrase for ca.key:
Verifying - Enter pass phrase for ca.key:
```
```


Then we create our local CA certificate (signed public key) with a 1 year (365 days) validity period and store in in ca.crt signed using our private key ca.key
[igor@splunk01 ca]$ openssl req -new -x509 -days 365 -key ca.key -out ca.crt

```text
Enter pass phrase for ca.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
```

For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CA
State or Province Name (full name) []:Ontario
Locality Name (eg, city) [Default City]:Toronto
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server's hostname) []:gu4r15m0
Email Address []:
And now we have a CA root certificate and we can read it with OpenSSL:
[igor@splunk01 ca]$ openssl x509 -in ca.crt -text -noout

```text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 16160480175172978768 (0xe0458f18f0ed8850)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=CA, ST=Ontario, L=Toronto, CN=gu4r15m0
        Validity
            Not Before: Feb 13 00:45:05 2017 GMT
            Not After : Feb 13 00:45:05 2018 GMT
        Subject: C=CA, ST=Ontario, L=Toronto, CN=gu4r15m0
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:ae:dd:34:de:4a:9a:82:21:0c:b0:d2:0b:a3:4c:
                    e8:78:85:83:5d:08:69:42:71:1f:c5:80:4b:34:b2:
                    e7:48:ef:d8:7d:26:af:0b:89:56:20:37:8f:85:2b:
                    aa:74:2f:80:97:b6:5e:45:01:94:d9:63:b9:05:f1:
                    c9:4a:95:e0:f5:3c:c4:28:9d:b4:14:3f:51:e7:a1:
                    28:9f:23:0e:c6:64:6e:ec:50:5c:f6:45:71:14:90:
                    5b:a2:88:53:ba:53:8e:43:10:e7:65:ec:d8:32:ae:
                    a9:e9:6d:a5:b0:7d:e6:31:dd:1e:dc:c0:25:c1:fb:
                    08:f6:72:92:c5:28:61:fe:6f:a9:80:2f:9e:36:77:
                    b6:88:13:87:48:d7:8d:d7:d6:6e:e9:b9:7b:84:d3:
                    4a:1c:a2:0d:52:94:64:2e:c9:90:5d:5b:5f:19:f0:
                    3e:70:aa:fa:70:bb:80:1d:0a:e6:68:e0:4b:e8:96:
                    9b:a4:6e:40:b3:a6:d5:af:b1:10:c9:51:5a:be:c7:
                    3f:28:2d:80:a8:72:8b:8d:63:96:7d:f7:35:1d:13:
                    40:f0:f3:ad:6c:f4:8f:cd:54:ae:41:1b:88:b8:23:
                    09:d2:5d:f4:93:e4:1f:fc:e6:43:0f:de:ac:04:19:
                    62:db:3f:25:37:bb:59:34:83:ac:35:6e:85:b4:9e:
                    8a:9c:af:a6:32:54:09:43:fe:f0:87:e4:ff:1e:6d:
                    b8:75:35:a9:59:21:1f:ea:15:e2:da:24:ed:e9:c0:
                    3d:87:a9:56:cd:39:b3:70:12:7f:4d:2d:88:4b:c7:
                    b4:c6:d6:3c:26:e3:63:19:9a:1f:36:76:22:59:84:
                    8b:c0:13:9c:aa:31:9a:30:36:96:88:3c:b3:45:61:
                    b0:e1:69:30:75:89:d6:60:f7:78:3d:db:2c:2c:d0:
                    a6:ce:01:28:49:10:08:9d:3b:e1:1b:d6:25:2b:28:
                    ae:2f:f8:60:83:da:8a:26:8e:53:a4:32:81:8c:8d:
                    ed:89:d2:a6:87:ea:99:34:a4:cd:52:fb:19:bf:30:
                    a9:ed:18:4d:1e:fc:cb:43:a0:ec:cf:29:28:df:d9:
                    8e:58:a8:aa:94:61:03:9c:a9:5e:b7:fa:e9:f6:84:
                    83:48:66:d9:af:ea:9f:04:6f:b3:32:2f:87:57:3d:
                    df:8b:5b:7f:3c:fb:ba:8a:f9:a9:a2:4b:19:7b:01:
                    27:5e:00:04:22:ec:85:93:7d:fa:19:c4:e7:a4:dc:
                    0d:30:57:7a:50:f2:23:68:70:99:6c:86:13:42:94:
                    2a:36:ba:87:ba:59:ad:d9:ff:f0:dd:4d:b4:eb:76:
                    4e:04:a3:48:9b:05:4a:95:0d:d5:cf:d4:80:e8:41:
                    6b:53:45
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                BD:14:46:8F:4A:00:36:95:BA:22:44:D6:8B:04:A5:BD:E6:6F:B3:83
            X509v3 Authority Key Identifier:
                keyid:BD:14:46:8F:4A:00:36:95:BA:22:44:D6:8B:04:A5:BD:E6:6F:B3:83
            X509v3 Basic Constraints:
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         83:f3:4c:2b:13:9d:90:ae:50:60:fc:d0:a7:64:94:41:87:45:
         a5:18:2d:18:31:39:ec:7a:b4:49:3e:c6:bc:38:6c:d6:20:54:
         77:55:18:d7:70:2b:d7:d0:63:fa:7c:8d:3b:13:00:14:1b:67:
         8c:20:d5:18:2f:3b:5b:b0:26:31:04:4c:03:22:e8:8d:49:a9:
         b7:fa:02:fe:7d:ab:b6:2f:25:f7:73:59:50:36:7a:9d:4c:6b:
         85:9f:88:f7:54:e7:48:da:9a:52:ae:0a:b7:a3:96:85:f5:b8:
         e1:09:64:12:75:b0:71:76:e6:fb:f1:70:2c:7e:d8:90:13:d5:
         89:f9:0b:33:2a:ec:b6:44:e7:08:da:54:d8:2e:bf:68:e1:d0:
         f2:a7:fc:aa:94:83:89:d5:74:ea:2c:bc:b2:32:81:52:c5:cd:
         34:b2:c6:74:8d:cf:7e:a1:dd:fc:20:41:50:13:0d:79:71:09:
         2f:76:36:38:73:cd:45:ae:89:64:98:7e:1a:25:72:7a:83:17:
         f0:d3:29:59:ba:2b:61:1b:7f:43:57:bf:94:5e:ea:c3:e9:ca:
         e8:2c:a7:ec:88:ad:be:a7:00:02:31:e9:60:fa:7c:02:d8:20:
         ec:e8:27:3b:a4:65:a7:db:b3:a5:fe:5e:6d:f4:28:9d:c6:50:
         7b:03:f2:b3:e4:52:d2:ea:18:5f:04:2c:09:57:0e:1e:7e:74:
         dd:3b:d1:7c:47:f9:17:7c:2a:1e:42:77:dd:5b:3c:28:da:84:
         8e:52:a7:ae:70:96:8c:0f:76:1b:0f:63:91:5d:38:f3:1a:94:
         c7:d9:9a:85:7b:4c:17:25:8f:90:48:92:b1:ba:19:a5:62:ba:
         72:86:74:02:09:67:cc:33:75:63:69:52:1f:2f:e5:50:e9:c4:
         d8:0b:1c:97:b8:10:8e:b9:5c:9e:94:cf:63:c2:4b:23:2c:6b:
         c1:87:0d:86:58:6b:3f:95:d8:7a:eb:15:23:62:53:5c:39:5b:
         b1:b8:4c:bb:f6:32:6a:69:ce:9e:99:d9:93:4a:c7:69:5d:e3:
         7e:60:69:2c:f7:c6:1d:05:61:1d:dd:c7:c7:a7:5e:f5:03:f0:
         9c:08:dc:15:9e:a7:d6:7d:16:f5:b6:df:ed:82:e7:7c:a5:a1:
         59:50:83:21:16:cf:44:c9:6d:20:98:5d:1e:6c:8a:75:2d:4b:
         38:24:99:1b:a8:8f:fd:86:5f:73:b7:2f:22:ad:c2:55:da:f0:
         ee:55:3e:0c:62:db:6b:ee:d5:c7:ae:53:af:21:41:65:8e:31:
         1d:fd:d6:90:8e:4a:1b:1f:a3:b9:8a:87:4b:0a:4c:a1:a8:a8:
         1b:2a:2b:5e:4a:28:56:4f
```

Now we can create our client side certificate:

First we create a Private key for our client:
[igor@splunk01 ca]$ openssl genrsa -des3 -out client.key 4096

```text
Generating RSA private key, 4096 bit long modulus
........................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................++
............++
e is 65537 (0x10001)

```text
Enter pass phrase for client.key:
Verifying - Enter pass phrase for client.key:
```

Then we create a Certificate Signing Request (CSR) using the private key:
[igor@splunk01 ca]$ openssl req -new -key client.key -out client.csr
Enter pass phrase for client.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
```

For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CA
State or Province Name (full name) []:Ontario
Locality Name (eg, city) [Default City]:Toronto
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server's hostname) []:gu4r15m0
Email Address []:
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
We can look at our Base64 CSR:
[igor@splunk01 ca]$ cat client.csr
-----BEGIN CERTIFICATE REQUEST-----
MIIEpzCCAo8CAQAwYjELMAkGA1UEBhMCQ0ExEDAOBgNVBAgMB09udGFyaW8xEDAO
BgNVBAcMB1Rvcm9udG8xHDAaBgNVBAoME0RlZmF1bHQgQ29tcGFueSBMdGQxETAP
BgNVBAMMCGd1NHIxNW0wMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA
sHGhMF53g9gbZxmoNMURekl+/7fxYGG1LAdl/cHrS/sIxeLMbqmBGGsivYwJo4L6
FpSne/pIdwWs0T/rTnDIbFD02tX6kYt734CNT8UWZ/Ty1URIBplcHyZOs4PNWMB0
fi1JDaW8OBCBEEsiIU4y8OdOUFziKru4JwKGTlL8Zv+STRvfxHFcK3Z+p50gq44j
VtAEmOicv5nnKxVCu1M0fe09yrYSmVe0SyYGLkYhddnEWcBfSmNb5k8z+Otq9gdJ
SlcNGn7a3sa6CiYKDJJotQdBvsIsDsgaBMlZKtzY8Hc2lQ25+2c1uFHu4XjyY5wf
WedBEPpdaQQxxETM4NFn3QomU9LyDRW7EZlcUi46pos1ARjEB0qzzAsDUmaZeVyP
q9XEArQb6QK9lgo8vT2ncY5aZl5FTbMrYr6/LMOgewArjnDSgFu/p5Mi8luMQvL6
JBfC9MvVuimp9BDM7dilqoXkGb+BWGHSHy/j4uwZxrX/tRR5IOh5byDdNfsQlPeY
zJH249OZF4zrF3QTyU2rfkUPqq8JzuJeigkgFOl9OfnlLVYPU08d1kFPJnDSe11T
zWiURc9bsGvyN6JGJwysZQHJRk24MKjqFDGdHwCqtpXiVw4QIY4Z2g1dIv3LE/0D
Y5jk10r8Lok31w8P4VoRujtcCEF1OOnFzdZg9Y5dBN8CAwEAAaAAMA0GCSqGSIb3
DQEBCwUAA4ICAQBHZmWmn1shjfQLRHcVQ8nPc+nCy0OMkd/cuda3va6hUfR77i4q
qPQ0PxQOJi3BAcOKcMIUebKuvu2ICJlpZ4ICD40sXABtx03EOKjsq7f16wpPJ3xW
KDoa67nZHeKH1cRzDKNlFRT5agyaEMJRAlKcodmyQqqHdZVySBnOzExvWSYbANT/
u7AZZ+ocDzJIcqvtqyhGAah4qyfjfJTqXhyUhYlPjYw2uj6duEQu68zIcPwW/d+u
fG4Pi+NZtmPSkv32s+aOwkunfNX6cJepSkf91x11juPy1gfHS8WZtxHnCm6EUtJc
waLY/rHBnedL7UXylLsNCKup9mBckHN5J77P15PxgrbCylVoHvcq9mLRWtyRIv1P
RRByg29CtOIAD6x7hHCGmzuSltVDA5N39TDF+XJRsYGN3QILBr8MTy/+bc/l5yGO
SvHIYpWG7gBKiBzlMceDRm3HzT7HEewNRQnpn4XuTmHoWiSPTow5ZOA0842qake+
Tami5UpwltBhDdJylxdM8Rded5I9mGBHMAsUsQZMzfF626ZFwsK5PbLT//wXWzSO
tO7Th9xvT0h5Vtcibly83d7mUOdM3PGg3dsbADvCWoZd5n7Ke7/LQYPZx02O2Bjs
sXcIdxSXxwbKFYEwlktgxnFAe85acA8E59NEvCMAGqgibkuXE4d6zCfiWw==
-----END CERTIFICATE REQUEST-----
[igor@splunk01 ca]$
Or using OpenSSL for the decode the Base64:
[igor@splunk01 ca]$ openssl req -in client.csr -text -noout
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=CA, ST=Ontario, L=Toronto, O=Default Company Ltd, CN=gu4r15m0
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:

```text
                    00:b0:71:a1:30:5e:77:83:d8:1b:67:19:a8:34:c5:
                    11:7a:49:7e:ff:b7:f1:60:61:b5:2c:07:65:fd:c1:
                    eb:4b:fb:08:c5:e2:cc:6e:a9:81:18:6b:22:bd:8c:
                    09:a3:82:fa:16:94:a7:7b:fa:48:77:05:ac:d1:3f:
                    eb:4e:70:c8:6c:50:f4:da:d5:fa:91:8b:7b:df:80:
                    8d:4f:c5:16:67:f4:f2:d5:44:48:06:99:5c:1f:26:
                    4e:b3:83:cd:58:c0:74:7e:2d:49:0d:a5:bc:38:10:
                    81:10:4b:22:21:4e:32:f0:e7:4e:50:5c:e2:2a:bb:
                    b8:27:02:86:4e:52:fc:66:ff:92:4d:1b:df:c4:71:
                    5c:2b:76:7e:a7:9d:20:ab:8e:23:56:d0:04:98:e8:
                    9c:bf:99:e7:2b:15:42:bb:53:34:7d:ed:3d:ca:b6:
                    12:99:57:b4:4b:26:06:2e:46:21:75:d9:c4:59:c0:
                    5f:4a:63:5b:e6:4f:33:f8:eb:6a:f6:07:49:4a:57:
                    0d:1a:7e:da:de:c6:ba:0a:26:0a:0c:92:68:b5:07:
                    41:be:c2:2c:0e:c8:1a:04:c9:59:2a:dc:d8:f0:77:
                    36:95:0d:b9:fb:67:35:b8:51:ee:e1:78:f2:63:9c:
                    1f:59:e7:41:10:fa:5d:69:04:31:c4:44:cc:e0:d1:
                    67:dd:0a:26:53:d2:f2:0d:15:bb:11:99:5c:52:2e:
                    3a:a6:8b:35:01:18:c4:07:4a:b3:cc:0b:03:52:66:
                    99:79:5c:8f:ab:d5:c4:02:b4:1b:e9:02:bd:96:0a:
                    3c:bd:3d:a7:71:8e:5a:66:5e:45:4d:b3:2b:62:be:
                    bf:2c:c3:a0:7b:00:2b:8e:70:d2:80:5b:bf:a7:93:
                    22:f2:5b:8c:42:f2:fa:24:17:c2:f4:cb:d5:ba:29:
                    a9:f4:10:cc:ed:d8:a5:aa:85:e4:19:bf:81:58:61:
                    d2:1f:2f:e3:e2:ec:19:c6:b5:ff:b5:14:79:20:e8:
                    79:6f:20:dd:35:fb:10:94:f7:98:cc:91:f6:e3:d3:
                    99:17:8c:eb:17:74:13:c9:4d:ab:7e:45:0f:aa:af:
                    09:ce:e2:5e:8a:09:20:14:e9:7d:39:f9:e5:2d:56:
                    0f:53:4f:1d:d6:41:4f:26:70:d2:7b:5d:53:cd:68:
                    94:45:cf:5b:b0:6b:f2:37:a2:46:27:0c:ac:65:01:
                    c9:46:4d:b8:30:a8:ea:14:31:9d:1f:00:aa:b6:95:
                    e2:57:0e:10:21:8e:19:da:0d:5d:22:fd:cb:13:fd:
                    03:63:98:e4:d7:4a:fc:2e:89:37:d7:0f:0f:e1:5a:
                    11:ba:3b:5c:08:41:75:38:e9:c5:cd:d6:60:f5:8e:
                    5d:04:df
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         47:66:65:a6:9f:5b:21:8d:f4:0b:44:77:15:43:c9:cf:73:e9:
         c2:cb:43:8c:91:df:dc:b9:d6:b7:bd:ae:a1:51:f4:7b:ee:2e:
         2a:a8:f4:34:3f:14:0e:26:2d:c1:01:c3:8a:70:c2:14:79:b2:
         ae:be:ed:88:08:99:69:67:82:02:0f:8d:2c:5c:00:6d:c7:4d:
         c4:38:a8:ec:ab:b7:f5:eb:0a:4f:27:7c:56:28:3a:1a:eb:b9:
         d9:1d:e2:87:d5:c4:73:0c:a3:65:15:14:f9:6a:0c:9a:10:c2:
         51:02:52:9c:a1:d9:b2:42:aa:87:75:95:72:48:19:ce:cc:4c:
         6f:59:26:1b:00:d4:ff:bb:b0:19:67:ea:1c:0f:32:48:72:ab:
         ed:ab:28:46:01:a8:78:ab:27:e3:7c:94:ea:5e:1c:94:85:89:
         4f:8d:8c:36:ba:3e:9d:b8:44:2e:eb:cc:c8:70:fc:16:fd:df:
         ae:7c:6e:0f:8b:e3:59:b6:63:d2:92:fd:f6:b3:e6:8e:c2:4b:
         a7:7c:d5:fa:70:97:a9:4a:47:fd:d7:1d:75:8e:e3:f2:d6:07:
         c7:4b:c5:99:b7:11:e7:0a:6e:84:52:d2:5c:c1:a2:d8:fe:b1:
         c1:9d:e7:4b:ed:45:f2:94:bb:0d:08:ab:a9:f6:60:5c:90:73:
         79:27:be:cf:d7:93:f1:82:b6:c2:ca:55:68:1e:f7:2a:f6:62:
         d1:5a:dc:91:22:fd:4f:45:10:72:83:6f:42:b4:e2:00:0f:ac:
         7b:84:70:86:9b:3b:92:96:d5:43:03:93:77:f5:30:c5:f9:72:
         51:b1:81:8d:dd:02:0b:06:bf:0c:4f:2f:fe:6d:cf:e5:e7:21:
         8e:4a:f1:c8:62:95:86:ee:00:4a:88:1c:e5:31:c7:83:46:6d:
         c7:cd:3e:c7:11:ec:0d:45:09:e9:9f:85:ee:4e:61:e8:5a:24:
         8f:4e:8c:39:64:e0:34:f3:8d:aa:6a:47:be:4d:a9:a2:e5:4a:
         70:96:d0:61:0d:d2:72:97:17:4c:f1:17:5e:77:92:3d:98:60:
         47:30:0b:14:b1:06:4c:cd:f1:7a:db:a6:45:c2:c2:b9:3d:b2:
         d3:ff:fc:17:5b:34:8e:b4:ee:d3:87:dc:6f:4f:48:79:56:d7:
         22:6e:5c:bc:dd:de:e6:50:e7:4c:dc:f1:a0:dd:db:1b:00:3b:
         c2:5a:86:5d:e6:7e:ca:7b:bf:cb:41:83:d9:c7:4d:8e:d8:18:
         ec:b1:77:08:77:14:97:c7:06:ca:15:81:30:96:4b:60:c6:71:
         40:7b:ce:5a:70:0f:04:e7:d3:44:bc:23:00:1a:a8:22:6e:4b:
         97:13:87:7a:cc:27:e2:5b
```

Now we have that we have a CSR, we submit it to our CA or signing using the CA private key (ca.key) and creating the Certificate client.crt 
[igor@splunk01 ca]$ openssl x509 -req -sha256 -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt
Signature ok
subject=/C=CA/ST=Ontario/L=Toronto/O=Default Company Ltd/CN=gu4r15m0
Getting CA Private Key
Enter pass phrase for ca.key:
And now we have a certificate that we can read it with OpenSSL:
[igor@splunk01 ca]$ openssl x509 -in client.crt -text -noout

```text
Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number: 1 (0x1)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=CA, ST=Ontario, L=Toronto, CN=gu4r15m0
        Validity
            Not Before: Feb 13 01:08:45 2017 GMT
            Not After : Feb 13 01:08:45 2018 GMT
        Subject: C=CA, ST=Ontario, L=Toronto, O=Default Company Ltd, CN=gu4r15m0
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    00:b0:71:a1:30:5e:77:83:d8:1b:67:19:a8:34:c5:
                    11:7a:49:7e:ff:b7:f1:60:61:b5:2c:07:65:fd:c1:
                    eb:4b:fb:08:c5:e2:cc:6e:a9:81:18:6b:22:bd:8c:
                    09:a3:82:fa:16:94:a7:7b:fa:48:77:05:ac:d1:3f:
                    eb:4e:70:c8:6c:50:f4:da:d5:fa:91:8b:7b:df:80:
                    8d:4f:c5:16:67:f4:f2:d5:44:48:06:99:5c:1f:26:
                    4e:b3:83:cd:58:c0:74:7e:2d:49:0d:a5:bc:38:10:
                    81:10:4b:22:21:4e:32:f0:e7:4e:50:5c:e2:2a:bb:
                    b8:27:02:86:4e:52:fc:66:ff:92:4d:1b:df:c4:71:
                    5c:2b:76:7e:a7:9d:20:ab:8e:23:56:d0:04:98:e8:
                    9c:bf:99:e7:2b:15:42:bb:53:34:7d:ed:3d:ca:b6:
                    12:99:57:b4:4b:26:06:2e:46:21:75:d9:c4:59:c0:
                    5f:4a:63:5b:e6:4f:33:f8:eb:6a:f6:07:49:4a:57:
                    0d:1a:7e:da:de:c6:ba:0a:26:0a:0c:92:68:b5:07:
                    41:be:c2:2c:0e:c8:1a:04:c9:59:2a:dc:d8:f0:77:
                    36:95:0d:b9:fb:67:35:b8:51:ee:e1:78:f2:63:9c:
                    1f:59:e7:41:10:fa:5d:69:04:31:c4:44:cc:e0:d1:
                    67:dd:0a:26:53:d2:f2:0d:15:bb:11:99:5c:52:2e:
                    3a:a6:8b:35:01:18:c4:07:4a:b3:cc:0b:03:52:66:
                    99:79:5c:8f:ab:d5:c4:02:b4:1b:e9:02:bd:96:0a:
                    3c:bd:3d:a7:71:8e:5a:66:5e:45:4d:b3:2b:62:be:
                    bf:2c:c3:a0:7b:00:2b:8e:70:d2:80:5b:bf:a7:93:
                    22:f2:5b:8c:42:f2:fa:24:17:c2:f4:cb:d5:ba:29:
                    a9:f4:10:cc:ed:d8:a5:aa:85:e4:19:bf:81:58:61:
                    d2:1f:2f:e3:e2:ec:19:c6:b5:ff:b5:14:79:20:e8:
                    79:6f:20:dd:35:fb:10:94:f7:98:cc:91:f6:e3:d3:
                    99:17:8c:eb:17:74:13:c9:4d:ab:7e:45:0f:aa:af:
                    09:ce:e2:5e:8a:09:20:14:e9:7d:39:f9:e5:2d:56:
                    0f:53:4f:1d:d6:41:4f:26:70:d2:7b:5d:53:cd:68:
                    94:45:cf:5b:b0:6b:f2:37:a2:46:27:0c:ac:65:01:
                    c9:46:4d:b8:30:a8:ea:14:31:9d:1f:00:aa:b6:95:
                    e2:57:0e:10:21:8e:19:da:0d:5d:22:fd:cb:13:fd:
                    03:63:98:e4:d7:4a:fc:2e:89:37:d7:0f:0f:e1:5a:
                    11:ba:3b:5c:08:41:75:38:e9:c5:cd:d6:60:f5:8e:
                    5d:04:df
                Exponent: 65537 (0x10001)
    Signature Algorithm: sha256WithRSAEncryption
         95:ba:fb:65:3c:af:d3:f7:4b:1f:e8:22:e7:4c:b1:54:77:e9:
         89:d4:9a:b5:87:e9:ed:e6:ec:86:e4:ae:25:b8:df:34:37:71:
         07:68:8e:ae:56:9d:f0:a7:6f:38:5f:6f:51:72:22:0c:c5:03:
         5a:48:29:e6:e5:35:79:bc:d2:3d:b3:6f:e3:5a:f9:bc:8d:34:
         a8:ec:d3:5c:f9:1d:5a:de:9a:79:d1:b0:11:82:14:86:52:ff:
         f7:24:35:34:7f:c4:99:d3:a8:3f:96:60:b0:52:f1:ce:4c:25:
         c3:b9:34:8f:62:47:7d:29:19:3b:0e:08:f5:ff:40:fd:9f:85:
         7a:a2:73:16:51:97:f3:87:12:f5:e8:26:51:f8:31:6b:29:5f:
         c3:1a:6a:bc:ce:9c:83:6f:e5:f2:59:75:c3:8f:79:56:82:21:
         42:8d:be:cb:a6:9b:72:b5:95:e8:1c:e0:83:75:9b:d7:0c:4b:
         b0:a1:a8:ea:69:c0:7a:a6:b3:6b:73:24:61:22:13:ca:cb:8c:
         40:ab:4a:d6:93:e8:8b:1d:08:11:1c:0d:02:85:12:80:83:bd:
         3d:fb:53:c5:ec:f5:01:b7:1e:31:95:0f:7c:71:a6:0d:d0:94:
         58:95:ca:7a:54:20:94:47:19:b5:36:00:ab:92:97:74:60:13:
         13:73:e5:b6:c6:43:cb:13:38:df:fb:19:41:6a:93:64:b7:31:
         4c:4e:4a:ed:22:ab:1e:da:cf:65:16:a5:ac:21:42:b5:41:51:
         f5:a5:ba:fd:62:b4:fd:9a:7b:93:60:20:0e:00:3a:d0:94:8e:
         06:24:df:a2:a7:58:9a:c8:b1:8f:8c:ae:53:ee:5b:ec:30:e8:
         80:d0:7f:17:03:33:a3:22:eb:e6:2c:80:d4:ac:f5:93:77:f5:
         58:11:3d:53:39:21:1b:ac:f4:51:1e:9e:5d:33:07:d1:55:9f:
         96:74:ae:8c:b2:8c:bd:b0:ea:f5:fc:11:58:14:f3:0a:06:70:
         94:55:4c:55:e0:35:a4:7c:5b:a8:db:7d:3f:c7:e7:51:07:25:
         1b:d4:23:15:e8:ad:e2:a9:24:da:0e:45:14:2d:61:55:80:71:
         be:7c:13:09:f5:21:3b:b3:ca:1a:98:cc:16:9b:b5:ed:eb:f4:
         e8:3b:77:57:98:98:f6:af:c3:a1:18:5e:29:a9:17:a0:61:35:
         fe:e2:dc:25:2d:a1:f2:c7:11:bc:77:32:48:d7:80:86:c7:e3:
         75:32:fb:2d:81:38:5c:c2:87:e4:fa:4d:5c:19:85:e0:20:b8:
         c3:76:59:fb:22:85:50:1e:87:22:92:75:60:44:96:05:f1:5c:
         b5:ad:62:56:bf:47:9f:be
```

Next Step is to create certificate for our server.
First we need a private key (server.key) to encrypt the traffic, in this case I don't use des3, so the key it's not encrypted with a passphrase, this is not recommended but it's just for a quick test: 
[igor@splunk01 ca]$ openssl genrsa -out server.key 4096
Generating RSA private key, 4096 bit long modulus
.....................................................................................................++
............................................................................................................++
e is 65537 (0x10001)
Then we create a CSR server.csr:
[igor@splunk01 ca]$ openssl req -sha256 -new -key server.key -out server.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CA
State or Province Name (full name) []:Ontario
Locality Name (eg, city) [Default City]:Toronto
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:splunk01
Email Address []:
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
We review our request with OpenSSL

[igor@splunk01 ca]$ openssl req -in server.csr -text

Certificate Request:

    Data:

        Version: 0 (0x0)

        Subject: C=CA, ST=Ontario, L=Toronto, O=Default Company Ltd, CN=splunk01

        Subject Public Key Info:

            Public Key Algorithm: rsaEncryption

                Public-Key: (4096 bit)

                Modulus:


```text
                    00:bc:b3:4d:9b:02:0a:7c:2e:81:27:e9:7e:f3:a8:

                    65:d0:33:35:fe:9f:18:47:88:d1:3b:0e:6d:91:79:

                    92:1a:b0:37:21:c3:cf:3a:24:07:4e:f2:da:5f:1d:

                    f1:5b:bc:50:3b:70:0b:88:92:54:34:7f:f5:bf:66:

                    35:dd:47:a2:7f:60:39:11:a6:8c:1f:bd:eb:0e:e2:

                    c1:b3:8b:c9:f2:aa:2d:51:d8:79:58:f3:92:05:80:

                    0a:eb:27:f7:14:ce:e7:35:8d:63:22:39:fa:ed:da:

                    4e:1f:40:0e:a4:38:82:0c:6a:d4:e6:7b:f0:09:88:

                    9f:d4:0e:73:22:1d:b2:0e:fe:8b:3d:bd:64:b5:7d:

                    52:b0:bd:e5:2c:a8:2b:ea:e4:20:f4:cf:f2:9d:dc:

                    e6:4d:22:6e:1f:dd:74:42:73:89:68:5e:ef:85:69:

                    dd:61:10:1d:6c:b8:dc:c6:f8:e2:df:ef:38:80:e3:

                    26:2c:4b:5d:72:c3:73:54:8b:75:e8:72:f9:10:d7:

                    1c:f9:d0:f9:ff:84:76:70:3c:2e:68:3e:e9:63:50:

                    8a:f9:62:53:63:a8:8d:9e:06:28:63:92:87:e8:81:

                    c5:b9:4d:3a:43:74:f7:07:3d:da:83:ef:4f:b9:70:

                    f5:c2:73:8d:b2:d2:36:27:3c:5c:10:e1:6e:f3:ea:

                    7a:d5:92:29:f2:ef:57:14:e1:3e:9b:98:be:fd:cc:

                    10:5d:f9:1e:d2:25:21:c2:b8:7e:a3:72:2b:12:5c:

                    64:1f:6a:ec:f1:a1:c7:2d:2b:36:8a:5d:1d:00:75:

                    20:95:92:36:5a:8f:0b:d8:de:68:bf:03:1a:3e:a0:

                    6b:c2:39:98:27:1a:55:d6:02:d9:32:3a:7d:5f:d0:

                    ae:2b:6c:73:18:d5:96:10:df:2e:b4:09:7e:f1:a3:

                    3a:fc:1a:c2:4e:d9:d5:eb:d1:9a:d5:9e:58:6c:1e:

                    e1:13:07:b6:88:88:d0:bd:9a:80:15:1b:88:38:7a:

                    cf:0d:7e:f2:f4:1c:84:77:19:3c:e9:39:a8:02:36:

                    f2:92:9a:b2:0a:ac:04:95:cb:79:41:22:24:6d:a3:

                    f2:a9:57:3d:96:fc:51:94:02:46:d6:b8:60:9a:d6:

                    b9:0d:25:7b:04:0a:4f:9d:f8:39:c1:2e:ca:24:db:

                    0a:f7:d9:64:6d:0c:93:5d:df:d7:b7:24:b1:86:52:

                    37:e2:2d:a9:84:f8:23:d2:ea:b3:0e:f6:e0:ff:c5:

                    31:3f:3e:56:c2:63:30:5d:d1:6f:7d:be:43:d5:46:

                    af:02:bf:cb:4d:e8:e1:7f:d1:c5:cf:27:51:ca:f0:

                    8a:17:a0:0d:22:cc:e2:2a:b3:0e:ec:b5:65:9b:38:

                    28:62:6f

                Exponent: 65537 (0x10001)

        Attributes:

            a0:00

    Signature Algorithm: sha256WithRSAEncryption

         29:13:65:c8:37:d7:7d:64:fa:1d:e6:02:a7:4d:09:ca:0e:e4:

         7f:82:e0:fc:75:cc:d8:d0:32:cc:b7:ed:c7:03:75:35:cc:58:

         e1:0b:f8:ee:29:25:e3:68:6c:3e:22:f1:be:87:a4:91:3e:45:

         58:f4:bd:cc:1d:0a:17:f8:94:1b:1e:8f:1f:88:50:64:da:02:

         92:75:3e:f1:64:86:37:8c:29:2f:08:64:56:bf:c7:a2:fc:46:

         c9:74:69:7a:af:f3:9c:0c:3d:37:15:07:0d:12:23:50:3f:06:

         f2:1d:97:40:da:fc:6e:db:7f:7d:90:9b:db:a3:ba:03:fa:4e:

         c0:a2:eb:84:00:c5:40:da:a6:ba:03:c4:9b:75:55:a3:df:8c:

         27:a7:18:2e:1b:da:af:8a:00:53:1a:25:fc:f1:50:1f:6b:fc:

         15:97:3d:7f:0c:04:12:6b:87:cc:7e:d3:ec:b6:0a:bf:12:1d:

         a9:68:71:4d:2d:f0:ee:27:0a:4f:d5:7a:00:50:82:f8:41:48:

         b9:48:6f:2e:af:5b:58:bc:a4:3d:93:47:8f:76:c6:80:38:1b:

         bb:56:52:91:79:2b:85:7a:f5:ea:82:db:4d:ac:07:f3:b2:6a:

         c6:36:e2:85:e2:b7:6c:fe:af:45:9a:7d:a0:1b:cb:a3:34:67:

         92:57:93:35:e8:61:b4:ea:27:55:72:c2:60:39:28:36:56:6b:

         fc:79:a8:b8:3f:ff:26:75:ef:f6:09:8c:28:59:a8:06:5b:d1:

         da:b4:7f:6c:14:e5:33:e4:c2:70:53:da:3f:0a:9f:df:63:c8:

         b5:29:27:a8:da:cb:45:53:9a:e0:00:49:3d:df:1c:a9:d4:03:

         03:4f:da:d2:f5:74:e0:44:4f:e0:e0:33:0b:10:d9:2b:fd:30:

         b0:d1:bf:d1:41:a5:fa:88:2a:79:17:e3:aa:b0:2e:49:af:8a:

         30:47:79:6b:98:44:c5:b9:1e:8a:af:6f:c5:94:33:b9:fd:c0:

         40:37:61:64:24:7c:a2:ea:b2:86:fc:e2:d4:26:10:9b:79:cb:

         d4:f6:34:e6:e7:9f:6d:fe:a7:28:03:35:4b:3b:1c:d1:cd:41:

         53:db:5c:69:8d:6b:1d:43:72:50:d6:e0:53:05:ea:c5:0d:44:

         ef:dd:91:d4:41:4e:80:75:3f:49:49:0b:f9:4b:fb:ea:27:36:

         19:38:1b:87:68:fd:e3:2a:9d:15:dd:44:5a:d1:e1:bd:4a:f3:

         be:30:46:09:3a:0f:4d:6c:ce:2c:84:80:bd:03:ff:5a:17:5f:

         f7:ab:fd:48:96:9b:0b:67:e4:d9:9d:d9:76:d3:42:b8:ed:07:

         bb:24:20:c8:86:be:b2:d8

-----BEGIN CERTIFICATE REQUEST-----
```


MIIEpzCCAo8CAQAwYjELMAkGA1UEBhMCQ0ExEDAOBgNVBAgMB09udGFyaW8xEDAO

BgNVBAcMB1Rvcm9udG8xHDAaBgNVBAoME0RlZmF1bHQgQ29tcGFueSBMdGQxETAP

BgNVBAMMCHNwbHVuazAxMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA

vLNNmwIKfC6BJ+l+86hl0DM1/p8YR4jROw5tkXmSGrA3IcPPOiQHTvLaXx3xW7xQ

O3ALiJJUNH/1v2Y13Ueif2A5EaaMH73rDuLBs4vJ8qotUdh5WPOSBYAK6yf3FM7n

NY1jIjn67dpOH0AOpDiCDGrU5nvwCYif1A5zIh2yDv6LPb1ktX1SsL3lLKgr6uQg

9M/yndzmTSJuH910QnOJaF7vhWndYRAdbLjcxvji3+84gOMmLEtdcsNzVIt16HL5

ENcc+dD5/4R2cDwuaD7pY1CK+WJTY6iNngYoY5KH6IHFuU06Q3T3Bz3ag+9PuXD1

wnONstI2JzxcEOFu8+p61ZIp8u9XFOE+m5i+/cwQXfke0iUhwrh+o3IrElxkH2rs

8aHHLSs2il0dAHUglZI2Wo8L2N5ovwMaPqBrwjmYJxpV1gLZMjp9X9CuK2xzGNWW

EN8utAl+8aM6/BrCTtnV69Ga1Z5YbB7hEwe2iIjQvZqAFRuIOHrPDX7y9ByEdxk8

6TmoAjbykpqyCqwElct5QSIkbaPyqVc9lvxRlAJG1rhgmta5DSV7BApPnfg5wS7K

JNsK99lkbQyTXd/XtySxhlI34i2phPgj0uqzDvbg/8UxPz5WwmMwXdFvfb5D1Uav

Ar/LTejhf9HFzydRyvCKF6ANIsziKrMO7LVlmzgoYm8CAwEAAaAAMA0GCSqGSIb3

DQEBCwUAA4ICAQApE2XIN9d9ZPod5gKnTQnKDuR/guD8dczY0DLMt+3HA3U1zFjh

C/juKSXjaGw+IvG+h6SRPkVY9L3MHQoX+JQbHo8fiFBk2gKSdT7xZIY3jCkvCGRW

v8ei/EbJdGl6r/OcDD03FQcNEiNQPwbyHZdA2vxu2399kJvbo7oD+k7AouuEAMVA

2qa6A8SbdVWj34wnpxguG9qvigBTGiX88VAfa/wVlz1/DAQSa4fMftPstgq/Eh2p

aHFNLfDuJwpP1XoAUIL4QUi5SG8ur1tYvKQ9k0ePdsaAOBu7VlKReSuFevXqgttN

rAfzsmrGNuKF4rds/q9Fmn2gG8ujNGeSV5M16GG06idVcsJgOSg2Vmv8eai4P/8m

de/2CYwoWagGW9HatH9sFOUz5MJwU9o/Cp/fY8i1KSeo2stFU5rgAEk93xyp1AMD

T9rS9XTgRE/g4DMLENkr/TCw0b/RQaX6iCp5F+OqsC5Jr4owR3lrmETFuR6Kr2/F

lDO5/cBAN2FkJHyi6rKG/OLUJhCbecvU9jTm559t/qcoAzVLOxzRzUFT21xpjWsd

Q3JQ1uBTBerFDUTv3ZHUQU6AdT9JSQv5S/vqJzYZOBuHaP3jKp0V3URa0eG9SvO+

MEYJOg9NbM4shIC9A/9aF1/3q/1IlpsLZ+TZndl200K47Qe7JCDIhr6y2A==

-----END CERTIFICATE REQUEST-----

And we submit the request to the CA to create our new server.crt:

[igor@splunk01 ca]$ openssl x509 -req -sha256 -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 02 -out server.crt
Signature ok
subject=/C=CA/ST=Ontario/L=Toronto/O=Default Company Ltd/CN=splunk01
Getting CA Private Key
Enter pass phrase for ca.key:

And we take a look at certificate to make sure it's what we asked for:

[igor@splunk01 ca]$ openssl x509 -in server.crt -text


```text
Certificate:

    Data:

        Version: 1 (0x0)

        Serial Number: 2 (0x2)

    Signature Algorithm: sha256WithRSAEncryption

        Issuer: C=CA, ST=Ontario, L=Toronto, CN=gu4r15m0

        Validity

            Not Before: Feb 13 02:10:40 2017 GMT

            Not After : Feb 13 02:10:40 2018 GMT

        Subject: C=CA, ST=Ontario, L=Toronto, O=Default Company Ltd, CN=splunk01

        Subject Public Key Info:

            Public Key Algorithm: rsaEncryption

                Public-Key: (4096 bit)

                Modulus:

                    00:bc:b3:4d:9b:02:0a:7c:2e:81:27:e9:7e:f3:a8:

                    65:d0:33:35:fe:9f:18:47:88:d1:3b:0e:6d:91:79:

                    92:1a:b0:37:21:c3:cf:3a:24:07:4e:f2:da:5f:1d:

                    f1:5b:bc:50:3b:70:0b:88:92:54:34:7f:f5:bf:66:

                    35:dd:47:a2:7f:60:39:11:a6:8c:1f:bd:eb:0e:e2:

                    c1:b3:8b:c9:f2:aa:2d:51:d8:79:58:f3:92:05:80:

                    0a:eb:27:f7:14:ce:e7:35:8d:63:22:39:fa:ed:da:

                    4e:1f:40:0e:a4:38:82:0c:6a:d4:e6:7b:f0:09:88:

                    9f:d4:0e:73:22:1d:b2:0e:fe:8b:3d:bd:64:b5:7d:

                    52:b0:bd:e5:2c:a8:2b:ea:e4:20:f4:cf:f2:9d:dc:

                    e6:4d:22:6e:1f:dd:74:42:73:89:68:5e:ef:85:69:

                    dd:61:10:1d:6c:b8:dc:c6:f8:e2:df:ef:38:80:e3:

                    26:2c:4b:5d:72:c3:73:54:8b:75:e8:72:f9:10:d7:

                    1c:f9:d0:f9:ff:84:76:70:3c:2e:68:3e:e9:63:50:

                    8a:f9:62:53:63:a8:8d:9e:06:28:63:92:87:e8:81:

                    c5:b9:4d:3a:43:74:f7:07:3d:da:83:ef:4f:b9:70:

                    f5:c2:73:8d:b2:d2:36:27:3c:5c:10:e1:6e:f3:ea:

                    7a:d5:92:29:f2:ef:57:14:e1:3e:9b:98:be:fd:cc:

                    10:5d:f9:1e:d2:25:21:c2:b8:7e:a3:72:2b:12:5c:

                    64:1f:6a:ec:f1:a1:c7:2d:2b:36:8a:5d:1d:00:75:

                    20:95:92:36:5a:8f:0b:d8:de:68:bf:03:1a:3e:a0:

                    6b:c2:39:98:27:1a:55:d6:02:d9:32:3a:7d:5f:d0:

                    ae:2b:6c:73:18:d5:96:10:df:2e:b4:09:7e:f1:a3:

                    3a:fc:1a:c2:4e:d9:d5:eb:d1:9a:d5:9e:58:6c:1e:

                    e1:13:07:b6:88:88:d0:bd:9a:80:15:1b:88:38:7a:

                    cf:0d:7e:f2:f4:1c:84:77:19:3c:e9:39:a8:02:36:

                    f2:92:9a:b2:0a:ac:04:95:cb:79:41:22:24:6d:a3:

                    f2:a9:57:3d:96:fc:51:94:02:46:d6:b8:60:9a:d6:

                    b9:0d:25:7b:04:0a:4f:9d:f8:39:c1:2e:ca:24:db:

                    0a:f7:d9:64:6d:0c:93:5d:df:d7:b7:24:b1:86:52:

                    37:e2:2d:a9:84:f8:23:d2:ea:b3:0e:f6:e0:ff:c5:

                    31:3f:3e:56:c2:63:30:5d:d1:6f:7d:be:43:d5:46:

                    af:02:bf:cb:4d:e8:e1:7f:d1:c5:cf:27:51:ca:f0:

                    8a:17:a0:0d:22:cc:e2:2a:b3:0e:ec:b5:65:9b:38:

                    28:62:6f

                Exponent: 65537 (0x10001)

    Signature Algorithm: sha256WithRSAEncryption

         98:b9:19:ee:c2:a7:08:b9:66:27:ba:3f:8a:13:87:5a:5c:d2:

         18:b1:91:3b:21:9c:5f:43:f2:94:41:dc:b5:59:bc:b2:e9:78:

         fa:bc:61:1e:17:43:eb:94:dd:79:a9:b8:a0:3c:1a:48:fd:d7:

         a0:10:e4:97:9d:62:9a:04:26:5b:b1:49:45:4e:b9:27:9e:76:

         c7:20:7a:5d:54:45:49:2d:3a:b5:b2:e4:0b:49:9f:32:31:07:

         1e:5e:54:cd:07:af:65:28:47:82:f2:07:80:56:11:47:aa:7a:

         4d:1b:26:19:20:4b:df:0d:36:a2:af:72:d5:bd:db:29:37:28:

         58:49:cd:26:a9:88:76:ed:51:60:80:d1:15:a1:d8:8b:a2:f3:

         af:a6:e5:f8:68:79:b2:d6:a9:5d:fc:76:03:e7:47:e5:66:5a:

         62:7e:5f:7e:47:8c:bb:2d:87:48:3d:fc:8d:49:b7:50:2d:2c:

         b6:22:ec:08:40:4c:5a:7f:c2:cd:cb:ff:ea:7b:f1:69:46:1e:

         00:18:1b:1f:1d:7d:16:14:af:f4:30:d8:5f:a7:3b:b8:d3:d7:

         29:37:4a:3d:67:e0:de:eb:48:0f:f7:49:be:b4:44:68:5d:1c:

         a6:40:e2:11:2f:a3:15:51:23:9c:f5:3f:58:ea:e0:3d:42:80:

         16:d7:5c:ef:0d:75:9f:16:db:0b:e9:dc:0c:c2:f3:81:f4:d6:

         d9:53:c7:ed:83:2c:2d:af:f2:09:0a:73:eb:d4:2d:4a:46:0f:

         2d:31:36:60:72:6d:62:5a:0f:cf:67:a0:71:14:e3:a1:b6:28:

         20:6b:29:cf:76:bc:1c:eb:87:1d:f3:db:a2:cf:74:fd:cc:84:

         53:f5:10:7c:65:b9:7b:3c:b3:c0:d2:87:a8:7d:65:71:bc:b7:

         f5:f8:e1:e2:f4:39:3a:06:ae:77:e0:f8:79:88:ef:18:66:a9:

         3d:5e:2c:1a:03:8e:35:3e:d4:b1:71:4f:2e:35:6f:99:e6:3b:

         64:0b:bf:f1:86:a2:15:6a:e8:3f:5e:41:4c:0f:44:a0:d9:83:

         87:05:ce:35:6c:39:95:fc:e1:59:39:e0:a6:3b:7a:ed:1b:34:

         e5:d4:f0:ca:82:31:7b:1c:1f:b2:b6:1d:bf:7a:8a:69:c5:60:

         4a:fe:0b:b2:62:12:76:28:dc:df:5b:1d:e0:a6:7d:54:87:5c:

         65:8b:50:cc:a4:2a:7c:cc:a8:a2:5a:e6:79:39:76:31:8c:32:

         14:ce:cb:8d:6e:02:60:3e:1f:ba:ef:24:c4:9d:7f:6c:f3:38:

         bb:cd:b8:a2:3e:e3:87:bd:62:77:18:14:da:27:ba:dd:be:7c:

         6e:5a:af:14:f8:a6:81:d5

-----BEGIN CERTIFICATE-----
```


MIIFGjCCAwICAQIwDQYJKoZIhvcNAQELBQAwRDELMAkGA1UEBhMCQ0ExEDAOBgNV

BAgMB09udGFyaW8xEDAOBgNVBAcMB1Rvcm9udG8xETAPBgNVBAMMCGd1NHIxNW0w

MB4XDTE3MDIxMzAyMTA0MFoXDTE4MDIxMzAyMTA0MFowYjELMAkGA1UEBhMCQ0Ex

EDAOBgNVBAgMB09udGFyaW8xEDAOBgNVBAcMB1Rvcm9udG8xHDAaBgNVBAoME0Rl

ZmF1bHQgQ29tcGFueSBMdGQxETAPBgNVBAMMCHNwbHVuazAxMIICIjANBgkqhkiG

9w0BAQEFAAOCAg8AMIICCgKCAgEAvLNNmwIKfC6BJ+l+86hl0DM1/p8YR4jROw5t

kXmSGrA3IcPPOiQHTvLaXx3xW7xQO3ALiJJUNH/1v2Y13Ueif2A5EaaMH73rDuLB

s4vJ8qotUdh5WPOSBYAK6yf3FM7nNY1jIjn67dpOH0AOpDiCDGrU5nvwCYif1A5z

Ih2yDv6LPb1ktX1SsL3lLKgr6uQg9M/yndzmTSJuH910QnOJaF7vhWndYRAdbLjc

xvji3+84gOMmLEtdcsNzVIt16HL5ENcc+dD5/4R2cDwuaD7pY1CK+WJTY6iNngYo

Y5KH6IHFuU06Q3T3Bz3ag+9PuXD1wnONstI2JzxcEOFu8+p61ZIp8u9XFOE+m5i+

/cwQXfke0iUhwrh+o3IrElxkH2rs8aHHLSs2il0dAHUglZI2Wo8L2N5ovwMaPqBr

wjmYJxpV1gLZMjp9X9CuK2xzGNWWEN8utAl+8aM6/BrCTtnV69Ga1Z5YbB7hEwe2

iIjQvZqAFRuIOHrPDX7y9ByEdxk86TmoAjbykpqyCqwElct5QSIkbaPyqVc9lvxR

lAJG1rhgmta5DSV7BApPnfg5wS7KJNsK99lkbQyTXd/XtySxhlI34i2phPgj0uqz

Dvbg/8UxPz5WwmMwXdFvfb5D1UavAr/LTejhf9HFzydRyvCKF6ANIsziKrMO7LVl

mzgoYm8CAwEAATANBgkqhkiG9w0BAQsFAAOCAgEAmLkZ7sKnCLlmJ7o/ihOHWlzS

GLGROyGcX0PylEHctVm8sul4+rxhHhdD65Tdeam4oDwaSP3XoBDkl51imgQmW7FJ

RU65J552xyB6XVRFSS06tbLkC0mfMjEHHl5UzQevZShHgvIHgFYRR6p6TRsmGSBL

3w02oq9y1b3bKTcoWEnNJqmIdu1RYIDRFaHYi6Lzr6bl+Gh5stapXfx2A+dH5WZa

Yn5ffkeMuy2HSD38jUm3UC0stiLsCEBMWn/Czcv/6nvxaUYeABgbHx19FhSv9DDY

X6c7uNPXKTdKPWfg3utID/dJvrREaF0cpkDiES+jFVEjnPU/WOrgPUKAFtdc7w11

nxbbC+ncDMLzgfTW2VPH7YMsLa/yCQpz69QtSkYPLTE2YHJtYloPz2egcRTjobYo

IGspz3a8HOuHHfPbos90/cyEU/UQfGW5ezyzwNKHqH1lcby39fjh4vQ5Ogaud+D4

eYjvGGapPV4sGgOONT7UsXFPLjVvmeY7ZAu/8YaiFWroP15BTA9EoNmDhwXONWw5

lfzhWTngpjt67Rs05dTwyoIxexwfsrYdv3qKacVgSv4LsmISdijc31sd4KZ9VIdc

ZYtQzKQqfMyoolrmeTl2MYwyFM7LjW4CYD4fuu8kxJ1/bPM4u824oj7jh71idxgU

2ie63b58blqvFPimgdU=

-----END CERTIFICATE-----

Now we copy our CA certificate and  server certificate and key to the /etc/pki/nginx folder and edit our /etc/nginx/nginx.conf to include a SSL configuration to use our certificate and trust our CA

...

\# Settings for a TLS enabled server.

#

    server {

        listen       443 ssl http2 default_server;

        listen       [::]:443 ssl http2 default_server;

        server_name  \_;

        root         /usr/share/nginx/html;

        ssl_certificate "**/etc/pki/nginx/server.crt**";

        ssl_certificate_key "**/etc/pki/nginx/private/server.key**";

        ssl_session_cache shared:SSL:1m;

        ssl_session_timeout  10m;

        ssl_ciphers HIGH:!aNULL:!MD5;

        ssl_prefer_server_ciphers on;

        # Client side cert

        ssl_client_certificate **/etc/pki/nginx/CA/ca.crt**;

        ssl_verify_client optional; # or \`on\` if you require client key

#

\#        # Load configuration files for the default server block.

        include /etc/nginx/default.d/\*.conf;

#

        location / {

        }

        error_page 404 /404.html;

            location = /40x.html {

        }

        error_page 500 502 503 504 /50x.html;

            location = /50x.html {

        }

    }

}

We restart nginx with systemctl:

[igor@splunk01 ca]$ sudo systemctl restart nginx

And with a curl command we make sure this is all working, notice I have to type the passphrase for the private key next to the certificate file (\--cert ./client.crt:**hello**)

[igor@splunk01 ca]$ curl -kv --cert-type pem --cert ./client.crt:hello --key ./client.key https://localhost

\* About to connect() to localhost port 443 (#0)

\*   Trying ::1...

\* Connected to localhost (::1) port 443 (#0)

\* Initializing NSS with certpath: sql:/etc/pki/nssdb

\* skipping SSL peer certificate verification

\* **NSS: client certificate from file**

\*       subject: CN=gu4r15m0,O=Default Company Ltd,L=Toronto,ST=Ontario,C=CA

\*       start date: Feb 13 01:08:45 2017 GMT

\*       expire date: Feb 13 01:08:45 2018 GMT

\*       common name: gu4r15m0

\*       issuer: CN=gu4r15m0,L=Toronto,ST=Ontario,C=CA

\* SSL connection using TLS_DHE_RSA_WITH_AES_256_CBC_SHA

\* **Server certificate:**

\*       subject: CN=splunk01,O=Default Company Ltd,L=Toronto,ST=Ontario,C=CA

\*       start date: Feb 13 02:10:40 2017 GMT

\*       expire date: Feb 13 02:10:40 2018 GMT

\*       common name: splunk01

\*       issuer: CN=gu4r15m0,L=Toronto,ST=Ontario,C=CA

\> GET / HTTP/1.1

\> User-Agent: curl/7.29.0

\> Host: localhost

\> Accept: \*/\*

\>

< HTTP/1.1 200 OK

< Server: nginx/1.10.2

< Date: Mon, 13 Feb 2017 02:49:08 GMT

< Content-Type: text/html

< Content-Length: 3700

< Last-Modified: Mon, 31 Oct 2016 12:37:02 GMT

< Connection: keep-alive

< ETag: "58173aee-e74"

< Accept-Ranges: bytes

<

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

Now for the python script, I'm going to use the [requests module](http://docs.python-requests.org/en/master/user/quickstart/) which is installed by default the Splunk's Python interpreter.

I'll install the CentOS one for now:

[igor@splunk01 ca]$ sudo yum install python-requests.noarch

Loaded plugins: fastestmirror, langpacks

Loading mirror speeds from cached hostfile

 \* base: mirror2.evolution-host.com

 \* epel: mirror.symnds.com

 \* extras: mirror2.evolution-host.com

 \* updates: mirror2.evolution-host.com

Resolving Dependencies

\--> Running transaction check

\---> Package python-requests.noarch 0:2.6.0-1.el7_1 will be installed

\--> Processing Dependency: python-urllib3 >= 1.10.2-1 for package: python-requests-2.6.0-1.el7_1.noarch

\--> Running transaction check

\---> Package python-urllib3.noarch 0:1.10.2-2.el7_1 will be installed

\--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================================================================================

 Package                                  Arch                            Version                                  Repository                     Size

=======================================================================================================================================================

Installing:

 python-requests                          noarch                          2.6.0-1.el7_1                            base                           94 k

Installing for dependencies:

 python-urllib3                           noarch                          1.10.2-2.el7_1                           base                          100 k

Transaction Summary

=======================================================================================================================================================

Install  1 Package (+1 Dependent package)

Total size: 193 k

Installed size: 711 k

Is this ok [y/d/N]: y

Downloading packages:

Running transaction check

Running transaction test

Transaction test succeeded

Running transaction

  Installing : python-urllib3-1.10.2-2.el7_1.noarch                                                                                                1/2

  Installing : python-requests-2.6.0-1.el7_1.noarch                                                                                                2/2

  Verifying  : python-requests-2.6.0-1.el7_1.noarch                                                                                                1/2

  Verifying  : python-urllib3-1.10.2-2.el7_1.noarch                                                                                                2/2

Installed:

  python-requests.noarch 0:2.6.0-1.el7_1

Dependency Installed:

  python-urllib3.noarch 0:1.10.2-2.el7_1

Complete!

OK, now we can use the requests module:

[igor@splunk01 ca]$ python

Python 2.7.5 (default, Jun 24 2015, 00:41:19)

[GCC 4.8.3 20140911 (Red Hat 4.8.3-9)] on linux2

Type "help", "copyright", "credits" or "license" for more information.

First we import the module so we can use it.

\>>> import requests

Now we create a response (res) object to capture the results of our POST HTTP Request using the my client certificate and key (cert=('./client.pem','./client.key'))to authenticate, also using the Root CA certificate to validate the server certificate (verify='./ca.crt').

\>>> res = requests.post('https://localhost', cert='./client.pem', verify='./ca.crt')

Enter PEM pass phrase: **<-- It asks me for my private key pass phrase (hello)**

And I get an error, because the Common Name in the server certificate (splunk01) doesn't match the hostname I used in the URL (localhost):

/usr/lib/python2.7/site-packages/urllib3/connection.py:251: SecurityWarning: Certificate has no \`subjectAltName\`, falling back to check for a \`commonName\` for now. This feature is being removed by major browsers and deprecated by RFC 2818. (See https://github.com/shazow/urllib3/issues/497 for details.)

  SecurityWarning

Traceback (most recent call last):

  File "<stdin>", line 1, in <module>

  File "/usr/lib/python2.7/site-packages/requests/api.py", line 108, in post

    return request('post', url, data=data, json=json, \*\*kwargs)

  File "/usr/lib/python2.7/site-packages/requests/api.py", line 50, in request

    response = session.request(method=method, url=url, \*\*kwargs)

  File "/usr/lib/python2.7/site-packages/requests/sessions.py", line 464, in request

    resp = self.send(prep, \*\*send_kwargs)

  File "/usr/lib/python2.7/site-packages/requests/sessions.py", line 576, in send

    r = adapter.send(request, \*\*kwargs)

  File "/usr/lib/python2.7/site-packages/requests/adapters.py", line 431, in send

    raise SSLError(e, request=request)

requests.exceptions.SSLError: hostname 'localhost' doesn't match u'splunk01'

So we try again with the correct hostname:

\>>> res = requests.post('https://splunk01', cert='./client.pem', verify='./ca.crt')

Enter PEM pass phrase: **<-- It asks me for my private key pass phrase (hello)**

No errors, that means I've authenticated successfully using my client certificate and private key (client.pem) so now I have data in my response object 'res', let's take a look at the HTML Text that came back from the server

\>>> res.text

u'<html>\\r\\n<head><title>**405 Not Allowed**</title></head>\\r\\n<body bgcolor="white">\\r\\n<center><h1>**405 Not Allowed**</h1></center>\\r\\n<hr><center>nginx/1.10.2</center>\\r\\n</body>\\r\\n</html>\\r\\n'

I get nginx's 405 Not Allowed page back as expected, that's because it's not acception the POST method since I don't have any application running on nginx.

We can take a look at the header of the response

\>>> res.headers

{'date': 'Tue, 14 Feb 2017 01:23:03 GMT', 'content-length': '173', 'content-type': 'text/html', 'connection': 'keep-alive', 'server': 'nginx/1.10.2'}

Although I don't have any application to handle any data I send, I just want to show you you can POST any data by including the data parameter (data='<xml>stuff</xml>') in the requests.post and also add header fields with the headers (headers={'Content-Type': 'text/xml;charset=UTF-8'}) parameter.

\>>> res = requests.post('https://splunk01', cert='./client.pem', verify='./ca.crt', headers={'Content-Type': 'text/xml;charset=UTF-8'},data='<xml>stuff</xml>')

Enter PEM pass phrase:

\>>> res.headers

{'date': 'Tue, 14 Feb 2017 01:28:01 GMT', 'content-length': '173', 'content-type': 'text/html', 'connection': 'keep-alive', 'server': 'nginx/1.10.2'}

\>>> res.text

u'<html>\\r\\n<head><title>405 Not Allowed</title></head>\\r\\n<body bgcolor="white">\\r\\n<center><h1>405 Not Allowed</h1></center>\\r\\n<hr><center>nginx/1.10.2</center>\\r\\n</body>\\r\\n</html>\\r\\n'

I can read the status_code value from the response, and use it in a conditional clause for example

\>>> res.status_code

405

\>>> if res.status_code <> 200:

...    print "We didn't get an HTTP 200 :(, instead we got {}".format(res.status_code)

...

We didn't get an HTTP 200 :(, instead we got 405

I hope this was useful for you as it was for me. 🙂
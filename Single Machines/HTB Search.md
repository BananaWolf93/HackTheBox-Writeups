# HTB Search

Basic nmap scan:
```
Nmap scan report for 10.10.11.129
Host is up (0.058s latency).
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
443/tcp  open  https
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
8172/tcp  open  unknown
9389/tcp  open  adws
49667/tcp open  unknown
49675/tcp open  unknown
49676/tcp open  unknown
49700/tcp open  unknown
49710/tcp open  unknown
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 109.43 seconds
          Raw packets sent: 131128 (5.770MB) | Rcvd: 178 (7.832KB)
```

Aggressive nmap scan:
```
└─$ sudo nmap -sC -sV -p 53,80,88,135,139,389,443,445,464,593,636,3268,8172,9389,49666,49675,49676,49700,49710,49736 -T4 10.10.11.129
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-02 20:00 MDT
Nmap scan report for research.htb (10.10.11.129)
Host is up (0.14s latency).
PORT      STATE    SERVICE      VERSION
53/tcp    open    domain        Simple DNS Plus
80/tcp    open    http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Search &mdash; Just Testing IIS
88/tcp    open    kerberos-sec  Microsoft Windows Kerberos (server time: 2023-04-03 02:00:55Z)
135/tcp  open    msrpc        Microsoft Windows RPC
139/tcp  open    netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open    ldap          Microsoft Windows Active Directory LDAP (Domain: search.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=research
| Not valid before: 2020-08-11T08:13:35
|_Not valid after:  2030-08-09T08:13:35
|_ssl-date: 2023-04-03T02:02:25+00:00; 0s from scanner time.
443/tcp  open    ssl/http      Microsoft IIS httpd 10.0
|_http-title: Search &mdash; Just Testing IIS
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-IIS/10.0
|_ssl-date: 2023-04-03T02:02:24+00:00; -1s from scanner time.
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=research
| Not valid before: 2020-08-11T08:13:35
|_Not valid after:  2030-08-09T08:13:35
445/tcp  open    microsoft-ds?
464/tcp  open    kpasswd5?
593/tcp  open    ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open    ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: search.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=research
| Not valid before: 2020-08-11T08:13:35
|_Not valid after:  2030-08-09T08:13:35
|_ssl-date: 2023-04-03T02:02:25+00:00; 0s from scanner time.
3268/tcp  filtered globalcatLDAP
8172/tcp  open    ssl/http      Microsoft IIS httpd 10.0
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=WMSvc-SHA2-RESEARCH
| Not valid before: 2020-04-07T09:05:25
|_Not valid after:  2030-04-05T09:05:25
|_http-title: Site doesn't have a title.
|_ssl-date: 2023-04-03T02:02:25+00:00; 0s from scanner time.
|_http-server-header: Microsoft-IIS/10.0
9389/tcp  open    mc-nmf        .NET Message Framing
49666/tcp filtered unknown
49675/tcp open    ncacn_http    Microsoft Windows RPC over HTTP 1.0
49676/tcp open    msrpc        Microsoft Windows RPC
49700/tcp open    msrpc        Microsoft Windows RPC
49710/tcp open    msrpc        Microsoft Windows RPC
49736/tcp open    msrpc        Microsoft Windows RPC
Service Info: Host: RESEARCH; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-time: 
|  date: 2023-04-03T02:01:49
|_  start_date: N/A
| smb2-security-mode: 
|  311: 
|_    Message signing enabled and required
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 99.04 seconds
```

LDAP enumeration with nmap:
```
─$ sudo nmap -n -sV --script "ldap* and not brute" -p 389 10.10.11.129 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-02 18:07 MDT
Nmap scan report for 10.10.11.129
Host is up (0.082s latency).
PORT    STATE SERVICE VERSION
389/tcp open  ldap    Microsoft Windows Active Directory LDAP (Domain: search.htb, Site: Default-First-Site-Name)
| ldap-rootdse: 
| LDAP Results
|  <ROOT>
|      domainFunctionality: 7
|      forestFunctionality: 7
|      domainControllerFunctionality: 7
|      rootDomainNamingContext: DC=search,DC=htb
|      ldapServiceName: search.htb:research$@SEARCH.HTB
|      isGlobalCatalogReady: TRUE
|      supportedSASLMechanisms: GSSAPI
|      supportedSASLMechanisms: GSS-SPNEGO
|      supportedSASLMechanisms: EXTERNAL
|      supportedSASLMechanisms: DIGEST-MD5
|      supportedLDAPVersion: 3
|      supportedLDAPVersion: 2
|      supportedLDAPPolicies: MaxPoolThreads
|      supportedLDAPPolicies: MaxPercentDirSyncRequests
|      supportedLDAPPolicies: MaxDatagramRecv
|      supportedLDAPPolicies: MaxReceiveBuffer
|      supportedLDAPPolicies: InitRecvTimeout
|      supportedLDAPPolicies: MaxConnections
|      supportedLDAPPolicies: MaxConnIdleTime
|      supportedLDAPPolicies: MaxPageSize
|      supportedLDAPPolicies: MaxBatchReturnMessages
|      supportedLDAPPolicies: MaxQueryDuration
|      supportedLDAPPolicies: MaxDirSyncDuration
|      supportedLDAPPolicies: MaxTempTableSize
|      supportedLDAPPolicies: MaxResultSetSize
|      supportedLDAPPolicies: MinResultSets
|      supportedLDAPPolicies: MaxResultSetsPerConn
|      supportedLDAPPolicies: MaxNotificationPerConn
|      supportedLDAPPolicies: MaxValRange
|      supportedLDAPPolicies: MaxValRangeTransitive
|      supportedLDAPPolicies: ThreadMemoryLimit
|      supportedLDAPPolicies: SystemMemoryLimitPercent
|      supportedControl: 1.2.840.113556.1.4.319
|      supportedControl: 1.2.840.113556.1.4.801
|      supportedControl: 1.2.840.113556.1.4.473
|      supportedControl: 1.2.840.113556.1.4.528
|      supportedControl: 1.2.840.113556.1.4.417
|      supportedControl: 1.2.840.113556.1.4.619
|      supportedControl: 1.2.840.113556.1.4.841
|      supportedControl: 1.2.840.113556.1.4.529
|      supportedControl: 1.2.840.113556.1.4.805
|      supportedControl: 1.2.840.113556.1.4.521
|      supportedControl: 1.2.840.113556.1.4.970
|      supportedControl: 1.2.840.113556.1.4.1338
|      supportedControl: 1.2.840.113556.1.4.474
|      supportedControl: 1.2.840.113556.1.4.1339
|      supportedControl: 1.2.840.113556.1.4.1340
|      supportedControl: 1.2.840.113556.1.4.1413
|      supportedControl: 2.16.840.1.113730.3.4.9
|      supportedControl: 2.16.840.1.113730.3.4.10
|      supportedControl: 1.2.840.113556.1.4.1504
|      supportedControl: 1.2.840.113556.1.4.1852
|      supportedControl: 1.2.840.113556.1.4.802
|      supportedControl: 1.2.840.113556.1.4.1907
|      supportedControl: 1.2.840.113556.1.4.1948
|      supportedControl: 1.2.840.113556.1.4.1974
|      supportedControl: 1.2.840.113556.1.4.1341
|      supportedControl: 1.2.840.113556.1.4.2026
|      supportedControl: 1.2.840.113556.1.4.2064
|      supportedControl: 1.2.840.113556.1.4.2065
|      supportedControl: 1.2.840.113556.1.4.2066
|      supportedControl: 1.2.840.113556.1.4.2090
|      supportedControl: 1.2.840.113556.1.4.2205
|      supportedControl: 1.2.840.113556.1.4.2204
|      supportedControl: 1.2.840.113556.1.4.2206
|      supportedControl: 1.2.840.113556.1.4.2211
|      supportedControl: 1.2.840.113556.1.4.2239
|      supportedControl: 1.2.840.113556.1.4.2255
|      supportedControl: 1.2.840.113556.1.4.2256
|      supportedControl: 1.2.840.113556.1.4.2309
|      supportedControl: 1.2.840.113556.1.4.2330
|      supportedControl: 1.2.840.113556.1.4.2354
|      supportedCapabilities: 1.2.840.113556.1.4.800
|      supportedCapabilities: 1.2.840.113556.1.4.1670
|      supportedCapabilities: 1.2.840.113556.1.4.1791
|      supportedCapabilities: 1.2.840.113556.1.4.1935
|      supportedCapabilities: 1.2.840.113556.1.4.2080
|      supportedCapabilities: 1.2.840.113556.1.4.2237
|      subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=search,DC=htb
|      serverName: CN=RESEARCH,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=search,DC=htb
|      schemaNamingContext: CN=Schema,CN=Configuration,DC=search,DC=htb
|      namingContexts: DC=search,DC=htb
|      namingContexts: CN=Configuration,DC=search,DC=htb
|      namingContexts: CN=Schema,CN=Configuration,DC=search,DC=htb
|      namingContexts: DC=DomainDnsZones,DC=search,DC=htb
|      namingContexts: DC=ForestDnsZones,DC=search,DC=htb
|      isSynchronized: TRUE
|      highestCommittedUSN: 229489
|      dsServiceName: CN=NTDS Settings,CN=RESEARCH,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=search,DC=htb
|      dnsHostName: Research.search.htb
|      defaultNamingContext: DC=search,DC=htb
|      currentTime: 20230403000728.0Z
|_      configurationNamingContext: CN=Configuration,DC=search,DC=htb
Service Info: Host: RESEARCH; OS: Windows; CPE: cpe:/o:microsoft:windows
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.48 seconds
```

In checking the website on port 80, I went to the "Our Team" page and I  see a list of users.:

```
keely lyons
dax santiago
siera frye
kyla stewart
kaiara spencer
dave simpson
ben thompson
chris stewart
```

kerbrute not working for some reason despite it being exactly the same as Michael.

I found a weird webpage at <https://10.10.11.129/single.html> which is like  a dating site or something.

I learned that sometimes, SSL certs that show in aggressive nmap scans may also indicate another domain. For instance, the scan I did up top (the aggressive one did not work).

Nmap scan snip:
```
443/tcp  open    ssl/http      Microsoft IIS httpd 10.0
|_http-title: Search &mdash; Just Testing IIS
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-IIS/10.0
|_ssl-date: 2023-04-03T02:02:24+00:00; -1s from scanner time.
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=research
| Not valid before: 2020-08-11T08:13:35
|_Not valid after:  2030-08-09T08:13:35
```

IsolationIsKey

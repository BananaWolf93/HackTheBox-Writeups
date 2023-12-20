# HTB Friendzone

Nmap basic scan:
```
Completed SYN Stealth Scan at 21:25, 400.66s elapsed (65535 total ports)
Nmap scan report for 10.10.10.123
Host is up (0.15s latency).
Not shown: 65528 closed tcp ports (reset)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 400.91 seconds
          Raw packets sent: 70932 (3.121MB) | Rcvd: 72875 (2.981MB)
```
Nmap aggressive scan:
```
Nmap scan report for 10.10.10.123
Host is up (0.078s latency).
Not shown: 993 closed tcp ports (reset)
PORT    STATE SERVICE    VERSION
21/tcp  open  ftp        vsftpd 3.0.3
22/tcp  open  ssh        OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|  2048 a96824bc971f1e54a58045e74cd9aaa0 (RSA)
|  256 e5440146ee7abb7ce91acb14999e2b8e (ECDSA)
|_  256 004e1a4f33e8a0de86a6e42a5f84612b (ED25519)
53/tcp  open  domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.11.3-1ubuntu1.2-Ubuntu
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Friend Zone Escape software
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open  ssl/http    Apache httpd 2.4.29
| ssl-cert: Subject: commonName=friendzone.red/organizationName=CODERED/stateOrProvinceName=CODERED/countryName=JO
| Not valid before: 2018-10-05T21:02:30
|_Not valid after:  2018-11-04T21:02:30
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: 404 Not Found
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=1/1%OT=21%CT=1%CU=33726%PV=Y%DS=2%DC=T%G=Y%TM=63B25B9F
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10C%TI=Z%CI=I%TS=A)SEQ(SP=10
OS:0%GCD=1%ISR=10C%TI=Z%TS=A)OPS(O1=M537ST11NW7%O2=M537ST11NW7%O3=M537NNT11
OS:NW7%O4=M537ST11NW7%O5=M537ST11NW7%O6=M537ST11)WIN(W1=7120%W2=7120%W3=712
OS:0%W4=7120%W5=7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M537NNSNW7%CC=Y%Q=)
OS:T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=
OS:0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T
OS:6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+
OS:%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK
OS:=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)
Network Distance: 2 hops
Service Info: Hosts: FRIENDZONE, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
Host script results:
|_clock-skew: mean: -39m58s, deviation: 1h09m16s, median: 1s
| smb2-security-mode: 
|  311: 
|_    Message signing enabled but not required
| smb-security-mode: 
|  account_used: guest
|  authentication_level: user
|  challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: FRIENDZONE, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-os-discovery: 
|  OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|  Computer name: friendzone
|  NetBIOS computer name: FRIENDZONE\x00
|  Domain name: \x00
|  FQDN: friendzone
|_  System time: 2023-01-02T06:20:39+02:00
| smb2-time: 
|  date: 2023-01-02T04:20:40
|_  start_date: N/A
TRACEROUTE (using port 143/tcp)
HOP RTT      ADDRESS
1  100.21 ms 10.10.16.1
2  56.08 ms  10.10.10.123
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.37 seconds
```

Feroxbuster small-directories wordlist results:
![[./_resources/HTB_Friendzone.resources/image.png]]
Feroxbuster small-files wordlist results:
![[./_resources/HTB_Friendzone.resources/image.2.png]]

There seems to be a wordpress site that I can scan with WPscan. However, in attempting to scan, I could not get any results. When browsing the directory in Burp, there seems to be nothing there.

In checking SMB shares, I did find the following with a creds.txt. in the "general" share.:
![[./_resources/HTB_Friendzone.resources/image.1.png]]
Raw data for creds.txt:
```
└─$ cat creds.txt     
creds for the admin THING:
admin:WORKWORKHhallelujah@#
```
These credentials may be a login to a portal somwhere. After finding the email address mentioned on the main friendzone page hosted on port 80, I remembered that 443 was also opened. In attempting to open the domain for the email I found using port 443, I obtained the following page.:
```
https://friendzoneportal.red/
```

![[./_resources/HTB_Friendzone.resources/image.3.png]]
Feroxbuster rerun using small-files wordlist:
![[./_resources/HTB_Friendzone.resources/image.4.png]]
I need to scan for directories, check out the wp-register.php directory, and search for a login portal to use the previously found admin credentials. SSH, FTP, SMB do not accept the credentials. However, there is an "admin" user on the machine per the SMB checks I performed with CME which also confirmed the hostname of the machine as well (originally shown in the nmap scan).:
![[./_resources/HTB_Friendzone.resources/image.5.png]]

Feroxbuster results obtained with small-directories wordlist.:
![[./_resources/HTB_Friendzone.resources/image.6.png]]
One step I missed was to do a zone transfer using dig on [friendzone.red](http://friendzone.red) which showed up in the nmap scan. I performed the transfer and added the newly found domains to the /etc/hosts file on my attacker machine as shown below.:
![[./_resources/HTB_Friendzone.resources/image.7.png]]

After logging into the [administrator1.friendzone.red](http://administrator1.friendzone.red) domain using the previously found login credentials, I obtained the following result.:
![[./_resources/HTB_Friendzone.resources/image.8.png]]![[./_resources/HTB_Friendzone.resources/image.9.png]]

Oddly, it asks me to manually enter the image\_name parameter and the page is under development.
![[./_resources/HTB_Friendzone.resources/image.10.png]]
After entering the above shown parameter, I got the following.:
![[./_resources/HTB_Friendzone.resources/image.11.png]]
feroxbuster results I obtained, I found another images directory using small-files wordlist.:
![[./_resources/HTB_Friendzone.resources/image.12.png]]
I had to lookup the next step. I learned that the URL https://administrator1.friendzone.red/dashboard.php?image\_id=a.jpg&pagename=timestamp is executing both the image and the file called timestamp.php. Additionally, it is assumed in this case that the previously writable "Development" share I previously accessed via SMB is in the /etc/Development path. Apparently, I should be able to get a reverse shell by uploading a php reverse shell to the SMB directory I previously visited and they trigger it using this URL but adding in the new directory as follows.:  https://administrator1.friendzone.red/dashboard.php?image\_id=a.jpg&pagename=/etc/Development/test

I obtained a reverse shell and found the user.txt flag.:
```
02eb6f4ad235a86ca853b5da0928198c
```

Next, I found in /var/www/mysql\_data.conf, the credentials for Friend which I was able to use to SSH into the machine to escalate privileges.
Raw Data:
```
$ cat mysql_data.conf
for development process this is the mysql creds for user friend

db_user=friend

db_pass=Agpyu12!0.213$

db_name=FZ
```

After running [Linpeas.sh](http://Linpeas.sh) I found that there was a writeable [os.py](http://os.py) file which also was being pulled by a cronjob with root privileges. I went ahead and added a reverse shell at the bottom of the file and saved it. After waiting for 2 minutes, (which is the frequency of the cronjob executing the [os.py](http://os.py) file) I got a root shell.:
![[./_resources/HTB_Friendzone.resources/image.13.png]]
Root shell:
![[./_resources/HTB_Friendzone.resources/image.14.png]]
Root flag:
```
142e41743d7596a52fd9d24d70df9e7b
```

**###ROOTED!!!###**

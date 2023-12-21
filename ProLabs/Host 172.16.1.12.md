# Host [172.16.1.12](http://172.16.1.12):

```
Nmap scan report for 172.16.1.12
Host is up (0.052s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
21/tcp  open  ftp
22/tcp  open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http    Apache httpd 2.4.43 ((Unix) OpenSSL/1.1.1g PHP/7.4.7 mod_perl/2.0.11 Perl/v5.30.3)
443/tcp  open  ssl/http Apache httpd 2.4.43 ((Unix) OpenSSL/1.1.1g PHP/7.4.7 mod_perl/2.0.11 Perl/v5.30.3)
3306/tcp open  mysql?
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port21-TCP:V=7.92%I=7%D=9/18%Time=6327AADE%P=x86_64-pc-linux-gnu%r(Gene
SF:ricLines,8F,"220\x20ProFTPD\x20Server\x20\(ProFTPD\)\x20\[::ffff:172\.1
SF:6\.1\.12\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20cre
SF:ative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creative
SF:\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port3306-TCP:V=7.92%I=7%D=9/18%Time=6327AAD3%P=x86_64-pc-linux-gnu%r(NU
SF:LL,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GenericLine
SF:s,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allow
SF:ed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GetRequest,4
SF:B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(HTTPOptions,4B,
SF:"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x2
SF:0to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(RTSPRequest,4B,"G
SF:\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20t
SF:o\x20connect\x20to\x20this\x20MariaDB\x20server")%r(RPCCheck,4B,"G\0\0\
SF:x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to\x20
SF:connect\x20to\x20this\x20MariaDB\x20server")%r(DNSVersionBindReqTCP,4B,
SF:"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x2
SF:0to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(DNSStatusRequestT
SF:CP,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Help,4B,"G\
SF:0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to
SF:\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SSLSessionReq,4B,"G\
SF:0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to
SF:\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TerminalServerCookie
SF:,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowe
SF:d\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TLSSessionReq
SF:,4B,"G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowe
SF:d\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Kerberos,4B,"
SF:G\0\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20
SF:to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SMBProgNeg,4B,"G\0
SF:\0\x01\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to\
SF:x20connect\x20to\x20this\x20MariaDB\x20server")%r(X11Probe,4B,"G\0\0\x0
SF:1\xffj\x04Host\x20'172\.16\.1\.100'\x20is\x20not\x20allowed\x20to\x20co
SF:nnect\x20to\x20this\x20MariaDB\x20server");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.02 seconds
zsh: segmentation fault  proxychains nmap -sV 172.16.1.12
```

After conducting a directory bruteforce, I found a list of team members.:
![](./_resources/HTB_Pro_Lab_Dante.resources/ferox172.txt)

![](./_resources/HTB_Pro_Lab_Dante.resources/image.30.png)
# HTB Lame

Step #1: Nmap scan results:

![[./_resources/HTB_Lame.resources/image.png]]
Nmap aggressive scan.:
![[./_resources/HTB_Lame.resources/image.1.png]]
Step #2: FTP Accepts Anon login

There were no directories in the anon FTP account.

Step #3: SMB has publicly available shares.:
![[./_resources/HTB_Lame.resources/image.2.png]]
After failing to exploit most of the ports, I found that the Samba SMB version has a vulnerability (CVE2007-2447) that allows me to immediately gain a root shell. The module in msfconsole for this is called **_multi/samba/usernmap\_script_**.

Root flag in the user makis directory.:
Raw Data:
```
total 28
drwxr-xr-x 2 makis makis 4096 Mar 14  2017 .
drwxr-xr-x 6 root  root  4096 Mar 14  2017 ..
-rw------- 1 makis makis 1107 Mar 14  2017 .bash_history
-rw-r--r-- 1 makis makis  220 Mar 14  2017 .bash_logout
-rw-r--r-- 1 makis makis 2928 Mar 14  2017 .bashrc
-rw-r--r-- 1 makis makis  586 Mar 14  2017 .profile
-rw-r--r-- 1 makis makis    0 Mar 14  2017 .sudo_as_admin_successful
-rw-r--r-- 1 makis makis  33 Dec 23 15:56 user.txt
cat user.txt
99d1b578d238855b21c63a4f2cea004c
```

I then quickly found the root flag in the root directory.:
Raw Data:
```
cat root.txt
00bb6d2df1e1be2a5c25e82794dac1c0
```

**###ROOTED!!###**

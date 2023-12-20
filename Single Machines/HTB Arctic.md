# HTB Arctic

Basic nmap scan:

```
Nmap scan report for 10.10.10.11
Host is up (0.078s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT      STATE SERVICE
135/tcp  open  msrpc
8500/tcp  open  fmtp
49154/tcp open  unknown
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 756.83 seconds
          Raw packets sent: 131630 (5.792MB) | Rcvd: 1081 (75.056KB)
```

Aggressive nmap scan:

```
Nmap scan report for 10.10.10.11
Host is up (0.094s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT      STATE SERVICE VERSION
135/tcp  open  msrpc  Microsoft Windows RPC
8500/tcp  open  fmtp?
49154/tcp open  msrpc  Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|phone|specialized
Running (JUST GUESSING): Microsoft Windows 8|Phone|2008|7|8.1|Vista (91%)
OS CPE: cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_8.1 cpe:/o:microsoft:windows_vista::- cpe:/o:microsoft:windows_vista::sp1
Aggressive OS guesses: Microsoft Windows 8.1 Update 1 (91%), Microsoft Windows Phone 7.5 or 8.0 (91%), Microsoft Windows 7 or Windows Server 2008 R2 (90%), Microsoft Windows Server 2008 R2 (90%), Microsoft Windows Server 2008 R2 or Windows 8.1 (90%), Microsoft Windows Server 2008 R2 SP1 (90%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (90%), Microsoft Windows 7 (90%), Microsoft Windows 7 Professional or Windows 8 (90%), Microsoft Windows 7 SP1 or Windows Server 2008 R2 (90%)
No exact OS matches for host (test conditions non-ideal).
Uptime guess: 0.005 days (since Fri Mar 24 17:55:44 2023)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
TRACEROUTE (using port 135/tcp)
HOP RTT      ADDRESS
1  121.09 ms 10.10.16.1
2  121.12 ms 10.10.10.11
NSE: Script Post-scanning.
Initiating NSE at 18:02
Completed NSE at 18:02, 0.00s elapsed
Initiating NSE at 18:02
Completed NSE at 18:02, 0.00s elapsed
Initiating NSE at 18:02
Completed NSE at 18:02, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 149.30 seconds
          Raw packets sent: 2099 (96.892KB) | Rcvd: 56 (4.020KB)
```

In checking port 8500, I was able to access this in a browser and it shows a couple of directories and has to do with Adobe Cold Fusion which is a web app dev platform for deploying web and mobile applications.:
![[./_resources/HTB_Arctic.resources/image.png]]

Clicking on the first directory, I then find multiple child directories and one of them is the Administrator directory which takes me to the following login page where the username "admin" is already prepopulated. I should be able to bruteforce my way into the application assuming common default credentials don't work.
![[./_resources/HTB_Arctic.resources/image.1.png]]

I was not able to break in with several default credentials. However, after investigating the name of this platform and the version, I found a quick script/exploit that creates a shell via RCE exploit after executing it. Link here: <https://www.exploit-db.com/exploits/50057>

![[./_resources/HTB_Arctic.resources/image.3.png]]![[./_resources/HTB_Arctic.resources/image.2.png]]

User enumeration:
![[./_resources/HTB_Arctic.resources/image.4.png]]

user.txt flag found:
![[./_resources/HTB_Arctic.resources/image.5.png]]

After viewing IPPSEC's video, he used exploit suggester in msfconsole to find a way to escalate privileges. This did not work for me for some reason. I need to figure out where to go from here. I will watch to see what exploit suggester he found and see if I can leverage that some other way. I think the main issue I have with this machine is that it is a limited reverse shell and not a fully interactive one.

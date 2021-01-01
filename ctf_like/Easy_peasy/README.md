
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-1.png)
This room was very guessy, i didn't enjoy it that much.

Let's start with a porst scanning. I will do a more detailed scan.

```text
nmap -sV -sC -p- -T 4 -Pn --max-retries 0 10.10.154.16
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-03 05:09 EDT
Warning: 10.10.154.16 giving up on port because retransmission cap hit (0).
Nmap scan report for 10.10.154.16
Host is up (0.087s latency).
Not shown: 46673 closed ports, 18859 filtered ports
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: nginx/1.16.1
|_http-title: Welcome to nginx!
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
I will start enumerating with http service on port 65524.
After taking a closer look at the default ubuntu welcoming page i found flag 3.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-2.png)

The page source has a string encoded in some base. I pasted the string in cyberchef and found out that the base was base64 and the decoded equivalent is:  /n0th1ng3ls3m4tt3r  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-3.png)

The page in the directory /n0th1ng3ls3m4tt3r contains what look like a hash, and an image.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-4.png)

After a long time of searching i found this website, https://md5hashing.net/  which successfully cracked the hash. 
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-5.png)

This room was labeled as steganography too, and the image i can use is the little one from the /n0th1ng3ls3m4tt3r directory. The perfect tool for this job is steghide.
After extracting, the secret message is:

```text
username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
```

The password docoded from binary is: iconvertedmypasswordtobinary  
I remembered, the nmap scan found this file Robots.txt and i did not accessed it. The page has something that looks like a hash as User-Agent. After i decoded this hash i had the second flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-6.png)

With the found credentials i can log in into the ssh service. The user flag looks caesar encoded.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-7.png)

In /etc/crontab is an interesting scheduled task.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-8.png)

This file belongs to boring user and i can modify it however i want.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-9.png)

The main advantage is that, when is executed is running with root privileges.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-10.png)

I opted for a reverse shell and now i am root.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-11.png)

Here is the root flag, but i don't have all the flags.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-12.png)

didn't fully enumerated the website on port 80 and it's a best time to start now.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-13.png)

I had alot of trouble with directory bruteforcing, mostly i encountered this error, (Client.Timeout exceeded while awaiting headers), and i had to rerun the querry again. I don't know what caused this problem.  


```text

gobuster dir -u http://10.10.154.16 -w /usr/share/wordlists/dirb/big.txt --timeout 20s 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.154.16
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        20s
===============================================================
2020/08/03 05:52:47 Starting gobuster
===============================================================
/hidden (Status: 301)

The second round, as the first directory didn't have much of use.

gobuster dir -u http://10.10.154.16/hidden -w /usr/share/wordlists/dirb/big.txt --timeout 20s 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.154.16/hidden
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/big.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        20s
===============================================================
2020/08/03 06:00:22 Starting gobuster
===============================================================
/whatever (Status: 301)
```

This is /hidden  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-14.png)

This is hidden/whatever  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-15.png)  
This page contains the first flag base64 encoded.   
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Easy_peasy/images/46-16.png)

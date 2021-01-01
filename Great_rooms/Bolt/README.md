
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-1.png)
In this room i exploited a vulnerable cms.

As always, let's start with a ports scanning.

```text
nmap -sS -sV -p- -T 4 -Pn --max-retries 0 10.10.153.207
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-13 05:05 EDT
Warning: 10.10.153.207 giving up on port because retransmission cap hit (0).
Nmap scan report for 10.10.153.207
Host is up (0.12s latency).
Not shown: 59780 closed ports, 5752 filtered ports
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.29 ((Ubuntu))
8000/tcp open  http-alt
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 113.51 seconds
```

```text
nmap -sC -sV -p22,80,8000 -T 4 -Pn --max-retries 0 10.10.153.207
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-13 05:09 EDT
Nmap scan report for 10.10.153.207
Host is up (0.063s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:85:ec:54:f2:01:b1:94:40:de:42:e8:21:97:20:80 (RSA)
|   256 77:c7:c1:ae:31:41:21:e4:93:0e:9a:dd:0b:29:e1:ff (ECDSA)
|_  256 07:05:43:46:9d:b2:3e:f0:4d:69:67:e4:91:d3:d3:7f (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8000/tcp open  http    (PHP 7.2.32-1)
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Date: Thu, 13 Aug 2020 09:09:13 GMT
|     Connection: close
|     X-Powered-By: PHP/7.2.32-1+ubuntu18.04.1+deb.sury.org+1
|     Cache-Control: private, must-revalidate
|     Date: Thu, 13 Aug 2020 09:09:13 GMT
|     Content-Type: text/html; charset=UTF-8
|     pragma: no-cache
|     expires: -1
|     X-Debug-Token: 73525b
|     <!doctype html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Bolt | A hero is unleashed</title>
|     <link href="https://fonts.googleapis.com/css?family=Bitter|Roboto:400,400i,700" rel="stylesheet">
|     <link rel="stylesheet" href="/theme/base-2018/css/bulma.css?8ca0842ebb">
|     <link rel="stylesheet" href="/theme/base-2018/css/theme.css?6cb66bfe9f">
|     <meta name="generator" content="Bolt">
|     </head>
|     <body>
|     href="#main-content" class="vis
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Thu, 13 Aug 2020 09:09:12 GMT
|     Connection: close
|     X-Powered-By: PHP/7.2.32-1+ubuntu18.04.1+deb.sury.org+1
|     Cache-Control: public, s-maxage=600
|     Date: Thu, 13 Aug 2020 09:09:12 GMT
|     Content-Type: text/html; charset=UTF-8
|     X-Debug-Token: cba4c6
|     <!doctype html>
|     <html lang="en-GB">
|     <head>
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <title>Bolt | A hero is unleashed</title>
|     <link href="https://fonts.googleapis.com/css?family=Bitter|Roboto:400,400i,700" rel="stylesheet">
|     <link rel="stylesheet" href="/theme/base-2018/css/bulma.css?8ca0842ebb">
|     <link rel="stylesheet" href="/theme/base-2018/css/theme.css?6cb66bfe9f">
|     <meta name="generator" content="Bolt">
|     <link rel="canonical" href="">http://0.0.0.0:8000/">
|     </head>
|_    <body class="front">
|_http-generator: Bolt
|_http-title: Bolt | A hero is unleashed
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
```

The first page of the website has a few post from the admin. In one of them is specified his password.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-2.png)

Let's check if this cms is vulnerable to any exploit. There are a few between i can choose from.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-3.png)

After looking at most of them i decided, the most suited for this machine is the rce one. Luckily it had also a metasploit module.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-4.png)

I set the required fields.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-5.png)

Let's run the exploit.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-6.png)

Here is the flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Bolt/images/56-7.png)

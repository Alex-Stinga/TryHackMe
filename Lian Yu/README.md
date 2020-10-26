

Lian yu is a beginner level room on Tryhackme based on the TV show The Arrow.

Let's scan the address to see which ports and services are open.

```text
# Nmap 7.80 scan initiated Fri Jul 24 06:30:41 2020 as: nmap -sV -sC -p- -Pn 10.10.47.102
Nmap scan report for 10.10.47.102
Host is up (0.058s latency).
Not shown: 65530 closed ports
PORT STATE SERVICE VERSION
21/tcp open ftp vsftpd 3.0.2
22/tcp open ssh OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey:
| 1024 56:50:bd:11:ef:d4:ac:56:32:c3:ee:73:3e:de:87:f4 (DSA)
| 2048 39:6f:3a:9c:b6:2d:ad:0c:d8:6d:be:77:13:07:25:d6 (RSA)
| 256 a6:69:96:d7:6d:61:27:96:7e:bb:9f:83:60:1b:52:12 (ECDSA)
|_ 256 3f:43:76:75:a8:5a:a6:cd:33:b0:66:42:04:91:fe:a0 (ED25519)
80/tcp open http Apache httpd
|_http-server-header: Apache
|_http-title: Purgatory
111/tcp open rpcbind 2-4 (RPC #100000)
| rpcinfo:
| program version port/proto service
| 100000 2,3,4 111/tcp rpcbind
| 100000 2,3,4 111/udp rpcbind
| 100000 3,4 111/tcp6 rpcbind
| 100000 3,4 111/udp6 rpcbind
| 100024 1 33814/tcp status
| 100024 1 34825/tcp6 status
| 100024 1 48701/udp6 status
|_ 100024 1 55506/udp status
33814/tcp open status 1 (RPC #100024)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

It seems to have this page only, but i will bruteforce directories hoping somewhere there are hidden pages.

![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-1.png)

It found a directory /island, but i will use gobuster again to search for other pages.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-2.png)

Here is other file.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-3.png)

In the /island page is this word vigilante which can be an username, password, etc.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-4.png)

The /island/2100 has this youtube video and a hint in the source code.With the help of the hint i realized  should bruteforce directories again, but this time with the extension ticket set.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-5.png)
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-6.png)

Checking the hint, .ticket is the extension of an important page. Let's bruteforce it.

![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-7.png)

The value is base58 encoded and together with the found username i can acces the files on the ftp server.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-8.png)

There are 3 images and a file called .other_user, which i noticed after i got stucked and i needed to take a few steps back.

![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-9.png)

There is an image which is probably corrupted.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-11.png)

This is aa.jpg.  
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-12.png)

This is Queen's gambit.png  
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-13.png)

The Leave me alone is corrupted. We canverify why is it is like that by opening it with xxd. The file is missing it's PNG signature.  

![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-14.png)

This can be fixed easily by modifying the hex bytes with hexeditor.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-15.png)

The repaired image is:  
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-16.png)

The Leave me alone file contained the password, so the other ones may contain the message. Let's begin with the jpeg image using steghide and the found password.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-17.png)

This file contained an archive.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-18.png)

The shado file contains what look like a password. But, what is the ssh username? I checked again the ftp server again and download the .other_user file.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-19.png)

This note is about slade, so this must be the needed username.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-20.png)

Hare is the user flag.  
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-21.png)

Now it's time for privesc. Let's check his sudo rights.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-22.png)

Pkexec is a command that runs a program as superuser.
![alt_img](https://github.com/Alex-Stinga/TryHackMe/blob/master/Lian%20Yu/images/31-23.png)

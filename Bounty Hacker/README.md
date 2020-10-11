
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-1.png)

In this room, i accessed a ftp server which had the default credentials ftp:ftp or anonymous:anonymous and downloaded the 2 files. One of them has a message from which i know the username of an user and the other file has a list of probable passwords. Because the only probable service login is ssh i attempted to bruteforce the password of a known user. The privilege escalation was easy to perform as it was done by checking the sudo privileges of the current user and using a payload found for the /bin/tar archive found on GTFOBins.

The nmap scan

```text

nmap -sV -T 4 10.10.54.154
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-31 04:32 EDT
Nmap scan report for 10.10.54.154
Host is up (0.10s latency).
Not shown: 967 filtered ports, 30 closed ports
PORT STATE SERVICE VERSION
21/tcp open ftp vsftpd 3.0.3
22/tcp open ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open http Apache httpd 2.4.18 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

The website holds this page, which does not have anything interesting.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-2.png)

The ftp server is worth to check, even if i don't have the credentials i try to log in with the default username ftp or anonymous. If i succed, like i did in this room, i would download any files the server has.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-3.png)

The locks.txt hold a list of passwords, task.txt is below.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-4.png)

We do have the username and a list of passwords. To make the bruteforcing easier i used hydra.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-5.png)

Let's login to ssh with the found credentials.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-6.png)

The root flag is the last task on tryhackme list. The user lin can perform sudo only with tar command.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-7.png)

A great website with plenty of privilege escalation payloads is: https://gtfobins.github.io/gtfobins/tar/

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-8.png)


![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Bounty%20Hacker/images/41-9.png)

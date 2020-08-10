
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/thm.png)  

Challenge link: https://tryhackme.com/room/inclusion  

Let's start with a nmap scan to see which ports are open and what services are running. Looking at the results it seems the services started are ssh and http.

```text
Nmap 7.80 scan initiated Sat Jul 11 06:05:21 2020 as: nmap -p- -sV -T 4 10.10.171.37
Nmap scan report for 10.10.171.37
Host is up (0.062s latency).
Not shown: 65523 closed ports
PORT      STATE    SERVICE   VERSION
22/tcp    open     ssh       OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http      Werkzeug httpd 0.16.0 (Python 3.6.9)
```

 The website has 3 functional buttons, each of them points to a page which describe a specific file inclusion attack. I recommend reading the contents as it's a good summary of what file inclusion is.  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/1.png)   

The 'name' parameter in these pages can be used to perform a lfi attack, since is the only parameter that permits requesting webpages. 

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/2.png)

To verify if the lfi attack worked, i requested the contents of /etc/passwd. Based on the result, it did. Looking carefully at the response, i noticed a comment that resemble as a username and password, 'falconfeast:rootpassword'. This credentials can be used to log in via ssh (in the nmap scan the ssh service was running). 

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/3.png)

The user flag is in the currect directory of the falcon user.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/4.png)

To escalate privileges to root, it is necessary to get more info about the falcon user.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/5.png)

He is in the sudo group and can use only the command socat.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/6.png)

The payload used to get root acces was found on the GTFObins.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/7.png)

The flag is in a directory called /root/root.txt
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Inclusion/8.png)

Overall it was a fun exercise. I learned new things about privilege escalation, socat command, and how to use the socat command in it's intended away, but also malicious way.

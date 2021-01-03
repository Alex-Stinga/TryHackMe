
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-0.png)

I exploited a webdav service by using a tool called cadaver to upload files to the website. Cadaver is a webdav utility similarly to ftp command line. 

I started with a ports scanning.

```text
nmap -sS -T4 10.10.12.161 -p-
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-29 05:23 EDT
Nmap scan report for 10.10.12.161
Host is up (0.095s latency).
Not shown: 65534 closed ports
PORT STATE SERVICE
80/tcp open http


nmap -sV -sC -p80 10.10.12.161
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-29 05:12 EDT
Nmap scan report for 10.10.12.161
Host is up (0.053s latency).

PORT STATE SERVICE VERSION
80/tcp open http Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works

```

There is only one port open and displays the default ubuntu page. I did a quick directory bruteforcing and found an interesting page.

```text
{
   "commandline":"./ffuf -u http://10.10.12.161/FUZZ -w /usr/share/wordlists/dirb/common.txt -o /root/Documents/tryhackme/Dav/directories.txt",
   "time":"2020-09-29T05:16:53-04:00",
   "results":[
      {
         "input":{
            "FUZZ":".htpasswd"
         },
         "position":13,
         "status":403,
         "length":296,
         "words":22,
         "lines":12,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/.htpasswd",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":".htaccess"
         },
         "position":12,
         "status":403,
         "length":296,
         "words":22,
         "lines":12,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/.htaccess",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":".hta"
         },
         "position":11,
         "status":403,
         "length":291,
         "words":22,
         "lines":12,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/.hta",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":""
         },
         "position":1,
         "status":200,
         "length":11321,
         "words":3503,
         "lines":376,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":"index.html"
         },
         "position":2021,
         "status":200,
         "length":11321,
         "words":3503,
         "lines":376,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/index.html",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":"server-status"
         },
         "position":3589,
         "status":403,
         "length":300,
         "words":22,
         "lines":12,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/server-status",
         "host":"10.10.12.161"
      },
      {
         "input":{
            "FUZZ":"webdav"
         },
         "position":4395,
         "status":401,
         "length":459,
         "words":42,
         "lines":15,
         "redirectlocation":"",
         "resultfile":"",
         "url":"http://10.10.12.161/webdav",
         "host":"10.10.12.161"
      }
   ],
   "config":{
      "headers":{
         
      },
      "extensions":[
         
      ],
      "dirsearch_compatibility":false,
      "method":"GET",
      "url":"http://10.10.12.161/FUZZ",
      "postdata":"",
      "quiet":false,
      "colors":false,
      "inputproviders":[
         {
            "name":"wordlist",
            "keyword":"FUZZ",
            "value":"/usr/share/wordlists/dirb/common.txt"
         }
      ],
      "cmd_inputnum":100,
      "inputmode":"clusterbomb",
      "outputdirectory":"",
      "outputfile":"/root/Documents/tryhackme/Dav/directories.txt",
      "outputformat":"json",
      "ignorebody":false,
      "ignore_wordlist_comments":false,
      "stop_403":false,
      "stop_errors":false,
      "stop_all":false,
      "follow_redirects":false,
      "autocalibration":false,
      "autocalibration_strings":[
         
      ],
      "timeout":10,
      "delay":{
         "value":"0.00"
      },
      "filters":{
         
      },
      "matchers":{
         "status":{
            "value":"200,204,301,302,307,401,403"
         }
      },
      "threads":40,
      "proxyurl":"",
      "replayproxyurl":"",
      "cmdline":"./ffuf -u http://10.10.12.161/FUZZ -w /usr/share/wordlists/dirb/common.txt -o /root/Documents/tryhackme/Dav/directories.txt",
      "verbose":false,
      "maxtime":0,
      "maxtime_job":0,
      "recursion":false,
      "recursion_depth":0
   }
}
```

The /webdav page contains this file which conins this credentials: 
```text
 wampp:$apr1$Wm2VTkFL$PVNRQv7kzqXQIHe14qKA91
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-1.png)  

Luckily, i found this website which turned into a tutorial:  https://www.samsclass.info/124/proj14/p12-php.htm .According to the website i can use a tool called cadaver and i can list and upload files. This service also require authentication but i mamange to log in with the default credentials wampp:xammp.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-2.png)

I uploaded the file and pasted the file name in the url to start the process.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-3.png)

The current user is www-data.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-4.png)

The user flag is in the merlin's home folder.  
```text
www-data@ubuntu:/home/merlin$ ls -la
ls -la
total 44
drwxr-xr-x 4 merlin merlin 4096 Aug 25 2019 .
drwxr-xr-x 4 root root 4096 Aug 25 2019 ..
-rw------- 1 merlin merlin 2377 Aug 25 2019 .bash_history
-rw-r--r-- 1 merlin merlin 220 Aug 25 2019 .bash_logout
-rw-r--r-- 1 merlin merlin 3771 Aug 25 2019 .bashrc
drwx------ 2 merlin merlin 4096 Aug 25 2019 .cache
-rw------- 1 merlin merlin 68 Aug 25 2019 .lesshst
drwxrwxr-x 2 merlin merlin 4096 Aug 25 2019 .nano
-rw-r--r-- 1 merlin merlin 655 Aug 25 2019 .profile
-rw-r--r-- 1 merlin merlin 0 Aug 25 2019 .sudo_as_admin_successful
-rw-r--r-- 1 root root 183 Aug 25 2019 .wget-hsts
-rw-rw-r-- 1 merlin merlin 33 Aug 25 2019 user.txt
www-data@ubuntu:/home/merlin$
```
This user can use sudo with command cat.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Dav/images/111-5.png)

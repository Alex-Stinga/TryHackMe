![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-1.png)

This room was pretty easy. I found the credentials of a user in the source of the only existing page, then login via ssh. Then look for the pokemons in various locations.

First of all, a ports scan.

```text
nmap -sS -T4 -p- 10.10.232.133 --max-retries 0

Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-09 09:20 EDT
Stats: 0:00:01 elapsed; 0 hosts completed (0 up), 1 undergoing Ping Scan
Ping Scan Timing: About 100.00% done; ETC: 09:20 (0:00:00 remaining)
Warning: 10.10.232.133 giving up on port because retransmission cap hit (0).
Nmap scan report for 10.10.232.133
Host is up (0.063s latency).
Not shown: 46720 closed ports, 18813 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```


The page source has this script which,when called prints the list of pokemons.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-2.png)

When i first saw those tags i did't know that they could be useful at all. It turns out, they are the username and password to be used to login via ssh.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-3.png)

As i am in the system, i will look for the flags/pokemons. I am in pokemon's home directory and will started verifying  every folder from left to right.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-4.png)

I found this directory just because it catched my eyes when i was doing something else.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-5.png)

This pokemon was the second, easiest to find, as the hint suggested to look for a specific location.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-6.png)

It was a good thing, that i verified each folder in the home directory, because i found this file which contains the root credentials.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Gotta%20catch'em%20all/images/88-7.png)

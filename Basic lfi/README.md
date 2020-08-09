
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/thmlfi.png)

Challenge link: https://tryhackme.com/room/lfibasics

The first and second part of the room concentrates on demonstrating how the parametrs can be exploited through lfi.

The lfi lessons are sectioned in 3 parts. The first and second parts are walkthrough and are self explanatory.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/1.png)

This was a basic example which demonstrated that an included file that exist on the server can be displayed if requested. This is a harmless example, but an attacker can acces sensitive file as /etc/shadow.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/1.2.png)

In this example i accesed the contents of /etc/passwd file which contains details about the users.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/3.png)

Here is a more readable image of the /etc/passwd.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/4.png)

The task asked for the credit card number located in the creditcard page.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/5.png)

Apache uses the access log files to record information about every visitor of the website. The information stored are the IP addres, the requested page,the response code.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/6.png)

The following images ilustrates how LFI can become RCE by modifying the User-Agent field. To check if the exploit worked, i executed the command ifconfig which displays the current network configuration.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/7.png)

I could not sumbit commands separated by spaces because the server didn't understand my request and from, but i found a workaround by url encoding them. This method worked for me, but there may be other ways of solving it, maybe even better. The command in the example is 'uname -r', which displays the current version of linux.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/8.png)

The encoded command is 'ls home/lfi'.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/9.png)

The command is 'cat /home/lfi/flag.txt'.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Basic%20lfi/10.png)

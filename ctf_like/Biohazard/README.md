

The nmap scan reveals there are 3 open ports.

```text
nmap -sV -p- 10.10.47.146
Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-16 06:04 EDT
Nmap scan report for 10.10.47.146
Host is up (0.058s latency).
Not shown: 65523 closed ports
PORT STATE SERVICE VERSION
21/tcp open ftp vsftpd 3.0.3
22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open http Apache httpd 2.4.29 ((Ubuntu))
```

It seems i need to follow the story of STARS alpha team.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-1.png)

Let's follow the link.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-2.png)

This page don't seem to hold any information regarding of the next roon, so let's check the page source.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-3.png)

Let's go to  /diningRoom
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-4.png)

The link has this string.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-5.png)

After refreshing the page, an input field appeared.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-6.png)

Aaand nothing happened after inputting the emblem.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-7.png)

Looking at the source of the diningroom i found this encoded string, which is base64 encoded. Decoding it gives 'How about the /teaRoom/'
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-8.png)

The tearoom is not very pleasant
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-9.png)

The lockpick link holds the flag, lock_pick{037b35e2ff90916a9abf99129c8e1837}
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-10.png)

The paper stick holds a map which is very useful.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-11.png)

I will check them in the order they are on the map. The baroom
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-12.png)

I submitted the found lockpick, and now the received page has some additional text
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-13.png)

The page has the following base32 encoded string which decodes to music_sheet{362d72deaf65f5bdc63daece6a1f676e}
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-14.png)

Playing the piano, we found a secret bar room. Which holds an emblem, gold_emblem{58a8c41a9d08b8a4e38d02a4d7ff4843}
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-15.png)

After refreshing the page an input field appeared. The first instinct is to input the gold_emblem, but nothing happens if submited. I tried with the enblem and the respnse was ‘rebbeca’. This is strange.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-16.png)

Maybe the emblem are switched and i need to go back in dinigroom and submit the gold-emblem. To my surpise, this was a good ideea. After submitting, the response was...
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-17.png)

From the hint, i know this is vigenere and i can decrypt it using the found key ‘rebecca’. The plaintext is ‘there is a shield key inside the dining room. The html page is called the_great_shield_key’
Next page is diningRoom/the_gret_shield_key.html, and holds the flag, shield_key{48a7a9227cd7eb89f0a062590798cbac}
If the key is name shield_key let's go to armor room.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-18.png)

It needs a shield symbol, luckily i got one.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-19.png)

The note contains:
```text
crest 3:
MDAxMTAxMTAgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAxMDAgMDExMDAxMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMTEgMDAxMDAwMDAgMDAxMTAxMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTAxMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMDEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTEwMDA=
Hint 1: Crest 3 has been encoded three times
Hint 2: Crest 3 contanis 19 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```
Decoding from base64, then binary, then hex resulted: 'c3M6IHlvdV9jYW50X2h'

Now i am in a dead end. So i will go in another room, in diningRoom2F.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-20.png)

Once again the source code has something interesting
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-21.png)

I used quiquip and the text decode to ‘You get the blue gem by pushing the status to the lower floor. The gem is on the diningRoom first floor. Visit sapphire.html
So i nedd to go to /diningroom/sapphire.html. The gem is blue_jewel{e1d457e96cac640f863ec7bc475d48aa}
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-22.png)


It needs a gem, a blue gem? Yes. Now i have crest 1.
```text
crest 1:
S0pXRkVVS0pKQkxIVVdTWUpFM0VTUlk9
Hint 1: Crest 1 has been encoded twice
Hint 2: Crest 1 contanis 14 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```

Decoding from base64, then base32 results: RlRQIHVzZXI6IG.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-23.png)

The note conatins:

```text
crest 2:
GVFWK5KHK5WTGTCILE4DKY3DNN4GQQRTM5AVCTKE
Hint 1: Crest 2 has been encoded twice
Hint 2: Crest 2 contanis 18 letters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```
Decoing from base32, then base58 resulted: 'h1bnRlciwgRlRQIHBh'
To the next location, the attic

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-24.png)

I have the shield key.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-25.png)

The nots has a crest
```text
crest 4:
gSUERauVpvKzRpyPpuYz66JDmRTbJubaoArM6CAQsnVwte6zF9J4GGYyun3k5qM9ma4s
Hint 1: Crest 2 has been encoded twice
Hint 2: Crest 2 contanis 17 characters
Note: You need to collect all 4 crests, combine and decode to reavel another path
The combination should be crest 1 + crest 2 + crest 3 + crest 4. Also, the combination is a type of encoded base and you need to decode it
```
```text
From base58, from hex results: pZGVfZm9yZXZlcg==
All crest together: RlRQIHVzZXI6IGh1bnRlciwgRlRQIHBhc3M6IHlvdV9jYW50X2hpZGVfZm9yZXZlcg==
From base64 decodes to: FTP user: hunter, FTP pass: you_cant_hide_forever
```
To download a file use get file_num or mget for multiple files. There are downloaded in the current directory.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-26.png)


![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-27.png)
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-28.png)
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-29.png)

```text
Key123 = cGxhbnQ0Ml9jYW5fYmVfZGVzdHJveV93aXRoX3Zqb2x0 which decoded from base64 is plant42_can_be_destroy_with_vjolt
After using the command below a popup will appear asking for the password.
```
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-30.png)

helmet_key{458493193501d2b94bbab2e727f8db4b}. Location hidden_closet
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-31.png)

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-32.png)
MO disk 1
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-33.png)

```text
The wolf medal
SSH password: T_virus_rules
The study room is the only one remained unvisited.
```
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-34.png)

```text
The book is an archive, after decompresing we got the ssh username: umbrella_guest
Let's login via ssh to the user umbrella with the password T_virus_rules 
```
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-36.png)

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-37.png)
This must be the key. Vigenere decrypt: weasker login password, stars_members_are_my_guinea_pig  
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-38.png)

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-39.png)
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/ctf_like/Biohazard/images/6-40.png)


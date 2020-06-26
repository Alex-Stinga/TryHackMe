The tile is osint, open source intelligence, so i have to extract any possible information from the given photo.  
Challenge link: https://tryhackme.com/room/ohsint

The photo is the well known windows wallpaper.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/WindowsXP.jpg)

I started checking the metadata of the image using exiftool and i found useful information in the Copyright and GPS label.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/1.png)

In osint challenges the web browser is the most used tool, so let's google the Copyright value, OWoodflint.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/5.png)

There are a lot of search results, but the answer for the first question is in the social page of OWoodflint, twitter.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/7.png)

1. What is this users avatar of?
Cat

It is worth mentioning that there is also a bssid addres which will be useful later.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/8.png)

2. What city is this person in?
London

Using the hint, i went to wigle.net page. I inputted and the bssid and i could'n figure it out how to input the coordinate found in the image and i just dragged the map until the coordinates on the right were close to mine.
And the city is London.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/2.png)

3. Whats the SSID of the WAP he connected to?
UnileverWiFi

I just keep zooming in the map, just to see what else is there, and it happened to find the ssid.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/3.png)

4. What is his personal email address?
OWoodflint@gmail.com

5. What site did you find his email address on?
Github

I looked through the pages found and the email address was in his github page.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/4.png)


6. Where has he gone on holiday?
New York

He also has a wordpress website and and it seems he was in New York.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/6.png)

7. What is this persons password?
pennYDr0pper.!

In the case of this one, i just recognized the password format in the google first result, and went to the page to check where exactly is placed. I mistakenly pressed CTRL-A, and found it hidden.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/OhSint/9.png)







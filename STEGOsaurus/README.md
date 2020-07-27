
Challenge link: https://tryhackme.com/room/stegosaurus

The room name is STEGOsaurus, so this is a steganography challenge.
The downloaded file is a picture of a dog. The image is a jpeg file in which messages are embedded using the tool steghide.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/STEGOsaurus/Dogehomemadememe.jpg)

Let's check if we can exctract the message without any passphrase. It extracted the file just fine.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/STEGOsaurus/1stegosaurus.png)

Let's see the image.
![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/STEGOsaurus/p.png)

Before using again steghide i would check the bit planes of the image with stegsolve.jar, just to verify if there is any message. On plane 2 i see a message 'SAVE THE PLANET'.

![alt text](https://github.com/Alex-Stinga/TryHackMe/blob/master/STEGOsaurus/2stegosaurus.png)


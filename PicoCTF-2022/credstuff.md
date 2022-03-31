# PicoCTF 2022
- Challenge: credstuff
- Category: Cryptography
- Points: 100
- Author: Will Hong / Lt 'Syreal' Jones

## Description
We found a leak of a blackmarket website's login credentials. Can you find the password of the user cultiris and successfully decrypt it?
Download the leak [here](https://artifacts.picoctf.net/c/534/leak.tar).
The first user in usernames.txt corresponds to the first password in passwords.txt. The second user corresponds to the second password, and so on.

## Solution
The challenge provides a list of usernames and passwords, but we only need to decode the password for user "cultiris". The files are provided as with "tar" compression.
So let's download and decompress it first:
```bash
wget https://artifacts.picoctf.net/c/534/leak.tar
tar -xf leak.tar                                
cd leak 
```
Now let's find what line the user is and what line their password is:
```bash
cat usernames.txt | grep cultiris -n             
sed -n 378p passwords.txt                        
```

![Password to decrypt](https://imgur.com/aVKHVOZ.png)

It seems that the password is rotated, possibly a ceasar cipher (or ROT13).
You can use an online rot13 decrypt, but I decided to use a quick bash script for it:

```bash
sed -n 378p passwords.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```


![Solution](https://imgur.com/kQjx4UX.png)

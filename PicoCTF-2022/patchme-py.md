# PicoCTF 2022
- Challenge: patchme.py
- Category: Reverse Engineering
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Run this [Python program](https://artifacts.picoctf.net/c/387/patchme.flag.py) in the same directory as this [encrypted flag](https://artifacts.picoctf.net/c/387/flag.txt.enc).

## Writeup
We are presented a simple python script that asks for a password to decrypt the flag.

![Python script running](https://imgur.com/wClHaft.png)

Looking at the source code, we have the plaintext password required in multiple lines.

![Source code](https://imgur.com/xM3qZhp.png)

There are many ways to solve this challenge. I just opted to delete the password check and replace it with `""`. This way I only needed to press `ENTER` to obtain the flag.

![Flag](https://imgur.com/6OlcE8H.png)

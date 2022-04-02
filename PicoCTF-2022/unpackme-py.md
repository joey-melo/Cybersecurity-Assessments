# PicoCTF 2022
- Challenge: unpackme.py
- Category: Reverse Engineering
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Reverse engineer this [Python program](https://artifacts.picoctf.net/c/465/unpackme.flag.py).

## Writeup
We are provided a python script that asks for a password to print the flag.
The source code is encrypted, so we can't really understand it just by reading it.
However, we are alowed to write to the source code, so we can simply add the line `print(plain)` in line 12.

![Altered source code](https://imgur.com/k5pMA6s.png)

And we get the decrypted source code with the flag:

![Flag](https://imgur.com/jD7JKT0.png)

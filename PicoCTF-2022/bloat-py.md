# PicoCTF 2022
- Challenge: bloat.py
- Category: Reverse Engineering
- Points: 200
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Run this Python program in the same directory as this encrypted flag.

## Writeup
We are provided an obfuscated python script that asks for a password.
The best way to tackle this challenge is to look at the source code and try to clean it up a bit.
Lucky me, the first string I cleaned up was the password to obtain the flag.

Here's how I cleaned it up (observe lines 5, 6 and 7):

![Cleaned up code](https://imgur.com/4BlX0ki.png)

I was going to do this process for all obfuscated strings, but the first one was enough.

![Flag](https://imgur.com/DFNV0hr.png)

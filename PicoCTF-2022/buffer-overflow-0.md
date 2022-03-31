# PicoCTF 2022
- Challenge: buffer overflow 0
- Category: Binary Exploitation
- Points: 100
- Author: Alex Fulton / Palash Oswal

## Description
Smash the stack
Let's start off simple, can you overflow the correct buffer? The program is available [here](https://artifacts.picoctf.net/c/521/vuln). You can view source [here](https://artifacts.picoctf.net/c/521/vuln.c). And connect with it using: `nc saturn.picoctf.net 65355`

## Solution
The challenge is a simple buffer overflow solution. The source code has a vulnerable function expecting 16 characters; however, if you enter 20 characters or more, the flag is triggered and dumped.

![Solution](https://imgur.com/32CjNHb.png)

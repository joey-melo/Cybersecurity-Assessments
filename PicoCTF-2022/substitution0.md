# PicoCTF 2022
- Challenge: substitution0
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
A message has come in but it seems to be all scrambled. Luckily it seems to have the key at the beginning. Can you crack this substitution cipher?
Download the message [here](https://artifacts.picoctf.net/c/380/message.txt).

## Writeup
The message is written with a simple substitution cipher and we are provided the key in the beginning of the file.
`QWITJSYHXCNDFERMUKGOPVALBZ`
We can associate this key with the alphabet and make a python script to automatize the substitution process.
Basically, the script below associates, in order, each character of the key with the each character of the alphabet, including lower cases.

```python
#!/usr/bin/env python3
#filename: solve.py

import string

MSG = open("message.txt","r").read() # read the whole message
KEY = MSG.split("\n")[0].strip() # get the key
KEY = KEY + KEY.lower() # make a string comprising the key in uppercase and lowercase
ALPHABET = string.ascii_uppercase + string.ascii_lowercase # make the alphabet in upper in lowercase for substitution

flag = MSG.maketrans(KEY, ALPHABET)
print(MSG.translate(flag))
```

![Flag](https://imgur.com/iqShlqm.png)

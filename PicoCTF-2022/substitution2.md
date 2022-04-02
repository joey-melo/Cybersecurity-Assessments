# PicoCTF 2022
- Challenge: substitution2
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
It seems that another encrypted message has been intercepted. The encryptor seems to have learned their lesson though and now there isn't any punctuation! Can you still crack the cipher?
Download the message [here](https://artifacts.picoctf.net/c/108/message.txt).


## Writeup
I just adapted the script from substitution1 and analyzed it manually. The process was the same. Here's the end result:

```python
#!/usr/bin/env python3
#filename: solve.py
# For this challenge, I used a manual analysis

from collections import Counter
import string
import re

letters = string.ascii_uppercase

def decipher(p, c):
	alphabet = p.upper() + p.lower()
	key = c.upper() + c.lower()
	flag = MSG.maketrans(key, alphabet)
	flag = MSG.translate(flag)
	return flag

def remove(alpha, excl):
	r = ""
	for l in alpha:
		if l not in excl.upper(): r += l
	return r

MSG = open("message.txt","r").read() # read the whole message
print("Raw message:")
print(MSG)

# The first obvious pattern we can find in the text is "picoCTF"
print()
cipher = "fstwXABCDEGHIJKLMNOPQRUVYZ"
plain  = "picoTFUre.sxha.yk.gnvDmlbw"
print(decipher(plain, cipher))
```

![Flag](https://imgur.com/4AYIEy0.png)

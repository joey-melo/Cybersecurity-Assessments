# PicoCTF 2022
- Challenge: substitution1
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
A second message has come in the mail, and it seems almost identical to the first one. Maybe the same thing will work again.
Download the message [here](https://artifacts.picoctf.net/c/415/message.txt).

## Writeup
The message is written with a simple substitution cipher; however, we are not provided the key and have to do a manual analysis.
I built the following script which kind of shows the step-by-step process itself.

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
print("\nStage 1: mkwrWYH = picoCTF")
cipher = "mkwrwyh" 
plain  = "picoCTF"
print(decipher(plain, cipher))
letters = remove(letters, cipher)
print(f"\nLetters remaining: {letters}\n\n")

# The second pattern we can recognize is the following:
# CTFg = CTFs
# paictics = practice
print("\nStage2: gais = srae")
cipher += "gais"
plain  += "srae"
print(decipher(plain, cipher))
letters = remove(letters, cipher)
print(f"\nLetters remaining: {letters}\n\n")

# Now it's a lot easier to read the text and we can decipher complete phrases:
# szort for captbre tze fvaj = short for capture the flog
# are a tupe of coopbter secbritu coopetitiol = are a type of computer security competition
print("\nStage3: zbvjuol = hulgymn")
cipher += "zbvjuol"
plain  += "hulgymn"
print(decipher(plain, cipher))
letters = remove(letters, cipher)
print(f"\nLetters remaining: {letters}\n\n")

# Now just look at the remaining letters and associate them.
print("\nStage4: cdfnqx = kwqdbv")
cipher += "cdfnqx"
plain +=  "kwqdbv"
print(decipher(plain, cipher))
letters = remove(letters, cipher)
print(f"\nLetters not used: {letters}\n\n")
```

![Flag](https://imgur.com/CZN3HBE.png)

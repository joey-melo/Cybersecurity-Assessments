# PicoCTF 2022
- Challenge: basic-mod1
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
We found this weird message being passed around on the servers, we think we have a working decrpytion scheme.
Download the message [here](https://artifacts.picoctf.net/c/394/message.txt).
Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.
Wrap your decrypted message in the picoCTF flag format (i.e. picoCTF{decrypted_message})

## Solution
The challenge gives us the following ciphertext:
`202 137 390 235 114 369 198 110 350 396 390 383 225 258 38 291 75 324 401 142 288 397`
And the following instructions to decode it:
1. Take each number's mod 37
2. Map it with the following character set:
	* 0-25 = uppercase alphabet
	* 26-35 = decimal digits
	* 36 = underscore
	
I built the following script to decode the message:
```python
#!/usr/bin/env python3
#filename: decrypt.py

import string
# Character set
ALPHABET = list(string.ascii_uppercase + string.digits + "_")

# Set the character set in a dictionary
my_dict = {}
for x in range(37):
	my_dict[x] = ALPHABET[x]

# Decode the message
def decode(n):
	n = int(n) % 37
	n = my_dict[n]
	return n

# Open ciphertext message
ciphertext = tuple(open("message.txt","r").read().strip().split(" "))

# Print flag
flag = "picoCTF{"
flag += "".join(list(map(decode,ciphertext)))
flag += "}"
print(flag)
```

![Solution](https://imgur.com/X5azHah.png)

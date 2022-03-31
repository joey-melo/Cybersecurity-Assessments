# PicoCTF 2022
- Challenge: basic-mod2
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
A new modular challenge!
Download the message [here](https://artifacts.picoctf.net/c/500/message.txt).
Take each number mod 41 and find the modular inverse for the result. Then map to the following character set: 1-26 are the alphabet, 27-36 are the decimal digits, and 37 is an underscore.
Wrap your decrypted message in the picoCTF flag format (i.e. picoCTF{decrypted_message})

## Solution
The challenge is really similar to basic-mod1 and it gives us the following ciphertext:
`104 85 69 354 344 50 149 65 187 420 77 127 385 318 133 72 206 236 206 83 342 206 370`
And the instructions to decode it are slightly different now, as they go from 1 to 37 instead of 0 to 36:
1. Take each number's mod 41
2. Find the modular inverse
3. Map it with the following character set:
	* 1-26 = uppercase alphabet
	* 27-36 = decimal digits
	* 37 = underscore

I built the following script to decode the message:
```python
#!/usr/bin/env python3
#filename: decrypt.py

import string
# Character set
ALPHABET = list(" " + string.ascii_uppercase + string.digits + "_")

# Set the character set in a dictionary
my_dict = {}
my_dict[0] = " "
for x in range(1, 38):
	my_dict[x] = ALPHABET[x]

# Decode the message
def decode(n):
	n = int(n)
	# Calculate the modular inverse in the for loop
	for i in range(42):
		mod = (n%41) * (i%41) % 41
		if mod == 1: 
			n = i
			break
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

![Solution](https://imgur.com/lzb68J1.png)

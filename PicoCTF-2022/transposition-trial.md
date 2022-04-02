# PicoCTF 2022
- Challenge: transposition-trial
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
Our data got corrupted on the way here. Luckily, nothing got replaced, but every block of 3 got scrambled around! The first word seems to be three letters long, maybe you can use that to recover the rest of the message.
Download the corrupted message [here](https://artifacts.picoctf.net/c/457/message.txt).

## Writeup
The message is scrambled every three letters. So we just need to split the message in blocks of three and analyze it.
The flag is scrambled in the following pattern: `2-3-1`. That means `ABC` became `BCA`.
Here is a script to unscramble it.

```python
#!/usr/bin/env python3

msg = open("message.txt","r").read()

for i in range(0, len(msg), 3):
	print(msg[i+2] + msg[i:i+2], end = "")
```

![Flag](https://imgur.com/YDnUhUh.png)

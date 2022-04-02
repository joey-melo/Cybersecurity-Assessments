# PicoCTF 2022
- Challenge: rail-fence
- Category: Cryptography
- Points: 100
- Author: Will Hong

## Description
A type of transposition cipher is the rail fence cipher, which is described [here](https://en.wikipedia.org/wiki/Rail_fence_cipher). Here is one such cipher encrypted using the rail fence with 4 rails. Can you decrypt it?
Download the message [here](https://artifacts.picoctf.net/c/273/message.txt).
Put the decoded message in the picoCTF flag format, picoCTF{decoded_message}.

## Writeup
There are online automatic decryptors out there. This one worked well for me: http://rumkin.com/tools/cipher/railfence.php
However, I tried to make a python script to solve it manually.
...
And failed miserably! Lol
So I found this python script and decided to study it. You can use to play around and find the flag yourself: https://github.com/TimCinel/RailFencePython/blob/master/railFence.py
I adapted the code a little bit to read the file and output the flag.

```python
#!/usr/bin/env python3

def offset(even, rails, rail):
    if rail == 0 or rail == rails - 1:
        return (rails - 1) * 2

    if even:
        return 2 * rail
    else:
        return 2*(rails - 1 - rail)



def decryptRailFence(encrypted, rails, showOff = 0):
    array = [[" " for col in range(len(encrypted))] for row in range(rails)]
    read = 0
    
    #build our fence
    for rail in range(rails):
        pos = offset(1, rails, rail)
        even = 0;
        
        if rail == 0:
            pos = 0
        else:
            pos = int(pos / 2)
        
        while pos < len(encrypted):
            if read == len(encrypted):
                break

            array[rail][pos] = encrypted[read];
            read = read + 1

            pos = pos + offset(even, rails, rail)
            even = not even


    if showOff:
        #hooray, done! show our handy work
        for row in array:
            print row

    #now return the decoded message
    decoded = ""

    for x in range(len(encrypted)):
        for y in range(rails):
            if array[y][x] != " ":
                decoded += array[y][x]

    return decoded
```

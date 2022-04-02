# PicoCTF 2022
- Challenge: Safe opener
- Category: Reverse Engineering
- Points: 100
- Author: Mubarak Mikail

## Description
Can you open this safe?
I forgot the key to my safe but this [program](https://artifacts.picoctf.net/c/463/SafeOpener.java) is supposed to help me with retrieving the lost key. Can you help me unlock my safe?
Put the password you recover into the picoCTF flag format like:
picoCTF{password}

## Writeup
The challenge provide us with a java file that asks for a password. It encodes the user-provided password in base64 and compare it with the string `cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz`

So all we need to do is decode that string
```bash
echo "cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz" | base64 -d
# or to print the formatted flag:
echo -n 'picoCTF{'; echo -n "cGwzYXMzX2wzdF9tM18xbnQwX3RoM19zYWYz" | base64 -d; echo '}'
```

![Flag](https://imgur.com/zWHh2bS.png)

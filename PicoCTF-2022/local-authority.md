# PicoCTF 2022
- Challenge: Local Authority
- Category: Web Exploitation
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Go to this [website](http://saturn.picoctf.net:63115/) and see what you can discover.

## Writeup
The website presents a simple login form.

![Website](https://imgur.com/88o9vZx.png)

We can input any user:pass just to see what happens. So let's try to log in with `admin:admin`.

![Login failed](https://imgur.com/O2KawoM.png)

Let's analyze the source code (Ctrl + U) and see if we find anything interesting.

![Source code](https://imgur.com/0KflR2H.png)

On the highlighted lines 15 and 16, we notice that there is a hidden form. We can post the content of `hash` in the `admin.php` endpoint.
The line 53 shows that the expected value for `hash` is `2196812e91c29df34f5e217cfd639881`.
So let's use `curl` to make that post request and get the flag.

```bash
curl -X POST http://saturn.picoctf.net:63115/admin.php -d "hash=2196812e91c29df34f5e217cfd639881"
```

![Flag](https://imgur.com/u1HHWRs.png)

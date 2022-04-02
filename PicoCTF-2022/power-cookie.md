# PicoCTF 2022
- Challenge: Power Cookie
- Category: Web Exploitation
- Points: 200
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Go to this website and see what you can discover.

## Writeup
The website has a simple cookie authentication mechanism; however, the user is not authenticated as admin.
To bypass that, simply open the developert tools `F12` and change the value of the `isAdmin` cookie to `1` and refresh the page.

![Flag](https://imgur.com/DAgps6H.png)

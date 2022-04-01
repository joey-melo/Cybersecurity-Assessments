# PicoCTF 2022
- Challenge: Includes
- Category: Web Exploitation
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Can you get the flag?
Go to this [website](http://saturn.picoctf.net:59300/) and see what you can discover.

## Writeup
The website presents a simple text and a button that just pops an alert.

![Website](https://imgur.com/wdyrTg0.png)

Analyzing the network section in the developer tab (F12), we notice that the website is including a `style.css` and a `script.js` file. We notice that these files have each a piece of the flag.

![Flag part 1](https://imgur.com/6cbjyEi.png)

![Flag part 2](https://imgur.com/43am0FL.png)

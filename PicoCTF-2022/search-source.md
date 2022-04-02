# PicoCTF 2022
- Challenge: Search source
- Category: Web Exploitation
- Points: 100
- Author: Mubarak Mikail

## Description
The developer of this website mistakenly left an important artifact in the website source, can you find it?
The website is [here](http://saturn.picoctf.net:50761/)

## Writeup
The task is to find the flag somewhere in the source code.
So let's open the developer tools `F12` and go to the `Network` tab and refresh the page.
Then, looking at all files loaded, we can inspect them to find the flag.
The flag is in the `style.css` file as a comment.

![Flag](https://imgur.com/SEdwfuL.png) 

# PicoCTF 2022
- Challenge: file-run2
- Category: Reverse Engineering
- Points: 100
- Author: Will Hong

## Description
Another program, but this time, it seems to want some input. What happens if you try to run it on the command line with input "Hello!"?
Download the program [here](https://artifacts.picoctf.net/c/352/run).

## Solution
This challenge is basically a walkthrough to running a file with arguments in linux.

```bash
wget https://artifacts.picoctf.net/c/309/run 
chmod +x run
./run Hello!
```

![Solution](https://imgur.com/F6FTpO7.png)

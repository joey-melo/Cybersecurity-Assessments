# PicoCTF 2022
- Challenge: Roboto Sans
- Category: Web Exploitation
- Points: 200
- Author: Mubarak Mikail

## Description
The flag is somewhere on this web application not necessarily on the website. Find it.
Check this out.

## Writeup
We are provided a simple website and the flag is somewhere there. However, it's nowhere to be found in the source code.
Judging by the name of the room, `Roboto Sans`, let's take a look at `/robots.txt`.

![Robots.txt](https://imgur.com/zN770Xg.png)

One of the lines seems to be a base64 code: `anMvbXlmaWxlLnR4dA==`. So let's decode it:

```bash
echo 'anMvbXlmaWxlLnR4dA==' | base64 -d         
```

It decodes to `js/myfile.txt`, so let's check that endpoint to see our flag.

![Flag](https://imgur.com/d8xPGvB.png)

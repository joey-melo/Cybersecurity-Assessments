# PicoCTF 2022
- Challenge: Enhance!
- Category: Forensics
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Download this image file and find the flag.
* [Download image file](https://artifacts.picoctf.net/c/137/drawing.flag.svg)

## Solution
The challenge provides a .svg image:

![Image provided](https://imgur.com/39daH8w.png)

We can analyze the readable binary data of this image by running the `strings` command in linux. By doing that, we can verify a pattern after the \<tspan37\*\*\> tag, which seems to be the flag:

```bash
strings drawing.flag.svg
```

![Interesting data](https://imgur.com/M9V1pDd.png)

So I built the following script to extract the information:

```python
#!/usr/bin/env python3
#filename: extract.py

import re

flag = open("drawing.flag.svg", "r").read()

flag= "".join(re.findall(r">(.*)</tspan", flag)).replace(" ","")
print(flag)
```

![Solution](https://imgur.com/LsL2Lnh.png)

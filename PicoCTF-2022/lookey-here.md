# PicoCTF 2022
- Challenge: Lookey here
- Category: Forensics
- Points: 100
- Author: Lt 'Syreal' Jones / Mubarak Mikail

## Description
Attackers have hidden information in a very large mass of data in the past, maybe they are still doing it.
Download the data [here](https://artifacts.picoctf.net/c/295/anthem.flag.txt).

## Writeup
The file is a really big text (2146 lines). The flag is hidden somewhere in the middle of it.

![Word count of file](https://imgur.com/ZZ1rF0d.png)

Since we know that the flags start with "picoCTF", we can simply `grep` for it.

```bash
grep picoCTF anthem.flag.txt
```

![Flag](https://imgur.com/SpL90lA.png)

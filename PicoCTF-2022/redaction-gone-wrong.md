# PicoCTF 2022
- Challenge: Redaction gone wrong
- Category: Forensics
- Points: 100
- Author: Mubarak Mikail

## Description
Now you DON’T see me.
This [report](https://artifacts.picoctf.net/c/264/Financial_Report_for_ABC_Labs.pdf) has some critical data in it, some of which have been redacted correctly, while some were not. Can you find an important key that was not redacted properly?

## Writeup
The challenge provides us with a redacted pdf document.

![Redacted pdf](https://imgur.com/DTiOIMW.png)

If you take a close look, you actually select the text in some of those black boxes.
So just `CTRL + A` the file, `CTRL + C` and `CTRL + V` somewhere to get the unredacted content of the file.

```
Financial Report for ABC Labs, Kigali, Rwanda for the year 2021.
Breakdown - Just painted over in MS word.
Cost Benefit Analysis
Credit Debit
This is not the flag, keep looking
Expenses from the
picoCTF{C4n_Y0u_S33_m3_fully}
Redacted document.
```

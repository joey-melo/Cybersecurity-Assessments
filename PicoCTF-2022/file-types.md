# PicoCTF 2022
- Challenge: File types
- Category: Forensics
- Points: 100
- Author: Geoffrey Njogu

## Description
This file was found among some files marked confidential but my pdf reader cannot read it, maybe yours can.
You can download the file from [here](https://artifacts.picoctf.net/c/324/Flag.pdf).

## Solution
The file provided is not a pdf, but actually a shell script.

```bash
wget https://artifacts.picoctf.net/c/324/Flag.pdf
file Flag.pdf
cat Flag.pdf
```

![Analyzing Flag.pdf](https://imgur.com/zPk5qDj.png)

However, the script does not run because we don't have `uudecoded` installed in the machine.

```bash
chmod +x Flag.pdf
./Flag.pdf
```

![Script does not run](https://imgur.com/bKCl7H1.png)

To fix it, we install `sharutils`, which has the `uudecoded` binary in its package.

```bash
sudo apt install sharutils
./Flag.pdf
```

![Flag extrated](https://imgur.com/7MZi5un.png)

However, the flag is still a bunch of bytes. Analyzing it, we verify that it is an "ar" file.
Well, at the end of the day, this flag is almost a zip bomb. I'll list all the commands to extract the file. If you want to do this manually, run the `file` command every time you extract the flag.

```bash
./Flag.pdf
ar -x flag
mv flag flag.cpio
cpio -i < flag.cpio
bunzip2 flag
mv flag.out flag.gz
gunzip flag.gz
sudo apt install lunzip
lunzip flag
sudo apt install lz4
unlz4 flag.out flag.lzma
lzma -d flag.lzma
sudo apt install lzop
mv flag flag.lzop
lzop -d flag.lzop
rm flag.out
lunzip flag
mv flag.out flag.xz
unxz flag.xz
cat flag # Finally
```

Then you can try [Cyberchef](https://gchq.github.io/CyberChef/) to decipher the hexcode and get the flag.

![Solution](https://imgur.com/3ZBNVtF.png)

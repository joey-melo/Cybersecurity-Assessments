# PicoCTF 2022
- Challenge: Packets Primer
- Category: Forensics
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Download the packet capture file and use packet analysis software to find the flag.
[Download packet capture](https://artifacts.picoctf.net/c/200/network-dump.flag.pcap)

## Writeup
After downloading the packet, you can run wireshark to visualize it. The flag is in frame 4.

![Flag in packet](https://imgur.com/HMJ2FfP.png)

However, if you want the get the plaintext to easily copy and paste it, run the following command:

```bash
tshark -r network-dump.flag.pcap -Y frame.number==4 -T fields -e data.text -o data.show_as_text:TRUE | tr -d " \\\\n"
# tshark = commandline version of wireshark
# -r = read file
# -Y = filter (we want only frame number 4)
# -T = read fields (specified with -e)
# -e = specify which field to read (we want only the asci/text portion, not the hex bytes)
# -o = override settings (standard settings do now allow to show text data)

# tr = "translate" tool from linux, we use it to delete the extra "\n" that comes with the text
# -d = delete these characters
```

![Solution](https://imgur.com/GME72p3.png)

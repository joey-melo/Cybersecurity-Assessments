# PicoCTF 2022
- Challenge: morse-code
- Category: Cryptography
- Points: 100
- Author: Lt 'Syreal' Jones

## Description
Morse code is well known. Can you decrypt this?
Download the file [here](https://artifacts.picoctf.net/c/235/morse_chal.wav).
Wrap your answer with picoCTF{}, put underscores in place of pauses, and use all lowercase.

## Writeup
To visualize this file, you can download audacity and run it. Then you'll be able to decipher it manually.

```bash
sudo apt install audacity
audacity morse_chall.wav
```

![Audacity](https://imgur.com/OjDAFv0.png)

But you can also automate the process by uploading the file to https://morsecode.world/international/decoder/audio-decoder-adaptive.html

![Flag](https://imgur.com/im7mUaD.png)

Just remember to put underscore in place of spaces and wrap it in "picoCTF{}".

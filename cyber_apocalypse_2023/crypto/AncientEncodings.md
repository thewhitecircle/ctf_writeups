---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Ancient Encodings Writeup
desc: Check out our writeup for Ancient Encodings for Cyber Apocalypse 2023 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: crypto
challenge: Ancient Encodings
tags: "crypto, legend, python, encryption"
date: 2023-03-27T00:00:00+00:00
last_modified_at: 2023-03-27T00:00:00+00:00
---


> Solved by Legend

Challenge description


> Your initialization sequence requires loading various programs to gain the necessary knowledge and skills for your journey. Your first task is to learn the ancient encodings used by the aliens in their communication.

In this challenge we were provided a `python` script named `source.py` and `output.txt` file containing the encrypted flag.

```python
from Crypto.Util.number import bytes_to_long
from base64 import b64encode

FLAG = b"HTB{??????????}"


def encode(message):
    return hex(bytes_to_long(b64encode(message)))


def main():
    encoded_flag = encode(FLAG)
    with open("output.txt", "w") as f:
        f.write(encoded_flag)


if __name__ == "__main__":
    main()
```

```
0x53465243657a467558336b7764584a66616a4231636d347a655639354d48566664326b786246397a5a544e66644767784e56396c626d4d775a4446755a334e665a58597a636e6c33614756794d33303d
```

The script is encoding the flag in `hex --> base64` to encrypt the flag.

To solve this I used CyberChef with following steps

![](https://i.imgur.com/xJzvIcV.png)



---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Enchanted Cipher Writeup
desc: Check out our writeup for Enchanted Cipher for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: coding
challenge: Enchanted Cipher
tags: "coding, ava, twh, python, shift, cipher"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---


> Solved by avantika, thewhiteh4t


- Each entry has been encoded with a shifting cipher that changes every N characters

```
import ast
import string

inputStr = input()
groups = int(input())
shifts = list(ast.literal_eval(input()))

alphabets = list(string.ascii_lowercase)

space_ind = []
i = 0

for letter in inputStr:
    i += 1
    if letter == ' ':
        space_ind.append(i)

string = inputStr.replace(" ", "")

groups = [string[i:i + 5] for i in range(0, len(string), 5)]

flag = []
for shift, word in zip(shifts, groups):
    output = ''
    for char in word:
        char_index = alphabets.index(char) + 1
        new_char_ind = char_index - shift
        if new_char_ind < 0:
            new_char_ind = 26 + new_char_ind
        new_char = alphabets[new_char_ind - 1]
        output += new_char
    flag.append(output)

flag_pre = "".join(flag)

j = 0
final_flag = ''

for space in space_ind:
    for flag_char in flag_pre:
        j += 1
        if j == space:
            x = j - 1
            flag_pre = flag_pre[:x] + ' ' + flag_pre[x:]
            final_flag = flag_pre
            break

print(final_flag)
```   


![](https://i.imgur.com/oy8ZeFl.png)


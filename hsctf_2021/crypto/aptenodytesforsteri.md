---
layout: load_md
title: The White Circle | Hsctf 2021 | aptenodytes-forsteri Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: crypto
challenge: aptenodytes-forsteri
tags: "crypto, taz, python"
date: 2021-06-20T00:00:00+00:00
last_modified_at: 2021-06-20T00:00:00+00:00
---

<h1 class="heading card-title white-text">Hsctf 2021</h1>

## aptenodytes-forsteri
> Solved by: Taz34

We are given 2 file: 
First is aptenodytes-forsteri.py

```python
flag = open('flag.txt','r').read() #open the flag
    
assert flag[0:5]=="flag{" and flag[-1]=="}" #flag follows standard flag format
    
letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    
encoded = ""
    
for character in flag[5:-1]:
    encoded+=letters[(letters.index(character)+18)%26] #encode each character
print(encoded)
```

second is output.txt

```
IOWJLQMAGH
```

I made a flag.txt file as the script takes in flag.txt file.
According to the script the script only takes in the characters between the curly brackets of the flag format i.e flag{}
And it also allows only capital letters.

So i made a flag.txt file with all charaters A-Z in the flag format.

```
flag{ABCDEFGHIJKLMNOPQRSTUVWXYZ}
```

![](https://i.imgur.com/1Aa0Lre.png)


Now i got the perspective encoded values of each letter so now I can compare it with the output.txt string.

```
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
S T U V W X Y Z A B C D E F G H I J K L M N O P Q R
    
I O W J L Q M A G H
Q W E R T Y U I O P
```

We can verify it by passing the flag we have in the flag.txt file and see if it matches with output.txt

![](https://i.imgur.com/EB638Lz.png)

It matches hence we have the right flag.

```
flag{QWERTYUIOP}
```


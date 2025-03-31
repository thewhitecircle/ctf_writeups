---
layout: load_md
title: The White Circle | Gpnctf 2024 | Never gonna run around and reverse you Writeup
desc: Check out our writeup for Never gonna run around and reverse you for Gpnctf 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Gpnctf 2024
parent: gpnctf_2024
category: re
challenge: Never gonna run around and reverse you
tags: "re, ava, elf, xor, python"
date: 2024-06-02T00:00:00+00:00
last_modified_at: 2024-06-02T00:00:00+00:00
---


> Solved by : iamavu

We are given a ELF file called `hasher` and text file `hash.txt` .

![](https://i.imgur.com/fwEXssL.png)


The `hasher` file takes an argument and outputs a hash of whatever you have entered, for example

![](https://i.imgur.com/Rnk4Yc5.png)


So, it’s pretty clear that we have to somehow “reverse” the hash function. Let’s fire up IDA and see the de-compilation.

```c
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  int i; // \[rsp+18h\] [rbp-18h]
  int inputLen; // \[rsp+1Ch\] [rbp-14h]
  char *input; // \[rsp+20h\] [rbp-10h]
  char *v7; // \[rsp+28h\] [rbp-8h]

// see if we get an argument else exit
  if ( a1 <= 1 )
  {
    printf("Please provide a flag as an argument");
    exit(1);
  }
  input = a2[1];
  inputLen = strlen(input);

//allocate memory + 2 according to input length
  v7 = (char *)malloc(inputLen + 2);

//copy the input starting from second character
  strcpy(v7 + 1, input);

//loop through each character while XORing with previous character
  for ( i = 1; inputLen >= i; ++i )
  {
    v7[i] ^= v7[i - 1];
    printf("%02x", (unsigned int)v7[i]);
  }
  putchar(10);
  return 0LL;
}
```

I have added comments on what major part of code does but the key part where I spent decent amount of time was, what if there is only one character, what would be the previous character be? And what about allocating two extra places while doing `malloc`, I  had to assume that it would be zero, though it could be any garbage value but I guess that’s what de-compilation told me.

So the solution python code is as follows.

```python
hash = '4717591a4e08732410215579264e7e0956320367384171045b28187402316e1a7243300f501946325a6a1f7810643b0a7e21566257083c63043404603f5763563e43'
n = 2
splittedHash = [hash[i:i+n] for i in range(0, len(hash), n)]

#insert 0 in the beginngin and at the last index so as to make the first character XORINg possible
splittedHash.insert(0, str(0))
splittedHash.append(str(0))

#loop through the hash list to make it hex
for i in range(len(splittedHash)):
    splittedHash[i] = hex(int(splittedHash[i], 16))

#loop through it again and perform the actual XOR operation
i = 1
flagList = [0]*len(splittedHash)
while len(splittedHash) >= i + 1:
    
    flagList[i] =  hex(int(splittedHash[i], 16) ^ int(splittedHash[i - 1], 16))
    i = i + 1

#pop the last and first character which we added initially and print the flag
flagList = flagList[1:-1]
flag = ''.join(chr(int(x, 16)) for x in flagList)
print(flag)
```


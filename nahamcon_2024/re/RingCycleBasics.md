---
layout: load_md
title: The White Circle | Nahamcon 2024 | Ring Cycle Basics Writeup
desc: Check out our writeup for Ring Cycle Basics for Nahamcon 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: re
challenge: Ring Cycle Basics
tags: "re, ava, c, python"
date: 2024-05-28T00:00:00+00:00
last_modified_at: 2024-05-28T00:00:00+00:00
---


> Solved by : iamavu

we are given two files as follows

![](https://i.imgur.com/5N7pcPw.png)


and the content of `basics.txt` includes following cool ascii art

![](https://i.imgur.com/rQJjzki.png)


and decompiling the `basics` binary in IDA gives us the following pseudo-C code

```c
int __fastcall main(int argc, const char **argv, const char **envp)
{
// define variables
  size_t v4; // rsi
  int i; // \[rsp+4h\] [rbp-6Ch]
  FILE *stream; // \[rsp+8h\] [rbp-68h]
  __int64 nmemb; // \[rsp+10h\] [rbp-60h]
  void *ptr; // \[rsp+18h\] [rbp-58h]
  char v9\[16]; // [rsp+20h\] [rbp-50h] BYREF
  char s\[56]; // [rsp+30h\] [rbp-40h] BYREF
  unsigned __int64 v11; // \[rsp+68h\] [rbp-8h]

  v11 = __readfsqword(0x28u);
  init();
//gets input from user and pass that to function called "check"
  printf("What is the passphrase of the vault?\n> ");
  fgets(s, 50, stdin);
  if ( check((__int64)s) )
//if check returns TRUE then do the magic and print the flag else "wrong passphrase"
  {
    stream = fopen("basics.txt", "r");
    if ( !stream )
      return -1;
    fseek(stream, 0LL, 2);
    nmemb = ftell(stream);
    fseek(stream, 0LL, 0);
    ptr = calloc(nmemb, 1uLL);
    if ( !ptr )
      return -1;
    fread(ptr, 1uLL, nmemb, stream);
    fclose(stream);
    printf((const char *)ptr);
    v4 = strlen(s);
    MD5(s, v4, v9);
    printf("flag{");
    for ( i = 0; i <= 15; ++i )
      printf("%02x", (unsigned __int8)v9[i]);
    puts("}");
  }
  else
  {
    puts("Wrong passphrase!");
  }
  return 0;
}
```

i have added comments explaining what the `main` function does, now let’s have a look at the `check` function which we have to understand

```c
_BOOL8 __fastcall check(__int64 input)
{
  char v2; // \[rsp+11h\] [rbp-8Fh]
  char v3; // \[rsp+12h\] [rbp-8Eh]
  char v4; // \[rsp+13h\] [rbp-8Dh]
  int i; // \[rsp+14h\] [rbp-8Ch]
  int j; // \[rsp+18h\] [rbp-88h]
  int k; // \[rsp+1Ch\] [rbp-84h]
  char s1\[64]; // [rsp+20h\] [rbp-80h] BYREF
  char s2\[56]; // [rsp+60h\] [rbp-40h] BYREF
  unsigned __int64 v10; // \[rsp+98h\] [rbp-8h]

  v10 = __readfsqword(0x28u);
  for ( i = 0; i <= 24; ++i )
  {
    v4 = *(_BYTE *)(i + input);
    s1[i] = *(_BYTE *)(50 - i - 1LL + input);
    s1[49 - i] = v4;
  }
  for ( j = 0; j <= 49; ++j )
  {
    v3 = s1[j];
    s1[j] = s1[j + 1];
    s1[j + 1] = v3;
  }
  s1[49] = 0;
  for ( k = 0; k <= 47; k += 2 )
  {
    v2 = s1[k];
    s1[k] = s1[k + 1];
    s1[k + 1] = v2;
  }
  strcpy(s2, "eyrnou jngkiaccre af suryot arsto  tdyea rre aouY");
  return strcmp(s1, s2) == 0;
}
```

this looks like an series of operation being performed on our input string
it first shifts first 24 characters to the last and brings the last one to first, then it puts the first character to the last (of which i don’t have understanding why) and finally it shifts alternate characters together bringing us to our final flag string
i have written a python script to solve it, thought it might be little inaccurate as i wasn’t able to figure out why exactly the second operation brings the `Y` character to the last, hence the flag string is little molded but nonetheless 

```python
flag = [0]*50
input = list("\x00eyrnou jngkiaccre af suryot arsto  tdyea rre aouY")

i = 0
while i < 25:
    tmp = input[i]
    flag[i] = input[50 - 1 - i]
    flag[49 - i] = tmp
    i = i + 1
print(''.join(flag))


for i in range(49):
    tmp = flag[i]
    flag[i] = flag[i + 1]
    flag[i + 1] = tmp
print(''.join(flag))


for i in range(0, 48, 2):
    tmp = flag[i]
    flag[i] = flag[i + 1]
    flag[i + 1] = tmp
print(''.join(flag))
```

the solve script is essentially doing whatever the original operation but performing it again and we get our flag string

![](https://i.imgur.com/GP0QnOn.png)


as i mentioned, the flag string is little molded so we need to put the `Y` character in front
and then we supply the flag string to the binary to get back our actual flag

![](https://i.imgur.com/I5ywHyg.png)



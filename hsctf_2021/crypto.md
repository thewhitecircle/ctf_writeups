# crypto

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

---

## opisthocomus-hoazin
> Solved by: Taz34

First opisthocomus-hoazin.py

```python
import time
from Crypto.Util.number import *
flag = open('flag.txt','r').read()
p = getPrime(1024)
q = getPrime(1024)
e = 2**16+1
n=p*q
ct=[]
for ch in flag:
    ct.append((ord(ch)^e)%n)
print(n)
print(e)
print(ct)
```

2nd is output.txt

```
15888457769674642859708800597310299725338251830976423740469342107745469667544014118426981955901595652146093596535042454720088489883832573612094938281276141337632202496209218136026441342435018861975571842724577501821204305185018320446993699281538507826943542962060000957702417455609633977888711896513101590291125131953317446916178315755142103529251195112400643488422928729091341969985567240235775120515891920824933965514217511971572242643456664322913133669621953247121022723513660621629349743664178128863766441389213302642916070154272811871674136669061719947615578346412919910075334517952880722801011983182804339339643
65537
[65639, 65645, 65632, 65638, 65658, 65653, 65609, 65584, 65650, 65630, 65640, 65634, 65586, 65630, 65634, 65651, 65586, 65589, 65644, 65630, 65640, 65588, 65630, 65618, 65646, 65630, 65607, 65651, 65646, 65627, 65586, 65647, 65630, 65640, 65571, 65612, 65630, 65649, 65651, 65586, 65653, 65621, 65656, 65630, 65618, 65652, 65651, 65636, 65630, 65640, 65621, 65574, 65650, 65630, 65589, 65634, 65653, 65652, 65632, 65584, 65645, 65656, 65630, 65635, 65586, 65647, 65605, 65640, 65647, 65606, 65630, 65644, 65624, 65630, 65588, 65649, 65585, 65614, 65647, 65660]
```

We can see the each vale in ct list is actually the n modulus(%) of the XOR (^) value of the ASCII values of the flag’s characters with e.

We can verify it here, by encoding first letter of the flag i.e. “f”  and this is also the first letter in the ct list:

![](https://i.imgur.com/RNRzeqQ.png)


Now I have written a small script which encode out all the characters that can be used in the flag:

```python
import string
chr_set = string.ascii_letters + string.digits + string.punctuation
e = 65537
n = 15888457769674642859708800597310299725338251830976423740469342107745469667544014118426981955901595652146093596535042454720088489883832573612094938281276141337632202496209218136026441342435018861975571842724577501821204305185018320446993699281538507826943542962060000957702417455609633977888711896513101590291125131953317446916178315755142103529251195112400643488422928729091341969985567240235775120515891920824933965514217511971572242643456664322913133669621953247121022723513660621629349743664178128863766441389213302642916070154272811871674136669061719947615578346412919910075334517952880722801011983182804339339643
for i in range(len(chr_set)):
    x = chr_set[i] + ": " + str((ord(chr_set[i])^e)%n)
    print(x)
```

I also made a lookup.txt dictionary with all the elements of the ct list
Now i used grep to get the elements we want form the script output:

![](https://i.imgur.com/T15GvI8.png)

Now i used Sublime text to replace all the values with there specific letters to get the flag

```
flag{tH1s_ic3_cr34m_i5_So_FroZ3n_i"M_pr3tTy_Sure_iT's_4ctua1ly_b3nDinG_mY_5p0On}
```

# **Queen of the hill**
> Solved By : Starry-Lord

![](https://i.imgur.com/pPTp4fp.jpg)

A bit of research took me to hill cipher. 

![](https://i.imgur.com/gyrkabO.jpg)

```
flag{climb_your_way_to_the_top}
```
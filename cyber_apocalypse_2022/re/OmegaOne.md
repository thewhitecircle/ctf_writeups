---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Omega One Writeup
desc: Check out our writeup for Omega One for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: re
challenge: Omega One
tags: "re, taz, cutter"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved by: Taz34

We are given a 64bit binary and an output file.
In the output file we have a list:

![](https://i.imgur.com/gPWqsRc.png)

Now to understand the working of the binary better, I fired-up Cutter
https://cutter.re/
So in the main function we found a list, in which every character has been assigned a name similar to which are in the output.txt file

![](https://i.imgur.com/eKD8StZ.png)

Hence we have to just replace each name in the output.txt with the letters assigned to them in the list shown above.
I have trimmed out the file to make it easy

```
k: Lendrens
d: Thauv\'i
P: Throrqiek
e: Inqods
6: Tarquts
p: Dut
A: Krolkel
n: Emoi
|: Dakroith
*: Creiqex
Y: Thomois
4: Groz\'ens
D: Urqek
v: Nid
H: Crerceon
#: Yonphie
S: Xits
I: Thohul
W: Zahrull
i: Om\'ons
F: Kradraks
+: Ielkul
q: Vranix
M: Trun
h: Craz\'ails
.: Xoq\'an
r: Ukox
N: Evods
;: Taxan
b: Munis
g: Trurkror
?: Tulphaer
_: Ehnu
$: Krets
:: Grons
): Ingell
(: Ecruns
m: Khehlan
R: Velzaeth
Q: Cuhix
l: Vinzo
E: Istrur
>: Zuvas
s: Honzor
0: Ukteils
}: Baadix
{: Zonnu
\\: Aarcets
[: Nevell
!: Dhohmu
X: Xan
O: Zissat
x: Iscax
t: Pheilons
`: Ghiso
-: Scrigvil
B: Ummuh
u: Inphas
/: Vurqails
a: Vruziels
:: Ghut\'ox
^: Aahroill
L: Gairqeik
U: Qeks
\': Scuvvils
3: Ohols
5: Som\'ir
C: Onzear
2: Dhaesux
w: Falnain
 : Draalpho
G: Yemor
c: Thraurgok
\: Vogeath
1: Cuzads
Z: Gagro
=: Zad
f: Dhieqe
&: Xustrek
o: Harned
V: Dhulgea
y: Zimil
z: Thretex
8: Bravon
%: Krugreall
J: Vaendred
@: Osux
T: Ezains
K: Mik\'ed
<: Cruz\'oll
]: Dhognot
7: Drids
9: Drercieks
j: Statars
```

We can use the grep command to cut out only the strings we need

![](https://i.imgur.com/ct3MXGa.png)

now simply replace these values with the respective letters and we have the flag.


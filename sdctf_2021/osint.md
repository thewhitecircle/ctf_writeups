# OSINT

## Speed-Studying
> Solved By : Taz

* searched: UCSD faculty CSE ( https://cse.ucsd.edu/faculty-research )
* UCSD faculty mathematics ( https://www.math.ucsd.edu/people/faculty/ )
* in the CSE got into Algorithm, cryptography etc as it’s most linked to math and got
: https://cstheory.ucsd.edu/faculty.html

* now looked for the common name in both and came across this profile: 
https://www.math.ucsd.edu/people/profiles/daniel-kane/

* Description here confirmed our target.

**FLAG**: `Daniel Kane`

---

## hIDe and seek
> Solved By : Taz

* we are given two locations :
```
First : ?v=hqXOIZtRYZU
```
* it looks like part of a youtube link 
* link: https://www.youtube.com/watch?v=hqXOIZtRYZU
* here he dictates the 1st part of the flag.
```
 Second : qFHIm0c.jpeg
```
* it’s an image so I first looked into the most used online image sharing platform **imgur**
* Link: https://i.imgur.com/qFHIm0c.jpeg
* here we have the 2nd part of the flag.
```
sdctf{W0w_1_h4D_n0_ID3a!}
```

---

## hIDe and seek 2
> Solved By : Ava and nigamelastic

* I've gotten some more good intel. Apparently, the following information is the location of another flag!

```
First piece of info : gg/4KcDWnUYMs

Second piece of info : 810237829564727312-810359639975526490
```

* the first piece is obviously discord:
* on opening the channel we see

![](https://paper-attachments.dropbox.com/s_E1CDE00EEF36A360B08C6F7FBF981F7819B27F689743D6B8214DB48BA518E43A_1620452658182_image.png)

```
sdctf{m@st3R_h@Ck3R_9999}
```

* which seems to be flag format, I am guessing that starts with 1 till 9999.

**Rabbit Hole**

The second part is obviously a unix time stamp: 

```
810237829564727312 is Mon 4 September 1995 18:03:49.564 UTC

and

810359639975526490 is Wed 6 September 1995 03:53:59.975 UTC
```

* my guess was to check the chats between the times mentioned, but they aren't matching

* `sdctf{m@st3R_h@Ck3R_1995}` --> randomly tried its incorrect

* however thanks to Ava we found it was a rabbit hole! below is the correct way to solve it.

* The numbers actually specify the discord links
* usually the discord message links are something like this :
https://discord.com/channels/675369276403744776/782835136654737429/840472812690210826

* there are three numbers, i am just taking guess :
    * first number is for server
    * second seems for the channel 
    * third seems for message

* we copy link for any message from the server we found and replace the last two numbers with the one given to us in the challenge
* https://discord.com/channels/810237829564727308/810237829564727312/810359639975526490
* boom, we get the right message

```
sdctf{m@st3R_h@Ck3R_4807}
```

---

## This flag has been stolen:
> Solved By : nigamelastic

* the flag is here : https://web.archive.org/web/20210130094941/https://sdc.tf/

---

## Speed-Studying 2 :
> Solved By : Ava

* Simply googling for `skyline problem ucsd`
* we get this link for PDF :
https://cseweb.ucsd.edu/~dakane/CSE101%20Problem%20Archive/F18/Homework3.pdf

* at the bottom we have our flag

```
sdctf{N1ce_d0rKiNG_C@pt41N}
```
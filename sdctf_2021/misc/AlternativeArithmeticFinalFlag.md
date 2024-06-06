---
layout: load_md
title: The White Circle | Sdctf 2021 | Alternative Arithmetic (Final Flag) Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: misc
challenge: Alternative Arithmetic (Final Flag)
tags: "misc, nigamelastic, choco, java"
date: 2021-05-10T00:00:00+00:00
last_update: 2021-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## Alternative Arithmetic (Final Flag)
> Solved By : nigamelastic and choco

* I will use the following to run [this](https://www.onlinegdb.com/online_java_compiler) java compiler to run my code online.

* on connecting we see the first question which is:

```
4th question :

Enter 3 `String` values `s1`, `s2`, and `s3` such that:
```
```java
new BigDecimal(s1).add(new BigDecimal(s2)).compareTo(new BigDecimal(s3)) == 0
```
but
```java
Double.parseDouble(s1) + Double.parseDouble(s2) != Double.parseDouble(s3)
```
```
Do not enter quotation marks.
```

* answer 4 worked but very stupidly: i will paste my inputs and outputs and you just see :

**First attempt**
```bash
Do not enter quotation marks.
s1 = 0.004
s2 = -0.003
s3 = 0.001
Sorry, your answer is not correct. Try something else next time.
```
**Second attempt**
```bash
s1 = 20000000000000000000000000000000000000000000000.0000000000000000000000000000000000000000004
s2 = -10000000000000000000000000000000000000000000000.0000000000000000000000000000000000000000003
s3 = 10000000000000000000000000000000000000000000000.0000000000000000000000000000000000000000001
Sorry, your answer is not correct. Try something else next time.
```

**Third attempt**
```bash
s1 = 0.04
s2 = -0.03
s3 = 0.01
Perfect! You are correct.
```

* credits to choco, who guided me, and after our discussion we realised for some reason `0.04` is working but `0.004` isn’t
choco also pointed out this amazing link https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html

* finally we discussed and realized the following :

* according to double :
```bash
0.003 will be represented as 3.00000000000000006245004513517E-3
0.004 will be represented as 4.00000000000000008326672684689E-3
0.02 will be represented as 2.00000000000000004163336342344E-2

but 

0.03 will be represented 2.99999999999999988897769753748E-2 
```

* after this stupidity we finally get the 5th and final option:

```
5. Final question! [🎶BOSS MUSIC🎶]

Fill in <type>, <num1>, <num2> below:
```
```java
var i = (<type>) <num1>; var j = (<type>) <num2>;
```
```
such that after running the code above, the following expression:
```
```java
i < j || i == j || i > j
```
```
evaluates to `false`.

<num1> and <num2> are Java code that satisfies this regex: [0-9]*\.?[0-9]*
```

* which is very easy , I remember it from my college days
* its `Integer 128`
* this is due to the integer cache limits
* the following link would be a better explanation
* https://www.programmersought.com/article/91462031238/



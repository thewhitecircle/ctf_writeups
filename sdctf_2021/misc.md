# misc

## **No flag for you**
> Solved By : Taz

* We found out that `ls`, `cat`, `echo` commands were available.
* So started looking around for these.
* Found a way to list dir using echo:

```
echo /*
```

![](https://i.imgur.com/nzyMsSw.png)

* started looking around for the flag and found it in the `/home/user/run/opt` dir

```
echo /home/user/run/opt/*
```

* Did some research on ways to read a file using echo, and got something.
* Reference link: https://stackoverflow.com/questions/22377792/how-to-use-echo-command-to-print-out-content-of-a-text-file

```
echo "$(</home/user/run/opt/flag-b01d7291b94feefa35e6.txt)"
```

![](https://i.imgur.com/pPB49gk.png)

---

## Alternative Arithmetic 
> Solved By : Ava and choco

* After connection we are given a java questions which we have to research for and solve

```
Question 1 :

1. Find a nonzero long x` such that `x == -x`, enter numbers only, no semicolons
```

* answer for 1st question is `-9223372036854775808`
* Explanation :-
> The first question given is to find long x such that `x == -x` Here we
> will need to exploit the use of 2's compliment of binary
> representation of long. example: in a 4 bit representation of 1, the
> binary is `0001` but to represent -1, the binary will take 2's
> compliment so it will be `1111` The MSB here represents the sign of
> the binary the limitation of 2's compliment is that, the number that
> has reached the minimum negative limit (i.e. the maximum 2's
> compliment value), it's positive counter part will be the same binary
> value as it's two's compliment. Let's take the one for 4 bit
> representation again, the minimum value in this will be -8, i.e,
> `1000` if you take +8 here, the binary will also be `1000` So we got
> the concept for the answer. But now, the data type is long (8 bytes)
> The answer must be `-9223372036854775808`


```
Question 2 :

Find 2 different `long` variables `x` and `y`, differing by at most 10, such that `Long.hashCode(x) == Long.hashCode(y)`
```

* answer for 2nd question is `x = -1 & y = 0`
* Explanation :-
> For the second one the given algorithm for Long.hashCode() is
> 
>     public static int hashCode(long value) {    
>         return (int)(value ^ (value >>> 32));    
>     }
> 
> As we see here, the hash code for long compresses the value to an int
> from 64 bits to 32 bits. The hash values of positive long values are
> itself (hashcode of 8 is 8)  The exploitation here is that negative
> numbers behaves different. let's take -12. it's binary representation
> is
> 
>     1111111111111111111111111111111111111111111111111111111111110100
> 
> after it got shifted to the right by 32 bits it will be
> 
>     0000000000000000000000000000000011111111111111111111111111111111
> 
> this value is xor with the original value giving
> 
>     1111111111111111111111111111111100000000000000000000000000001011
> 
> when we convert it to int only the first 32 from LSBs are taken i.e.,
> 
>     00000000000000000000000000001011
> 
> Hence, the Hash value of -12 will be 11 So 
> 
>     Long.hashCode(i) = i for i >=0 and 
>                          (-i)-1 for i < 0
> 
> but the question is asking for x and y where `x - y <=10`
> 
> Hence using the advance stages of arithmetic mathematics, we can take
> answers as
> 
>     {0,-1},{1,-2},{2,-3},{3,-4} and {4,-5} 
> 
> Courtesy of
> <https://www.devdiaries.net/blog/Java-Recruitment-Questions-HashCode/>for
> Hashcode algorithm.

```
Question 3 - 

Enter a `float` value `f` that makes the following function return true
```

```java
// code given along with Q3 :

boolean isLucky(float magic) {
    int iter = 0;
    for (float start = magic; start < (magic + 256); start++) {
        if ((iter++) > 2048) {
            return true;
        }
    }
    return false;
```

* answer for 3rd Question is `2.14E9`
* Explanation:-
> The third question is a little tricky We need to find float value
> magic such that we need to break the loop The other catch is that it
> have to be less than 7 characters
> 
> The trick here is that we need to exploit the precision error in java
> 
> in java, the floating points can go upto `3.4 * 10^34` but the secret
> is that it will take a precision of 8 digits. i.e., if the value in
> decimal points is more than 8 digits. It will round them off to 8
> digits itself. so for example '345678' in float will be 345678.0 but
> if digits has reached 8 like '`12345678`', float will save the value
> as `1.2345678E7` The maximum digits in float is actually 10
> (1999999999) if you want to represent a number higher than that, you
> must make it in `2.45E10` like format
> 
> now, for the exploit, if you add any number that is negligibly small
> to a float value, this will not affect the float value at all. In
> fact, float values can only consider changes made in the first 8 most
> significant digits. In other words, if converted in decimal, only the
> first 7 digits will be considered, rest all are static Rather, it is
> interesting to note that the remaining digits will face binary
> accuracy error (when you convert some numbers to IEEE754 and back to
> the number, it won't be the same. This will be addressed in the next
> question)
> 
> example for `123456789 + 1` it will be written as `(1.23456792 +
> 0.00000001)*10^8` you see that one is negligent when converted to this form hence the answer will be `1.23456792*10^8` (binary rep error in
> last digits)
> 
> now if we take `123456789 + 100` it will be written as `(1.23456792 +
> 0.00000100)*10^8`  you see that 100 falls under the 8 digit range hence the answer will be `1.23456892*10^8` We got the idea about the
> exploit. but keep in mind, we need to select a number such that
> incrementing it will not affect the value but adding 256 to it should
> So we can discard numbers that are 7 digits or less since incrementing
> it affects the value Also we can discard numbers that are 10 digits or
> above since adding 256 won't affect the value
> 
> Hence, we can only take numbers are that 8 and 9 digits long.
> 
> The question says, we can only use 7 characters or less.
> 
> We can simply represent the float value as `1.0E8` too
> 
> hence, the answer is within the range of `1E8 to 9.999E9`
---

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



# mobile

## To do
> Solved by: Starry-lord

De-compile the app

I personally used 
https://www.decompiler.com/jar/62e31f7faaf148b1b1c4fd143e5480c1/todo.apk/sources/com/congon4tor/todo/LoginActivity.java


![](https://i.imgur.com/c0mTJ5A.jpg)


Find password "testtest"

Login to the app and find to do list along with the flag


![](https://i.imgur.com/of0pbsl.jpg)

---

## Reactor
> Solved by: Starry-Lord


Flag gets more and more unscrambled with correct digits. 
Basically 4 digit Pin probabilities plus dynamic deobfuscating made the total of possibilities go down to less than 40, like an Eval situation, where you would have result if your first characters are correct. 

A. Input 1 digit, 0 to 5, (5)
B. Input second digit 0-9(9)
C. Input third digit 0-2(2)
D. Input fourth digit 0-7(flag!)

27 tries on /40

I agree it's most likely not the intended way but 4 digits pin plus Eval like function is vulnerable enough 😉

![](https://i.imgur.com/zkFlMup.jpg)


5 was the only one starting with letter f, four characters and a promising { like in other flags. 

![](https://i.imgur.com/k1LEdFA.jpg)

![](https://i.imgur.com/i0ZIN3Z.jpg)

![](https://i.imgur.com/dVezqS5.jpg)

![](https://i.imgur.com/odN6hY0.jpg)



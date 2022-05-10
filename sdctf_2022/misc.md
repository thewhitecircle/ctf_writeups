# misc

## Ishihara test++
> Solved by: Taz34

Convert the image to PNG format, run it through stegsolve and change color planes multiple times till it gets readable.

![](https://i.imgur.com/emntk0g.png)

And here is the ```flag: sdctf{c0untle55_col0rfu1_c0lors_cov3ring_3veryth1ng}```

----------

## Bishop Duel
> Solved by: Taz34

We have a chess board and we are playing as a white bishop and opponent as a black bishop.
So for us to lose or win this game we need to be running our bishop on the same color, i.e. as in the image below we can see the opponent ‘BB’ can move on ‘__’ spaces and we ‘WW’ can move on ‘\\\\’ spaces, so we need our bishop to move on ‘__’ spaces.

![](https://i.imgur.com/WA1zPxW.png)

So for that we need to get out of the board and there are tow ways out of the board i.e. the top right corner or the bottom left corner.
So, I decided to move out of the top right.
I used C1 then E2 and then we landed on a ‘__’ space.

![](https://i.imgur.com/uaalQSO.png)

Now just get in the way of the opponent to lose to get the flag.

![](https://i.imgur.com/60Ofz10.png)

```
Flag: sdctf{L0SiNG_y0uR_S0uRC3_C0d3_sUcKs}
```
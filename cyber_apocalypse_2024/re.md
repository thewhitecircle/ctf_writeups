# re

## Packed
> solved by : avantika(@iamavu)

we are given a binary, called packed i.e this has been obfuscated/packed by the dev, which hinders our exploitation and reversing as it’s very hard to understand what’s going on

we use checksec command provided by pwntools to check which packer they have used
`pwn checksec packed`


![](https://i.imgur.com/v8mMY4p.png)


as we can observe it has been packed with UPX, quite the popular packer, now we just need to unpack it to see all the functions properly, so we can dissect it using the following command
`upx -d -o unpacked packed`
we would now have binary called unpacked in our current directory, which will be unpacked and not obfuscated
fire up your ghidra, let’s have a look at the functions, the `entered` function has our flag in plaintext, so pretty much easy win
`HTB{unp4ck3d_th3_s3cr3t_0f_th3_p455w0rd}`
pwned!

----------

## LootStash
> solved by : Starry-lord

Since I don’t really dive into reversing usually, I decided to try my luck with tools like ghidra for the occasion.

![recon on the file](https://i.imgur.com/3zMCcVZ.png)


We can see the binary seems to output a random string, searched into a list then closes the program.

Immediately curious to see what was the list, I looked for it into ghidra and eventually cycling through the functions, you can find it.


![sentence list found](https://i.imgur.com/w3didgB.png)


Then curiosity made the rest possible, since i wanted to find some sort of pattern or something that would stand out:

![finding the flag v1](https://i.imgur.com/hNrPN1w.png)


After all of this, realise you could’ve actualy just use strings:


![](https://i.imgur.com/FxDZCAC.png)


`HTB{n33dl3_1n_a_l00t_stack}`

---

## Boxcutter
> Solved by : thewhiteh4t


- Very simple challenge, all we had to do is load the binary in GDB and go few instructions forward and we get the flag, free points.


![](https://i.imgur.com/H5uRBqj.png)



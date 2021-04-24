# Hardware

> Solved by : Nigamelastic

* `.sal` files are saleae logic analyzer files. 
* I got this as reference https://dystopia.sg/allesctf20-PWNkemon/
so i downloaded the sla logic analyzer
https://www.saleae.com/downloads/
* from here after installing, i opened the sal files which i could and then the following:


## Serial Logs:

* I imported the value i am pretty sure if i find the right analyzer it should work

![](https://i.imgur.com/mO5s5Lx.png)

* by far for this challenge async serial works the best
* challenge states raspberry pi which has a baud rate of 115200
* after some pondering i realized that there are some errors in parity on fixing them and using the `115200` in async serial we get the hex converting which gives us this:

https://www.dropbox.com/s/ztqoa16wvp6rvf6/message.txt?dl=0

![](https://i.imgur.com/1wJv0Ws.png)

* found this : https://6e726d.github.io/Challenge/Ekoparty15/
* this writeup explains how to get the baud rate
* open the SAL file and zoom

![](https://i.imgur.com/hgD3Jz4.png)

* zoom 

![](https://i.imgur.com/fzE0pOJ.png)

* zoom

![](https://i.imgur.com/qwj6blJ.png)

* zoom 

![](https://i.imgur.com/1Gl1HTX.png)

* until you see the smallest unit of the digital wave then measure it: as its 13.498

![](https://i.imgur.com/j0S5hvd.png)

* since it is in **μ** we will divide it by 1000000
* so 1000000/13.498 = 74085.049637
* I obviously used 74000 as bit rate, converted the hex to ascii with the tool itself and it gave me the flag:

```
CHTB{wh47?!_f23qu3ncy_h0pp1n9_1n_4_532141_p2070c01?!!!52}
```

---

## Compromised

* import the file in salea logic analyzer, and use i2c analyzer
* export the data and you will see two columns if we take everything written and try the  hex dump we get

```
set_maCxH_lTimB{itn_tuo1:110_se73t_2mimn1_nli4mi70t_2to5:_1c0+.]<+/4~nr^_yz82Gb3b"4#kU_..4+J_5.
```
```
3M.2B1.4B.1dV_5. yS.5B7k3..1V.Qxm.!j.@Q52yq)t%# @5%md}S.
```

* and we can see its slightly off i noticed the following :

![](https://i.imgur.com/y8YiO2F.png)

* so i used only `0x2C` used the corresponding hex 

which is 

```
0x43 0x48 0x54 0x42 0x7B 0x6E 0x75 0x31 0x31 0x5F 0x37 0x33 0x32 0x6D 0x31 0x6E 0x34 0x37 0x30 0x32 0x35 0x5F 0x63 0x34 0x6E 0x5F 0x38 0x32 0x33 0x34 0x6B 0x5F 0x34 0x5F 0x35 0x33 0x32 0x31 0x34 0x31 0x5F 0x35 0x79 0x35 0x37 0x33 0x6D 0x21 0x40 0x35 0x32 0x29 0x23 0x40 0x25 0x7D
```

* and got the flag 

```
CHTB{nu11_732m1n47025_c4n_8234k_4_532141_5y573m!@52)#@%}
```
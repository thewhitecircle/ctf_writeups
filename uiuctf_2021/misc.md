# misc

## CEO
> Solved by : thewhiteh4t

- We have a `.cap` file in this challenge
- its a handshake file which can be used with `aircrack-ng`


    aircrack-ng megacorp-01.cap -w /usr/share/wordlists/rockyou.txt


![](https://i.imgur.com/fkJw0Nl.png)



    uiuctf{nanotechnology}


----------

## doot doot
> Solved by : thewhiteh4t


- we are given a youtube video which is 8hrs 52mins long
- bee movie script is looping
- flag appears in the scrolling text at 9:55
- https://youtu.be/zNXl9fqGX40?t=595


![](https://i.imgur.com/HnW5dyi.png)


`uiuctf{doot_d0ot_do0t_arent_you_tired_of_the_int4rnet?}`

---

## Emote
> Solved by : Starry-Lord

Challenge mentions sharing images on discord, so I checked uiuctf 's discord and found a suspicious emoji:


![](https://i.imgur.com/Vu74o6r.jpg)


Use zsteg on the png to read flag:


    uiuctf{staring_at_pixels_is_fun}


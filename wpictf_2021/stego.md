# stego

## Glute

> Solved by : Ava

* we have a PNG image

![](https://i.imgur.com/7AUOHqb.png)

* running `foremost` reveals a JPG and PDF files inside it

```bash
    Foremost version 1.5.7 by Jesse Kornblum, Kris Kendall, and Nick Mikus
    Audit File
    
    Foremost started at Sat Apr 24 21:28:46 2021
    Invocation: foremost glute.png
    Output directory: /home/twh/ctf/wpictf/glute/output
    Configuration file: /etc/foremost.conf
    ------------------------------------------------------------------
    File: glute.png
    Start: Sat Apr 24 21:28:46 2021
    Length: 335 KB (343136 bytes)
    
    Num         Name (bs=512)               Size         File Offset         Comment
    
    0:        00000564.jpg               19 KB              288793         
    1:        00000000.png              335 KB                   0           (699 x 699)
    2:        00000557.pdf               55 KB              285674         
    Finish: Sat Apr 24 21:28:46 2021
    
    3 FILES EXTRACTED
            
    jpg:= 1
    png:= 1
    pdf:= 1
    ------------------------------------------------------------------
```

* PDF contains our flag

![](https://i.imgur.com/dgnNrXG.png)

```
    WPI{P0lyGlOtz_R_koo1}
```

----------

## Suspicious traffic

> Solved by : thewhiteh4t

* we are given a pcapng file with some HTTP traffic
* some of the requests have extra characters in data field
* they can be listed using tshark easily

```bash
    tshark -r capture.pcapng -T fields -e http.file_data | grep "\S" | grep -v "html" | cut -d "\\" -f 1 | tr -d "\n"
```

```
WPI{su3p1ci0uS_htTp}
```
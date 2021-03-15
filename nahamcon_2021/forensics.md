# forensics

## buzz

```
$ mv buzz flag.z
$ uncompress flag.z
$ cat flag
```

----------

## Henpeck

if we inspect the pcap file in wireshark we can see some of the packets have “Leftover Capture Data”, these are our keystrokes, we can extract the value of these by using tshark :

```
tshark -r henpeck.pcap -T fields -e usb.capdata > usb_data.txt
```

after that we can use a py script to convert this data into readable characters

```
newmap = {
	4: 'a', 5: 'b', 6: 'c', 7: 'd', 8: 'e',
	9: 'f', 10: 'g', 11: 'h', 12: 'i', 13: 'j', 14: 'k', 15: 'l',
	16: 'm', 17: 'n', 18: 'o', 19: 'p', 20: 'q', 21: 'r', 22: 's',
	23: 't', 24: 'u', 25: 'v', 26: 'w', 27: 'x', 28: 'y', 29: 'z',
	30: '1', 31: '2', 32: '3', 33: '4', 34: '5', 35: '6', 36: '7',
	37: '8', 38: '9', 39: '0', 43: '    ', 44: ' ', 45: '_', 47: '{',
	48: '}', 56: '/'
}
ks = []
msg = []

with open('hid_data.txt') as kshex:
	mykeys = kshex.readlines()
	for key in mykeys:
		if len(key) > 5:
			ks.append(key)

i = 1
for line in ks:
	bytesArray = bytearray.fromhex(line.strip())
	for byte in bytesArray:
		if byte != 0:
			keyVal = int(byte)

			if keyVal in newmap:
				msg.append(newmap[keyVal])
			else:
				pass

	i += 1

print(''.join(msg))
```

----------

## Typewriter

* first we need to find the profile for this memory dump

```
volatility imageinfo -f image.bin
```

profile is `Win7SP1x86_23418`

* now the challenge states that the files were lost in a system crash, so we need to look for files

```
volatility filescan -f image.bin --profile Win7SP1x86_23418
```

* this provides a long list of files, but we know in windows people work in desktop most of the time so after inspecting desktop path we can see this file path

```
0x000000007e841f80      8      0 RW-r-- \Device\HarddiskVolume1\Users\IEUser\Desktop\CONFIDENTIAL DOCUMENT.docx
```

* now we can try and dump this file

```
volatility -f image.bin --profile Win7SP1x86_23418 dumpfiles -Q 0x000000007e841f80 --dump-dir .
```

* using file command we can see the file format and rename appropriately but the file is corrupt, remember system crash?

* but docx files can be extracted like a zip with `unzip` command

* after extracting we get these files/dirs

```
drwxr-xr-x 5 twh users 4096 Mar 14 13:54  .
drwxr-xr-x 3 twh users 4096 Mar 14 13:54  ..
-rw-r--r-- 1 twh users 1422 Jan  1  1980 '[Content_Types].xml'
drwxr-xr-x 2 twh users 4096 Mar 14 13:53  docProps
drwxr-xr-x 2 twh users 4096 Mar 14 13:53  _rels
drwxr-xr-x 4 twh users 4096 Mar 14 13:53  word
```

* inside word we have a file named `document.xml`

* open this file in browser and flag is in plain text :D

----------

## Parseltongue

first get python code from bytecode using `uncompyle6`

```
    #!/usr/bin/env python3
    import Crypto.Util.number as l2b
    import random
    '''
        sszz -> a_list
        zzss -> some_bytes
    '''
    a_list = [
     'aposlogahs', 'apsle', 'Sine', 'aʃe', 'bei∫ed', 'tuif', 'Kura', 'Vera', 'pard', 'pardshesl', 'bo∫', 'Gara', 'vinth', 'Pelʃis', 'keilsing', 'khair', 'tikni', 'Bana', 'Slehara', 'koukh', 'kups', 'dai', 'Andi', 'dorʃe', 'doʃe', 'sloʃe', 'kaʃe', 'Sarna', 'Suu', 'giʃe', 'Gorna', 'ass-girou', 'dros', 'feslure', 'hasli', 'riʃan', 'fraeslis', 'vris', 'gatsi', 'runʃe', 'Tira', 'hishe', 'einʃe', 'hesleuf', 'Firna', 'Baʃ', 'ʃem', 'ai', 'ine', 'dinʃe', 'Negei', 'slanp', 'ʃena', 'sliʃe', 'dati', 'slifai', 'Kuine', 'Ha', 'nisl', 'ʃe', 'Sobne', 'bna', 'Sora', 'ovith', 'houk', 'parknent', 'fasar', 'nesha', 'praughs', 'Pura', 'ʃine', 'ʃane', 'gisan', 'rai∫e', 'kata', 'Ara', 'Nigi', 'akaʃe', 'rashe', 'slan', 'Derne', 'Tina', 'snart', 'gariʃe', 'kerashe', 'stabsle', 'Fasi', 'Peina', 'Tasi', 'Sekusi', 'Harne', 'kapi', 'Athne', 'vaʃe', 'asl', 'ʃik', 'agiro', 'vei', 'Asuna', 'Teʃ', 'Fiʃ', 'Doʃ', 'ʃira', 'Haʃ', 'Vuʃ', 'vindovth', 'Bira', 'Sa', 'Slu', 'ou', 'iangsteur'
    ]
    some_bytes = b'\x07\x1c\x0e\x14\x17\n\x06\x03\x0cJ\x00@G\x0e\x017X\x0b\x04W\xf8\xb5\x03P\x06\x0f\x80\xea\x9b\x00\x05A\x16\\\x00.\x17\x0f'
    s = False
    z = True
    ss = s & z  # False AND True -> always False
    # abs()      -> Returns absolute value
    # abs(True)  -> 1
    # abs(False) -> 0
    z = abs(ss) - abs(z) # -1
    zz = ss | z          # False OR -1 = -1
    z = zz - z - z       # -1 - (-1) - (-1) = 1
    zz = z | z           # 1 OR 1 = 1
    z = zz << zz         # 1 LEFT SHIFT 1 = 2
    s = ss >> ss         # False RIGHT SHIFT False = 0
    sz = s << z          # 0 LEFT SHIFT 2 = 0
    zs = z << s          # 2 LEFT SHIFT 0 = 2
    z = zs - sz          # 2 - 0 = 2
    # Values at this point
    # z  = 2
    # zs = 2
    # sz = 0
    # s  = 0
    # zz = 1
    # ss = False [unchanged]
    ss = str(z).replace(str(zs), str(ss).replace(str(ss), str(z).replace(str(z), '')))
    # '2'.replace('2', 'False'.replace('False', '2'.replace('2', '') ))
    # '2'.replace('2', 'False'.replace('False', ''))
    # '2'.replace('2', '')
    # ss = ''
    sss = bytes(ss.join(a_list), 'utf-8')
    zzz = bytes([_a ^ _b for _a, _b in zip(sss, some_bytes)])
    ##### JACKPOT #####
    print(zzz.decode())
    ###################
    ssszzz = bytes([_a ^ _b for _a, _b in zip(zzz, some_bytes)])
    sss += b'S'
    ssss = []
    ss = sss[:len(sss) // 2]
    zz = sss[len(sss) // 2:]
    for s in range(len(ss)):
        ssss.append(ss[s] ^ zz[s])
    else:
        if 5 == 1:
            print(' '.join([random.choice(a_list).upper() for _ in range(random.randrange(5, 10))]))
        else:
            print(' '.join([random.choice(a_list).upper() for _ in range(random.randrange(5, 10))]))
```

---
layout: load_md
title: The White Circle | Nahamcon 2021 | Henpeck Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2021
parent: nahamcon_2021
category: forensics
challenge: Henpeck
tags: "forensics, pcap, tshark, usb"
---

<h1 class="heading card-title white-text">Nahamcon 2021</h1>

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


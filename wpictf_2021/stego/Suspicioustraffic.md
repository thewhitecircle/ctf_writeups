---
layout: load_md
title: The White Circle | Wpictf 2021 | Suspicious traffic Writeup
desc: Check out our writeup for Suspicious traffic for Wpictf 2021 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: stego
challenge: Suspicious traffic
tags: "stegano, twh, pcap, tshark"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---



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
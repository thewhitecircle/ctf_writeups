---
layout: load_md
title: The White Circle | Wpictf 2021 | Suspicious traffic Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Wpictf 2021
parent: wpictf_2021
category: stego
challenge: Suspicious traffic
tags: "stegano, twh, pcap, tshark"
date: 2021-04-26T00:00:00+00:00
last_modified_at: 2021-04-26T00:00:00+00:00
---

<h1 class="heading card-title white-text">Wpictf 2021</h1>

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
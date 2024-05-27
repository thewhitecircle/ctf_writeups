---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Confinement Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Confinement
tags: "forensics, starry, twh, ad1, ftk, ransomware, encryption, evtx, powershell, python, dotnet"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

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

```
https://blog.khairulazam.net/2023/12/12/extracting-quarantine-files-from-windows-defender/

https://github.com/zam89/Windows-Defender-Quarantine-File-Decryptor
```

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


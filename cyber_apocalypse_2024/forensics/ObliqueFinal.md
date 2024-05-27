---
layout: load_md
title: The White Circle | Cyber Apocalypse 2024 | Oblique Final Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2024
parent: cyber_apocalypse_2024
category: forensics
challenge: Oblique Final
tags: "forensics, starry, twh, memory, volatility, dotnet, ilspy"
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2024</h1>

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


```
HTB{IL_st0mp1ng_4nd_h1b3rn4t10n_f0r_th3_gr4nd_f1n4l!!}
```



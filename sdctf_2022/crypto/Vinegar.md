---
layout: load_md
title: The White Circle | Sdctf 2022 | Vinegar Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: crypto
challenge: Vinegar
tags: "crypto, ava, cipher"
date: 2022-05-10T00:00:00+00:00
last_modified_at: 2022-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2022</h1>

## Vinegar
> Solved by - Avantika(iamavu)

```
Vinegar
My friend gave me another encrypted flag...I think they hate me! I heard them yell something about “Vinegar”, but I still don’t know what they’re talking about!

Ciphertext
{wbeyrjgewcfroggpesremvxgvefyrcmnnymxhdacgnnrwprhxpuyyaupbmskjrxfopr}

Note
My friend also yelled something about “preventing plaintext attacks” and said once I’ve decrypted this, I’ll need to append sdctf to the front of it.
```

As the name implies it is Vigenere cipher, we just need to bruteforce it.|
I googled for Vigenere bruteforce and the first [tool](https://www.guballa.de/vigenere-solver) did the trick
don’t forget to append `sdctf` in the beginning 

FLAG :

```
sdctf{couldntuseleetstringsinthisonesadlybutwemadeitextralongtocompensate}
```



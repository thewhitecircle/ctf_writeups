# crypto

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



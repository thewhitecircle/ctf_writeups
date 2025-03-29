---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Traces Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: crypto
challenge: Traces
tags: "crypto, starry, aes, python, reuse"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

## Traces
> Solved by Starry-Lord

We were given a server.py which allowed us to connect to an IRC chat with encrypted messages.

One notable thing in that server.py was:


    def __init__(self, host, port):
            self.host = host
            self.port = port
            self.key = os.urandom(32)
    
    <...>
    
    def encrypt(self, msg):
            encrypted_message = AES.new(self.key, AES.MODE_CTR, counter=Counter.new(128)).encrypt(msg)
            return encrypted_message
        
        def decrypt(self, ct):
            return self.encrypt(ct)

What we could see from this was the key was only generated once during 
`__init__(self, host, port)`, and the `AES.MODE_CTR` encryption was having the same counter every time it occurred because `Counter.new()` starts from 0 if not otherwise specified.
Basically the key stream was the same reused for each encryption, which meant we could try and derive it from known plaintext. The server itself allowed connecting manually, and after joining a channel, one would have to write `!nick username` in order to start chatting. This was a good start for decrypting the rest of the messages because we could easily guess that was the content of the first messages in the chat.

Decrpytor.py:


    from binascii import unhexlify, hexlify
    
    # === Known plaintexts from the !nick messages ===
    known_pairs = {
        "a0811d228b9d321130e412d21323": b"!nick Doomfang",
        "a0811d228b9d250a30fb19d11c2a25": b"!nick Stormbane",
        "a0811d228b9d240b31ec16df1423288f": b"!nick Runeblight"
    }
    
    # === Full encrypted chat log ===
    messages = [
        ("23:30", "Doomfang", "a0811d228b9d321130e412d21323"),
        ("23:32", "Stormbane", "a0811d228b9d250a30fb19d11c2a25"),
        ("23:34", "Runeblight", "a0811d228b9d240b31ec16df1423288f"),
        ("00:00", "Stormbane", "d68a5337859d11112ba91593132137dbffa1a80f6eb256ede20edf0de102ef97b0df0254e7e22f9779fa0efbc793d8bcd69c8f147ccc14e12d939a27cbffb9c1e340"),
        ("00:01", "Doomfang", "d481102492ce021130ed5a93352533dbffa0bd5d6af05bfdf3408b04ea5bbd81bbdd0007a6a4438665b810fbde93dabe99868a0f32c85cf330dd893082e6adc7a602037a1e1c260e9d6e91"),
        ("00:02", "Runeblight", "cf80006199d802527feb01c75d0d6796ababb04a6cbb50f6f10ed80ae947bd87bccf1d52a8ae438164ba13bac08593ecbf95da1234ca05a02ad68426c7a9adc6aa4e156c4a512206837f8e47fdc8eeac01778cbbd24bfb0d6d798696efb05ad75d46ccd3aa41"),
        ("00:04", "Doomfang", "d5871d32c0de1e1f31e711df5d2d33dbe5a7ac0f7cb15ffdb648c417a44ef29cb59a1a46a5a910dc2d9118af8b859dbf819a8e05348f08ef79dc9f2782f9aadcf00f166c4a4e240e8625"),
        ("00:04", "Doomfang", "c98a0624c0d4055e2be111930d253388fba0aa4e7cb519fef95c8b0af150bd81b7d91b55ace2009a6cb313bec0cc9de99b9ac8012ae734c30f868c0ac9eaba88dc5a145c264d2438a12dc17d"),
        ("00:05", "Runeblight", "c680006189c9585e10e718ca5d37289af9adf8467bf04ef1e2468b0af150bd9fbdc91a07bdb0168179b819fbcd9ad1a59380d4"),
        ("00:06", "Stormbane", "d88a076fc0f2030c7fe515c009642d94fdadf8426ea919f0f758ce45e847fb86f2ce1c46aaa710dc2d8a18fbc183ceb8d6919f462aca0ef979d08b27c7efadd9a8"),
        ("00:07", "Doomfang", "c8c8196183d5131d34e01ad45d2b3589aba4b7487cf04df7b64cce45f757ef97f2d40107bdb0029168fd12bd8c99c8bed692991235c012f379c18f38c3e0b6c6a8"),
        ("00:08", "Runeblight", "ca8a1131c0d0135e2af910d2092124d5ab81be0f7bb85ce1b64dca11e74abd9dbc964e50ace50f9e2db51cadc9d6c9a3d69299127cc91df32d9d"),
        ("00:09", "Stormbane", "c8c8182dc0de19132fe806d65d30289eaba4b95b6aa34db8f24fdf04a455f486ba9a0152bbe201936eb608ab8c86d1ad98ddda31398f11f52ac7ca30d0e8abd0a60f0c704a4f2206852bd94abcc9eefe1038c4bdc84fbb"),
        ("00:10", "Doomfang", "c889542496d804072be11ddd1a642988ababb44a6ea215b8e14b8b08eb54f8d2a6d54e53a1a7439c68a509fbdf82dcab93ddda2929dd5ce736d28675cbfaf8c2ef1a0a60041c39048a68c601"),
        ("00:11", "Runeblight", "c9801825c0d218507fc053de5d37259ee2a6bf0f7ca44bf9f849ce45f74bfa9cb3d61d07afb00c9f2db208afdf9fd9a9d8d3ad037cc215e731c7ca37c7a9afd4f20d0a6c0e12"),
        ("00:12", "Stormbane", "d68a542281d3510a7ffd15d818642195f2e8aa467cbb4ab6b662ce11a351bd9eb7db1842e9b60b9b7efd1eb3cd98d3a99ad398033ac00ee579c78230dba9acc7e70d09291f4f65"),
        ("00:13", "Doomfang", "c088062485d9585e12e602d65d252c97abbcb94364a319ecf90edf0de102ed80bbcc0f53ace2119d62b053fbfe83d3a9949f930134db50a029df8f34d1ecf8d6ea0b037b4a482304cb67c148ef9ee3e9077d82"),
        ("00:14", "Runeblight", "d481102492ce021130ed5a9334632ddbefa1ab4c60be57fdf55ac20be302f39da5944e6eafe2179a68a45db3cd80d8ec85969f087cda0fac79c48f75cffcabc1a60a0b7a0b4c3b048a798e46f1d3eee81c79d8bdd653bb"),
        ("00:53", "Doomfang", "a083112096d8"),
        ("00:53", "Stormbane", "a083112096d8"),
        ("00:53", "Runeblight", "a083112096d8"),
        ("00:26", "Runeblight", "d68a543288d203123ba91fd618346094febaf85f63b157f6ff40cc45ec47ef97fc9a3a4face20c8779b80ffbc497d1a085d39b14398f12ef2d939930c1fcaad0aa4e03670e1c3f0e842bc34ef2c7abe90c7ddff8cd4be10960799d8bf8b041c65b5c85d3b10edfe19d0380e3"),
        ("01:01", "Doomfang", "c088062485d9585e0be11193182a2596f2efab0f7cb356ede25d8b02f64dead2bfd51c42e9b206807eb40eafc998c9e2d6ba9c4628c719f979d08b21c1e1f8d0f00b0c290b1c3c098278de4aee9ee4ea5577d9aa9a4ef019613e8790b1b05ade5b4b85c7b003ddaf950085a80c8453fed2bf3cdf9fe7cbf11ca9d375e19a7161c67f79d2c593edb993c015616c3cfa340a0fb93cebbbf2ee95f1a7c2312c7da572df0801104c3d75eebdd119217045a4f02f24"),
        ("01:02", "Runeblight", "c8c80224c0df131b31a907c708203992e5aff85b67b519ece44fc800f702f197b4ce4e45acaa0a9c69fd1fa28c99c8bed68388032ac613f52a93833bc1e8b6c1e71a0b66044f67418a65ca0fefd1e6e90170c5b6dd0af30f6d359ac3eae241d8591c85ffac1d91e19d1b84a25e8e14f0ddf13cdbdafed4ac1c96d726ac9c677b923179d391d2f2bc9bd954797779e7290a1db279eca6b9ef88a6ba923a2c6fe669de051a0c487e6aa6b3d04b6f6c5fa4a0336ce36b8a6d3587c31b8cf924110e4661cabbbb24478344ce36bc7c578ea1da53eb82829ecea65a05789e97922080148c46d5bc9f1b869f199f7cba5f0a6b53032d6ba9af9fba726bdcbf233eaf183c88c4d1058b545e4ff292c42584a0a742647c7aeee027ce21ff7b6d299dcbd112d5e3073430ca83ff970c43468cefb5a8e7e0a55a4703384b4b84ec73dc6ea23516ea765a1d9f14d96a1c0d323ce31c0ce884fc371b19f73da4"),
        ("01:03", "Stormbane", "c8c8196181d1041b3eed0d931e362f88f8e5bb476ab352f1f8498b0af150bd81a2df024bbead11992dbc1abac598ceb8d68792037cce12e330d6842182fbbdd6e91c067a441c0207cb7fc646ef9ee9e9147bc3b69a5df41928298891e9b041d01e53cb90b603d5ea8a4f96a34f8d55f1cfbc2ac5cbbe98961b92da75ea866c71c66164c98a94aff5bec241356d3fb32f5e5cb52ab9a9b1f58ea7b192362679a926c440100c0d297beebac51d642350e1f02e65a1729c207ad2fe07c3e8221e07466f888af32c14c048cf20b5791b89a0da51ed859e92cea81842379bd6893042f7775adff8cc16cf9f17ca66f34b026f53507a67bab29fa4717e9fb8223eac123c94d4c0578b4e1b43e8d7c06e849cae5b743b6bf5bb"),
        ("01:04", "Doomfang", "d68a542281d318112ba915d51b2b329faba0bd5c66a458ecff41c54ba46bfbd2a6d20754e9ab10d26cfd1fa9c997dea4dad38e0e39c15cf431d6ca1dcbeeb095c501176709552746982bc840eeddeeff5575cda19a4bf9186d388d9abdf24b96515c85dfac1d91fb8a0e9aa102c571e9debf6fdfd7f798ac519fda39e99c76358b7865d28499e4f59fd840796079f7294511fc36ecbaf2e489a5bdc03d696de46bc0491c0543703e99b78406747045e1e33364a5778b207493c3538ce52e0e4b4b29ca80a63f14c255c222be7d5788a19c58ea858f8480a9175372ccd59e31ce57805cd4a8cd168ed60bda70fd"),
        ("01:05", "Runeblight", "c497152294d10f507fc81ad75d21369ee5e8b1492fa75cb8e44bc604ed4cbd87bcc90b42a7e2059d7ffd13b4dbda9dbb93d3940339cb5ce336dd9e3ccceebddbe5174279065d2512c52be749bccae3e9555bc3add449fc06283f8691e9f948df5b4185d9ad1c91e299089aae4d8914fddaa33dc2dae0cbf31c89d375ef80777982317ac99697a1b49fd450667779e7290a08b43cf0baf2f293a3bbdc3f2161e962c3065526427e69abf2cc0a776611a0a02f6fa07197293580ce538eee2c051802208ccfb62340d15e812ab6380384a1da4df69f8796d2b856427698d2db3dd3579056d8b4da1ddc"),
        ("01:06", "Stormbane", "d88a076dc0df030a7ffe11931031338fabbcaa4a6ea419f1e20ec40be85bbd93a19a0f07a5a310862daf18a8c384c9e2d6ba9c462bca5ce13ac78323c3fdbd95ef1a427d05536b128464c003bcc9eeac0771dfb39a58f01c6d38858af3f70edf4a4185dcb60cd0fb91009de30cac40bfd2a26fc7def0ddb3599a9634ffd5225db2536de5979be38ab8c554726330fd217539a429f5a7bbf586a5bddd361659ec72d8773e07540150a1bcc70e5e5154b4f3392bbe"),
        ("01:07", "Doomfang", "c6801b25ce9d38117ffb11d0123624dbe4aef8467bf054ede55a8b00fc4bee86f2d30007bdaa06d27aaf14afd893d3ec829c97032f815cc979c48339cea9bddbf51b106c4a5d270dcb7fdc4effdbf8ac146ac9f8df58f4196d3dc5c3fcfe4a96574685c3b10edde3d80196bb499714fddef13cdbd0f9ddb11c91d075e39f677b8a683886ac94a1a194d215706a3cfe3f0a19aa3cebe8bee486a3bac1782668a56fc4045515487e69a7bec84b696247a4a03265e36d9c2e3b9cd35380e32c05084761"),
        ("01:08", "Runeblight", "c088062485d9585e0be11193102b329eabbfbd0f6bb94afbe35dd845ed56b1d2a6d20b07aeb0069379b80ffbd89ed8ec849a890d728f39f63cc19375cfe6b5d0e81a427e0f1c2f04876ad703bccae3e9555bc3add449fc06282a9d91f8fe49c25657cbc3f906c5fcd80b96ab498b47fac8ff6ffcdab2d5aa4f8a9634ef9b2266897e78868797e7ba8ed2157a712bb3314312b836eee8bde7c7bea4c2373b7af068d95c0c424e3271bdb7d745"),
        ("01:09", "Stormbane", "d68a543288d203123ba911dd19643493e2bbf8426ab54df1f8498b04ea46bd9fbdcc0b07bdad43932db012a9c9d6cea9958688037cdc1dee3ac79f388ca991d3a61a0a6c034e6b0c8a6ccb5cbcd1f9ac0668c5bdc90af4186d798a8ff2e347d85912ccdef54fc5e79d16d3a04d9c14f6d5a52ad9dcf7c8ab1c91c327ac986d6782623886b297a1b889c441356a36e7665e1db73cb9bcbae093f1b7da39276de028906410160d2a76a7a18409642345a9e57c66a26d8d6d3997c40082ec284b024c6f9e87ba3e14d34bc020b536")
    ]
    
    keystream = bytearray()
    for hex_ct, pt in known_pairs.items():
        ct = unhexlify(hex_ct)
        ks = bytes([c ^ p for c, p in zip(ct, pt)])
        for i in range(len(ks)):
            if len(keystream) <= i:
                keystream.append(ks[i])
    
    def decrypt(ciphertext_hex, keystream):
        ct = unhexlify(ciphertext_hex)
        out = []
        for i in range(len(ct)):
            if i < len(keystream):
                out.append(ct[i] ^ keystream[i])
            else:
                out.append(ord('?'))  # unknown
        return bytes(out)
    
    def extend_keystream(ciphertext_hex, guess_bytes):
        ct = unhexlify(ciphertext_hex)
        for i in range(len(guess_bytes)):
            if len(keystream) <= i:
                keystream.append(ct[i] ^ guess_bytes[i])
            elif keystream[i] != (ct[i] ^ guess_bytes[i]):
                pass  # Optional: log
    
    def main():
        while True:
            print("\n=== Decrypted Messages ===\n")
            pending = []
            for i, (timestamp, sender, ct_hex) in enumerate(messages):
                pt_bytes = decrypt(ct_hex, keystream)
                plaintext = pt_bytes.decode(errors='replace')
                if '?' in plaintext:
                    pending.append((i, timestamp, sender, ct_hex, plaintext))
                print(f"[{timestamp}] <{sender}> : {plaintext}")
            if not pending:
                print("\n[+] All messages decrypted!")
                break
            print("\n[?] Messages with partial decryption:\n")
            for i, timestamp, sender, ct_hex, pt in pending:
                print(f"\[{i}\] [{timestamp}] <{sender}> : {pt}")
            try:
                index = int(input("\nSelect index to guess plaintext for (or -1 to quit): "))
                if index == -1:
                    break
                _, _, _, ct_hex, current_pt = pending[index]
                print(f"Current partial: {current_pt}")
                guess = input("Your guess: ")
                extend_keystream(ct_hex, guess.encode())
            except Exception as e:
                print("[-] Error:", e)
    
    if __name__ == "__main__":
        main()

So this was a game of guessing the correct word in order to retrieve the original messages:

Password for the secret channel:


    [00:04] <Doomfang> : Here is the passphrase for our secure channel: %mi2gvHHCV5f_kcb=Z4vULqoYJ&oR


![](https://i.imgur.com/d5znI6V.png)


Flag:


    HTB{Crib_Dragging_Exploitation_With_Key_Nonce_Reuse!}



---
layout: load_md
title: The White Circle | Sdctf 2022 | Paypal Playboy Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2022
parent: sdctf_2022
category: osint
challenge: Paypal Playboy
tags: "osint, starry, blockchain"
date: 2022-05-10T00:00:00+00:00
last_update: 2022-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2022</h1>


## Paypal Playboy
> Solved by: Starry-Lord

![](https://i.imgur.com/fLmviX8.png)


link for the mail: https://cdn.discordapp.com/attachments/808487148332122144/969683292918001787/mbox

Reading the mail shows us some base 64 encoded string, which is a commonly used Content-Transfer-Encoding for e-mails.

```
    From flag.peddler@wehate.sdc.tf Sat Apr 23 00:39:01 2022
    Delivered-To: [redacted]@gmail.com
    Received: by 2002:a05:6520:266f:b0:1b9:b81b:dce2 with SMTP id il15csp1101047lkb;
            Fri, 22 Apr 2022 17:39:02 -0700 (PDT)
    X-Google-Smtp-Source: ABdhPJzxwJlzfSGrxfS8BUrPyF3Rv2x9sStBn/U0tFRYMXyPsNYCyOVhWQQn7IKnPNjwjmMRrzkp
    X-Received: by 2002:a05:6402:d2:b0:413:2e50:d6fd with SMTP id i18-20020a05640200d200b004132e50d6fdmr7715550edu.171.1650674341863;
            Fri, 22 Apr 2022 17:39:01 -0700 (PDT)
    ARC-Seal: i=1; a=rsa-sha256; t=1650674341; cv=none;
            d=google.com; s=arc-20160816;
            b=BnpreoBKj7rM/1WsS1qGkQIpbJ5Q7glsLDD6PWDJRM4ChXRSMefEmwqElRjZAsNnxQ
             WbkErwUv9FGbI9XDj0QRlgbcKOY+zfDryej6XaZQ7/YzfTNUYFVLQ9H1tFQvNV32oI9m
             2d8eZn4LyHtp4x4M2nIplYo9EWPUObAd9/V2ajSdVBagveQXocWA28Sdcy2bflA0SplT
             gOUu1TWsT1yD1aRwIzdwyJY8J139u02P7ZIjvO0OHunde46MKIlcA50KyumhKeTCyxqJ
             YLjmgyw6Aey6PlG224L9Mslkcg739k81aUKUTMflpKGqxzM75UZepuCoKyVcwpCp+3vc
             WFug==
    ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed; d=google.com; s=arc-20160816;
            h=date:message-id:mime-version:reply-to:errors-to:importance:from
             :subject:to;
            bh=SklLFzj9D7lIOMbltmueGQUwZr9CMGutPIb3RqUNk6E=;
            b=EEGOb2Wm/rKABjU6iN1Cv+E3b6mU3Ke04PhXntTkpa7vGH8WeZg7GCfg7dmrBLGyDb
             nGC6L9utfPWqzL7tBmXDtyzqVJE/Qy82QujjnKuT5Vnxuwn1mU1ZOoAXr0KfyK8hOIe6
             jn2nQGeADRUuEedqomYGjUH6RGjuU536npXLWtqmwIwWIRv08UYkItB6ma5pDuuyEdId
             PBwv2ByfPMrReSahatKSk3FbvVuQMCqmHwSlvCIJC2fbNMVw64cQwyCP0sOl1CsFbu98
             UmtH/8ptv3tavNNjIpNqvwCFyp9pbvqZvhOUwDakqV6rY2USr51DIBFymLBllWiCHupb
             w2ZQ==
    ARC-Authentication-Results: i=1; mx.google.com;
           spf=neutral (google.com: 101.99.94.116 is neither permitted nor denied by best guess record for domain of flag.peddler@wehate.sdc.tf) smtp.mailfrom=flag.peddler@wehate.sdc.tf
    Return-Path: <flag.peddler@wehate.sdc.tf>
    Received: from emkei.cz (emkei.cz. [101.99.94.116])
            by mx.google.com with UTF8SMTPS id c1-20020a170906694100b006df76385bb2si7985374ejs.82.2022.04.22.17.39.01
            for <[redacted]@gmail.com>
            (version=TLS1_3 cipher=TLS_AES_256_GCM_SHA384 bits=256/256);
            Fri, 22 Apr 2022 17:39:01 -0700 (PDT)
    Received-SPF: neutral (google.com: 101.99.94.116 is neither permitted nor denied by best guess record for domain of flag.peddler@wehate.sdc.tf) client-ip=101.99.94.116;
    Authentication-Results: mx.google.com;
           spf=neutral (google.com: 101.99.94.116 is neither permitted nor denied by best guess record for domain of flag.peddler@wehate.sdc.tf) smtp.mailfrom=flag.peddler@wehate.sdc.tf
    Received: by emkei.cz (Postfix, from userid 33)
            id 41BF9182372; Sat, 23 Apr 2022 02:39:01 +0200 (CEST)
    To: [redacted]@gmail.com
    Subject: very cheap banner
    From: "cheap sdctf banner" <flag.peddler@wehate.sdc.tf>
    X-Priority: 3 (Normal)
    Importance: Normal
    Errors-To: flag.peddler@wehate.sdc.tf
    Reply-To: flag.peddler@wehate.sdc.tf
    MIME-version: 1.0
    Content-Type: multipart/mixed; boundary=BOUND_62634AA53D6521.02935547
    Message-Id: <20220423003901.41BF9182372@emkei.cz>
    Date: Sat, 23 Apr 2022 02:39:01 +0200 (CEST)
    
    --BOUND_62634AA53D6521.02935547
    Content-Type: text/plain; charset=utf-8
    Content-Transfer-Encoding: base64
    
    5Zyj5Zyw5Lqa5ZOl572R57uc5a6J5YWo6L+Q5Yqo5Lya55qE5buJ5Lu35qiq5bmF44CC5oiQ5Li6
    IFNEQ1RGIOeahOi1ouWutuOAguW/q+aNt+aWueS+v+OAgueCueWHu+S4i+aWueOAguWco+WcsOS6
    muWTpee9kee7nOWuieWFqOi/kOWKqOS8mueahOW7ieS7t+aoquW5heOAguaIkOS4uiBTRENURiDn
    moTotaLlrrbjgILlv6vmjbfmlrnkvr/jgILngrnlh7vkuIvmlrnjgILlnKPlnLDkuprlk6XnvZHn
    u5zlronlhajov5DliqjkvJrnmoTlu4nku7fmqKrluYXjgILmiJDkuLogU0RDVEYg55qE6LWi5a62
    44CC5b+r5o235pa55L6/44CC54K55Ye75LiL5pa544CC5oKo5Lmf5Y+v5Lul5ZyoIFBheVBhbOaI
    luiAheWMuuWdl+mTvuS4iuaJvuWIsOaIkeS7rOOAgjB4QkFkLi4uQTQzQi4uLi4uIFNEQ1RGe+S8
    qumAoOeahF/ml5fluJx9IDPvuI/ig6Mg8J+SsCDinLPvuI8g8J+RnyDwn5izIPCfk5kg8J+NkyDw
    n5i6IPCfkYgg8J+UoCDwn4+eIPCflpYg8J+QviDwn4OPIPCflZUg8J+QtiDwn5uPIPCflJEg4p2H
    77iPIPCfkqkg4qyH77iPIPCfiLfvuI8g8J+RnSDwn5mIIPCfmoIg8J+UnSDimKog4pqh77iPIPCf
    k6wg8J+YtyDwn4+oIPCfmoQg8J+PmSDwn5WcIOKGmO+4jyDwn42VIPCfkagg8J+MjyDil77vuI8g
    8J+MjiDwn5i4IPCfjYQg4pyz77iPIPCflaEg8J+amyDwn5GnIPCflLsg4pmT77iPIPCflKAg8J+Y
    rQ==
    mbox part 1/2
```

This is some text in Chinese, which translates to the following:

![](https://i.imgur.com/0fXEGHS.png)

Then we also have an attachment, named “pay.png” in Chinese:

```
    mbox part 2/2
    --BOUND_62634AA53D6521.02935547
    Content-Type: image/png; name="支付.png"
    Content-Transfer-Encoding: base64
    Content-Disposition: attachment; filename="支付.png"
    
    iVBORw0KGgoAAAANSUhEUgAAAUYAAAFOCAYAAAD6hLSdAAABPmlDQ1BJQ0MgUHJvZmlsZQAAKJFj
    YGASSCwoyGFhYGDIzSspCnJ3UoiIjFJgf8bAwiDMIMigxcCXmFxc4BgQ4ANUwgCjUcG3awyMIPqy
    Lsisncmnb3+YKdt7w+KqjrKaJjemehTAlZJanAyk/wBxYnJBUQkDA2MCkK1cXlIAYrcA2SJFQEcB
    2TNA7HQIew2InQRhHwCrCQlyBrKvANkCyRmJKUD2EyBbJwlJPB2JDbUXBNiNzH0DjI0JuJQMUJJa
    UQKinfMLKosy0zNKFByBIZSq4JmXrKejYGRgZMTAAApviOrPN8DhyCjGgRArBPrPypOBgSkXIZYQ
    wMCw4wPImwgxVR0GBp7jDAwHYgsSixLhDmD8xlKcZmwEYXNvZ2Bgnfb//+dwoJc1GRj+Xv////f2
    ////LmNgYL4F1PsNABfNXW1QnDRZAAAAYmVYSWZNTQAqAAAACAACARIAAwAAAAEAAQAAh2kABAAA
    AAEAAAAmAAAAAAADkoYABwAAABIAAABQoAIABAAAAAEAAAFGoAMABAAAAAEAAAFOAAAAAEFTQ0lJ
    AAAAU2NyZWVuc2hvdNuRKQwAAAI9aVRYdFhNTDpjb20uYWRvYmUueG1wAAAAAAA8eDp4bXBtZXRh
    IHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJYTVAgQ29yZSA2LjAuMCI+CiAgIDxy
    ZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4
    <--SNIP-->
    DtgBO+CN0eeAHbADdmDDAW+MG4b4rR2wA3bAG6PPATtgB+zAhgPeGDcM8Vs7YAfsgDdGnwN2wA7Y
    gQ0HvDFuGOK3dsAO2AFvjD4H7IAdsAMbDnhj3DDEb+2AHbAD3hh9DtgBO2AHNhzwxrhhiN/aATtg
    B7wx+hywA3bADmw44I1xwxC/tQN2wA54Y/Q5YAfsgB3YcMAb44YhfmsH7IAd8Mboc8AO2AE7sOGA
    N8YNQ/zWDtgBO/D/ATm6EF9LClK2AAAAAElFTkSuQmCC
    
    
    --BOUND_62634AA53D6521.02935547--
```

After decoding base 64 we get a qr code png.

![pay.png](https://paper-attachments.dropbox.com/s_3E49052B8F52EA38C938769F2417404AD8A57EF722BEF30D334ECAEE6FFC2A1C_1652046711586_pay.png)

Reading this with any qr code reader app gives a link to the cash app page of the seller.

```
https://cash.app/$limosheen?qr=1
```

Nothing much in the url itself, but now we have a username “limosheen” which we can hunt for.

Staying in the money app spectrum, we could find a paypal user with that name here:

```
https://paypal.me/limosheen
```

![](https://i.imgur.com/D2fHhvt.png)

New clues to work with! We can see a wallet address and that it’s for ropETH. I don’t think it’s a common way to call it, but we’re talking about the Ropsten Ethereum blockhain here, which is also known as the “Ethereum Testnet”. It’s basically used for development and testing purposes before deploying to the mainnet.

So now that we have the wallet and the blockchain, we can try and follow the money 💸 

https://ropsten.etherscan.io/address/0xbad914d292cbfee9d93a6a7a16400cb53319a43b

![](https://i.imgur.com/pbtME73.png)

We can see a small amount of transactions happening 15 days ago, and only one outgoing transaction to another wallet. Following the money, we can consider this wallet as interesting:

![](https://i.imgur.com/gTcCkTa.png)

Receiving wallet (with a hefty amount of ETH and no outgoing transactions):

```
0x949213139D202115c8b878E8Af1F1D8949459f3f
```

I got lucky on this one. I always considered Google to be the superior search engine, period, but for the first time, it was not showing anything. DuckDuckGo won this fight.

![](https://i.imgur.com/UHCfccV.png)

This url was not available anymore, but when looking back in time we can find the flag, still base 64 encoded.

```
https://www.reddit.com/user/wrestling-wave/comments/u9tgk7/how_i_made_10000000_selling_fake_flags_ama/
```

https://web.archive.org/web/*/https://www.reddit.com/user/wrestling-wave/comments/u9tgk7/how_i_made_10000000_selling_fake_flags_ama/

![](https://i.imgur.com/VyuuzMC.png)

Only one capture to look into:

![](https://i.imgur.com/7zP0eUe.png)

```
sdctf{You_Ever_Dance_With_the_Devil_In_the_Pale_Moonlight}
```

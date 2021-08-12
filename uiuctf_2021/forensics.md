# forensics

## tablet 1
> Solved by : thewhiteh4t

- we are given an iPad image in this challenge
- goal was to find a server where the data was exfiltrated
- so a guess was that I will find a server and flag will be present on it
- following this theory I looked for the string `uiuc.tf` because thats the domain being used in all remote based challenges


    grep -rni "uiuc.tf" .


![](https://i.imgur.com/2AoBT9W.png)

- this is a Sqlite3 database file
- I found SSH private key and other login info required for login


![](https://i.imgur.com/dkItbjg.png)



- SSH login
    ssh -i id_rsa red@red.chal.uiuc.tf -p 42069


- the private key is encrypted and the password is actually :
    ********


- but on login I got a message that service allows only SFTP connections, so I tried logging in with SFTP


![](https://i.imgur.com/nzKD3JV.png)

- unlike most ctf challenges flag was not available after login so I downloaded `.bash_history` using `get` command


![](https://i.imgur.com/DXCDagb.png)

- and in this location I found a JPG image…


![](https://i.imgur.com/VgitoW9.png)



![](https://i.imgur.com/rN1zUxn.png)



    uiuctf{upload_task_only_takes_9_seconds_0bf79b}


----------

## tablet 2
> Solved by : thewhiteh4t


- similar to tablet 1 we need to use the iPad image
- this challenge talks about one more imposter
- after some digging around we found a message database


    mobile/Library/SMS/sms.db


![](https://i.imgur.com/rkdVPKy.png)

- using the username in the messages above we found a discord cache database file


![](https://i.imgur.com/sRNUzgX.png)

![](https://i.imgur.com/z2iSJV7.png)

- this file contains a JSON with readable chat
- one of the messages contain a password and other one contains an image in attachment
- password : `su5Syb@k4`
- image : `https://cdn.discordapp.com/attachments/868908952434384926/868914084639293490/image0.jpg`
- full JSON

```json
[
    {
        "attachments": [
            {
                "content_type": "image/jpeg",
                "filename": "image0.jpg",
                "height": 421,
                "id": "868914084639293490",
                "proxy_url": "https://media.discordapp.net/attachments/868908952434384926/868914084639293490/image0.jpg",
                "size": 13859,
                "url": "https://cdn.discordapp.com/attachments/868908952434384926/868914084639293490/image0.jpg",
                "width": 421
            }
        ],
        "author": {
            "avatar": "f15b13e77a7fe5ef2d4b4d13be65d1dd",
            "discriminator": "8715",
            "id": "868302522304053248",
            "public_flags": 0,
            "username": "RedAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868914084370866187",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:54:21.357000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "f15b13e77a7fe5ef2d4b4d13be65d1dd",
            "discriminator": "8715",
            "id": "868302522304053248",
            "public_flags": 0,
            "username": "RedAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "Ok",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913936542597140",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:53:46.112000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "92f083abd028e406866677d86f4ca3d4",
            "discriminator": "8346",
            "id": "868907394569207858",
            "public_flags": 0,
            "username": "BlueAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "The password is ||su5Syb@k4||",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913804002607114",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:53:14.512000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "92f083abd028e406866677d86f4ca3d4",
            "discriminator": "8346",
            "id": "868907394569207858",
            "public_flags": 0,
            "username": "BlueAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "I sent you an encrypted note with all the details",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913676176994324",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:52:44.036000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "92f083abd028e406866677d86f4ca3d4",
            "discriminator": "8346",
            "id": "868907394569207858",
            "public_flags": 0,
            "username": "BlueAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "I'll deal with them, you just make sure this next sabotage goes to plan",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913627615363103",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:52:32.458000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "f15b13e77a7fe5ef2d4b4d13be65d1dd",
            "discriminator": "8715",
            "id": "868302522304053248",
            "public_flags": 0,
            "username": "RedAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "White is onto me… they kept calling me out last meeting",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913576629403659",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:52:20.302000+00:00",
        "tts": false,
        "type": 0
    },
    {
        "attachments": [],
        "author": {
            "avatar": "f15b13e77a7fe5ef2d4b4d13be65d1dd",
            "discriminator": "8715",
            "id": "868302522304053248",
            "public_flags": 0,
            "username": "RedAmogus"
        },
        "channel_id": "868908952434384926",
        "components": [],
        "content": "Yo",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "868913513463181332",
        "mention_everyone": false,
        "mention_roles": [],
        "mentions": [],
        "pinned": false,
        "timestamp": "2021-07-25T17:52:05.242000+00:00",
        "tts": false,
        "type": 0
    }
]
```

- on googling I found that the notes are stored in `NoteStore.sqlite` so I used `find` command to look for the file:
![](https://i.imgur.com/0L6p8j1.png)

- as per the discord chat the note is encrypted and we can see that in the database, the content of blob is binary
![](https://i.imgur.com/ZbiLjOM.png)

- after some more googling I found a nice ruby tool to decrypt and decode the encrypted notes


https://github.com/threeplanetssoftware/apple_cloud_notes_parser

- here is the usage and decryption using the password I found above


![](https://i.imgur.com/c4vIgzd.png)

- in the output html file I found the flag


![](https://i.imgur.com/OM5TSE9.png)

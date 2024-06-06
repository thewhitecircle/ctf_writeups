---
layout: load_md
title: The White Circle | Sdctf 2021 | Apollo 1337 Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Sdctf 2021
parent: sdctf_2021
category: web
challenge: Apollo 1337
tags: "web, nigamelastic, twh, api"
date: 2021-05-10T00:00:00+00:00
last_update: 2021-05-10T00:00:00+00:00
---

<h1 class="heading card-title white-text">Sdctf 2021</h1>

## Apollo 1337
> Solved By : Nigamelastic and thewhiteh4t

* [https://space.sdc.tf/](https://space.sdc.tf/)

* On view the page source we find something interesting:

```javascript
<script id="__NEXT_DATA__" type="application/json">
    {
        "props": {"pageProps":{}},
        "page":"/",
        "query":{},
        "buildId":"QQvgkUHNyaIn68Led0yAi",
        "nextExport":true,
        "autoExport":true,
        "isFallback":false
    }
</script>
```
* on performing a GET request with :

```json
{
    "GET":{
        "scheme":"https",
        "host":"space.sdc.tf",
        "filename":"/api/status",
        "remote":{
            "Address":"172.67.178.219:443"
        }
    }
}
```

* we get a response

```json
{
    "status":"health",
    "longStatus":"Healthy. All routes are functioning properly.",
    "version":"1.0.0"
}
```

* only playing around i see three endpoints

```json
{
    "status":"health",
    "longStatus":"Healthy. All routes are functioning properly.",
    "version":"1.0.0",
    "routes":[
        {
            "path":"/status",
            "status":"healthy"
        },
        {
            "path":"/rocketLaunch",
            "status":"healthy"
        },
        {
            "path":"/fuel",
            "status":"healthy"
        }
    ]
}
```

* hitting the `/fuel` endpoint we get

```json
[
    {
        "name":"west1 pump",
        "id":0
    },
    {
        "name":"east1 pump",
        "id":1
    },
    {
        "name":"south1 pump",
        "id":2
    },
    {
        "name":"north1 pump",
        "id":3
    },
    {
        "name":"west2 pump",
        "id":4
    },
    {
        "name":"lil pump",
        "id":5
    }
]
```

* hitting `/rocketLaunch` with a simple GET request 

```
request body must be json
```

* But since we have to launch the rocket we can assume that we need to POST something to the endpoint
* One of the first few things that come to mind is the **launch** keyword
* we know that endpoint expects a JSON body
* we first sent : 

```json
{
    "launch":true
}
```

![](https://i.imgur.com/qwYg1zB.png)

* we get the above response, lets specify a random rocket name since we dont know the rocket name

![](https://i.imgur.com/6ceVl3Q.png)

* endpoint tell us that **triton** is available

![](https://i.imgur.com/snndVhL.png)

* now we need a launch time but we don't know what format it expects so we tried to make it tell us again

![](https://i.imgur.com/Gp1UFin.png)

* after sending time in correct format we get this

![](https://i.imgur.com/P07DOMY.png)

* we can get a list of pumps and their IDs from /fuel endpoint

![](https://i.imgur.com/vbvshzO.png)

* interesting, now it wants a token, so we looked into the page source, we did not find anything so we started looking into the JS files of the web app and we found the token

![](https://i.imgur.com/0LSVj2V.png)

* after sending the token we get our flag!

![](https://i.imgur.com/rs1uck3.png)


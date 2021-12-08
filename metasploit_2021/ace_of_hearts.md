# ace of hearts

> Solved by: Starry-Lord

```
Port 20011
```

We had a image gallery here with 4 user galleries. 

Sarah’s gallery
John’s gallery
Ripley’s gallery
Ash’s gallery

John’s gallery was not accessible, set to private.

When querying any other gallery, we could see the URL changing like so:

```
http://172.17.15.117:20011/gallery?galleryUrl=/admin
```

I tried to call the admin gallery through local-host and got access to the admin panel, where I just had to untick a box for making John’s gallery public.

```
http://172.17.15.117:20011/gallery?galleryUrl=http://127.0.0.1:20011/admin
```

He had this flag inside:

![](https://i.imgur.com/Dnqy853.png)

> Yay an Ace!!!
>   — Starry-Lord
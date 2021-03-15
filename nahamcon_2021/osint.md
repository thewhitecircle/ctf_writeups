# OSINT

## intigriti Sponsor

https://app.intigriti.com/researcher/programs/intigriti/nahamcon2021ctf/detail

found this which has the following:

![](https://i.imgur.com/5IDtKf4.png)

the two links do not work  so we shall try deciphering this using :

https://enkhee-osiris.github.io/Decoder-JSFuck/

but we got an error stating about an illegal character

https://stackoverflow.com/questions/35657620/illegal-character-error-u200b

solution to this was easy as we just opened the challenge text in VSCode and found the `zero width space` character and removed it and the decoder now works!

----------

## INE Sponsor

Source code analysis on the link provided in the challenge

![](https://i.imgur.com/yRFuBt8.png)

----------

## Google Play Sponsor

Head over to : https://www.google.com/about/appsecurity/play-rewards/

look for a PDF link under “Theft of Sensitive Data”

Download : https://www.google.com/about/appsecurity/play-rewards/Android_app_vulnerability_classes.pdf

Search for “flag{“ in the pdf, its invisible on page 17!

![](https://i.imgur.com/rrDqLk2.png)

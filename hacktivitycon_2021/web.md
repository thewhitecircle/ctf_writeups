# web

## Titanic
> Solved By : thewhiteh4t


- In this challenge we were given a website of a company
- Two things which instantly caught attention were `URL Capture` and `Admin` buttons
- URL capture service accepts a URL and takes screenshot of the webpage


![](https://i.imgur.com/Zvb3jw1.png)



- Admin page got a login
- First idea was to try `http://localhost` and it worked


![](https://i.imgur.com/p1p8cwY.png)

![](https://i.imgur.com/jBMQpgG.png)

- This is same as the loading splash screen I saw while loading the challenge website
- Next I checked `robots.txt` and got 200 and this revealed a new path `/server-status`


![](https://i.imgur.com/MvvLwjj.png)



- Next I obviously tried to access `/server-status` and got 200 again


![](https://i.imgur.com/aIKbkBX.png)

- And in the logs you can see the login credentials!


![](https://i.imgur.com/QDhwPpP.png)

----------


## SWAGGY 
> Solved by: Taz34


- Change the server to the testing server
![](https://i.imgur.com/F5ULF8M.png)

- now authorize using the admin:admin credentials
![](https://i.imgur.com/8cLu4hz.png)

- now try and execute the request to get the flag
![](https://i.imgur.com/ladHT3D.png)


here we have the flag.


----------
# **Confidentiality**
> Solved by: Taz34


- here we have a service which lists all the items in the mentioned dir
- so i simply started looking for flag, look for elements in the /home dir
- here we have a user dir and in that we have the flag.txt
![](https://i.imgur.com/6I1hCb4.png)


now to red the file

    /home/user & cat /home/user/flag.txt
![](https://i.imgur.com/G0AlZ6s.png)


here we have the flag


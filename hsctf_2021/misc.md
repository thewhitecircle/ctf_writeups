# misc

## pallets-of-gold
> Solved by: Taz34

![](https://i.imgur.com/qDpuE48.png)

After doing some basic analysis I passed it through stegsolve and changed some planes and got the flag.

![](https://i.imgur.com/PgDP3X3.png)

```
flag{plte_chunks_remind_me_of_gifs}
```

---

## glass-windows
> Solved by: Taz34

![](https://i.imgur.com/fZHFobu.png)

Similarly as the previous i used stegsolve and changed some planes to get the flag

![](https://i.imgur.com/N8L0clK.png)

```
flag{this_is_why_i_use_premultiplied_alpha}
```

---

## c-brother-1
> Solved By : thewhiteh4t

- The challenge mentions a user like `AC01010` and `JC01010` in the HSCTF discord server
- Finding the user was easy :

![](https://i.imgur.com/bqNUuM3.png)

- In discord users can add some links of their social media for other people to see, here I found the YouTube channel of `BC01010` :

![](https://i.imgur.com/ebXCPIe.png)

- There are no videos in the channel so we cannot see the watermark directly
- At first we thought that we can get it by using the YouTube API but it only offers to set or remove a watermark from our own profile
- So I searched for channels which use watermark in their videos and landed on `Motherboard`

![](https://i.imgur.com/6j6Jqf0.png)

- Motherboard uses a `subscribe` image as their watermark, next I checked the files loaded in YouTube to check if this image was also loaded :

![](https://i.imgur.com/WWhm3sI.png)

- Now we have the URL through which the watermark is being loaded :

```
https://i.ytimg.com/an/B6PV0cvJpzlcXRG7nz6PpQ/featured_channel.jpg
```

- We know that each channel as a random alphanumeric ID so I compared this URL with the channel page URL :

```
Channel   : https://www.youtube.com/channel/UCB6PV0cvJpzlcXRG7nz6PpQ
Watermark : https://i.ytimg.com/an/B6PV0cvJpzlcXRG7nz6PpQ/featured_channel.jpg
```

- If you noticed, in the watermark URL the ID is missing two characters from the start i.e. `UC`
- Following the pattern I tried to input the channel ID of `BC01010` without the first two characters and got the watermark!

```
Channel   : https://www.youtube.com/channel/UCqZq81jZcdjAHQJ3UtAbdaA
Watermark : https://i.ytimg.com/an/qZq81jZcdjAHQJ3UtAbdaA/featured_channel.jpg
```

![](https://i.imgur.com/q0B8qxd.png)

---

## Geographic 1
> Solved By : Ava and Starry-Lord

**Image 1**

https://www.google.com/maps/@35.8980331,14.517993,0a,75y,357.04h,87.26t/data=!3m4!1e1!3m2!1sv4-Tz3_nciJr10A1On3UZA!2e0

Round up

```
35.898,14.518
```

**Image 2**

https://www.google.com/maps/@43.9376526,12.4458733,0a,75y,4.8h,110.46t/data=!3m4!1e1!3m2!1sRBMGZ4AMaKcpoI7txtv7IQ!2e0

Round up

```
43.938,12.446
```

---

## **Geographic 2**
> Solved By : Starry-Lord

**Image** **1**

```
Id. Antall József rkp.
47.504,19.044
```

https://maps.app.goo.gl/a8u8REGKuZv6LToe6


**Image 2**

```
Schwimmende Wiese
53.62,11.41
```

https://maps.app.goo.gl/Pf73iVT5pMxuTiiE7


**Image 3**

```
CERVESA ALPHA
42.569,1.489
```

https://maps.app.goo.gl/Uk4EBhjeHtNKZP996

---

## seeded randomizer
> Solved By : thewhiteh4t

* we are given a java file with two functions

```java
import java.util.Random;

public class SeededRandomizer {

	public static void display(char[] arr) {
		for (char x: arr)
			System.out.print(x);
		System.out.println();
	}

	public static void sample() {
		Random rand = new Random(79808677);
		char[] test = new char[12];
		int[] b = {9, 3, 4, -1, 62, 26, -37, 75, 83, 11, 30, 3};
		for (int i = 0; i < test.length; i++) {
			int n = rand.nextInt(128) + b[i];
			test[i] = (char)n;
		}
		display(test);
	}

	public static void main(String[] args) {
		// sample();
		// Instantiate another seeded randomizer below (seed is integer between 0 and 1000, exclusive):
		char[] flag = new char[33];
		int[] c = {13, 35, 15, -18, 88, 68, -72, -51, 73, -10, 63, 
				1, 35, -47, 6, -18, 10, 20, -31, 100, -48, 33, -12, 
				13, -24, 11, 20, -16, -10, -76, -63, -18, 118};
		for (int i = 0; i < flag.length; i++) {
			int n = (int)(Math.random() * 128) + c[i];
			flag[i] = (char)n;
		}
		display(flag);
	
	}

}
```

* sample prints "Hello World"
* random is initialized with a constant seed `79808677`

```java
Random rand = new Random(79808677);
```

* this means that the value produced by random will now be constant every time we execute the script
* our flag is in main and this time `Math.random` is used instead of previous approach
* but in the comments we can see that they have mentioned the range of seed which lies between 0 and 1000
* I modified main to bruteforce random with seeds from 0 to 1000

```java
public static void main(String[] args) {
	//sample();
	// Instantiate another seeded randomizer below (seed is integer between 0 and 1000, exclusive):
	char[] flag = new char[33];
	int[] c = {13, 35, 15, -18, 88, 68, -72, -51, 73, -10, 63, 
			1, 35, -47, 6, -18, 10, 20, -31, 100, -48, 33, -12, 
			13, -24, 11, 20, -16, -10, -76, -63, -18, 118};

	for (int x = 0; x < 1001; x++) {
		Random rand = new Random(x);
		for (int i = 0; i < flag.length; i++) {
			int n = rand.nextInt(128) + c[i];
			flag[i] = (char)n;
		}
		display(flag);
	}	
}
```

Output :

![](https://i.imgur.com/ojD1yuq.png)
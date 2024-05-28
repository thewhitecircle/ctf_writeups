---
layout: load_md
title: The White Circle | Nahamcon 2024 | Kitty Kitty Bang Bang Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Nahamcon 2024
parent: nahamcon_2024
category: mobile
challenge: Kitty Kitty Bang Bang
tags: "mobile, legend, android"
---

<h1 class="heading card-title white-text">Nahamcon 2024</h1>

## Kitty Kitty Bang Bang
> Solved by: Legend

We are given an APK file.

I loaded it in Android Studio's Virtual Device Manager. Nothing is really happening in the app if you tap/click the animation with an image is running and saying "BANG!" along with sound.

The challenge description gave a hint that something might be happening behind the screen.

So I checked the code of the APK using JD-GUI. After opening it I searched for `flag{` since its an easy challenge I thought it might be hidden in the source.

In `MainActivity.class` I saw that `flag{` was mentioned and in `onCreate$lambda$0` it was getting shown.

```java
private static final boolean onCreate$lambda$0(MainActivity paramMainActivity, View paramView, MotionEvent paramMotionEvent) {
   Intrinsics.checkNotNullParameter(paramMainActivity, "this$0");
   Log.i("kitty kitty bang bang", "Listening for taps...");
   if (paramMotionEvent.getAction() == 0) {
     Log.i("kitty kitty bang bang", "Screen tapped!");
     paramMainActivity.showOverlayImage();
     paramMainActivity.playSound(R.raw.bang);
     Log.i("kitty kitty bang bang", "BANG!");
     Log.i("kitty kitty bang bang", "flag{" + paramMainActivity.stringFromJNI() + '}');
   } 
   return true;
 }
```

Basically what is happening here is that the app is waiting and listening for taps/click on the screen. If someone tap/click then that "BANG!" is shown as told earlier.

But two interesting thing are happening here it is using `Log.i` to log that also and more interestingly the `flag{` is algo getting logged if someone taps/click.

So now I used `adb logcat` to generate logs. And from Virtual Device Manager I tapped/clicked on screen for POC.

And indeed the flag was there.

```
flag{f9028245dd46eedbf9b4f8861d73ae0f}
```


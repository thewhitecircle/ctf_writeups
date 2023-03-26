# re

## Shattered Tablet
> Solved by Starry-Lord


![](https://i.imgur.com/oPaxFQs.png)


This challenge gave an ELF file so after basic identification, I tried to run it. When you run the file the tablet asks for an input and returns “no, not that”:

![](https://i.imgur.com/elH0lhM.png)


I decided to fire up ghidra, hopefully looking for what the expected input could be, what it would be compared against or anything relevant. Sure enough, by checking the main function, I found this code “explicitly” showing it was expecting the flag as input.


    undefined8 main(void)
    
    {
      undefined8 local_48;
      undefined8 local_40;
      undefined8 local_38;
      undefined8 local_30;
      undefined8 local_28;
      undefined8 local_20;
      undefined8 local_18;
      undefined8 local_10;
      
      local_48 = 0;
      local_40 = 0;
      local_38 = 0;
      local_30 = 0;
      local_28 = 0;
      local_20 = 0;
      local_18 = 0;
      local_10 = 0;
      printf("Hmmmm... I think the tablet says: ");
      fgets((char *)&local_48,0x40,stdin);
      if (((((((((local_30._7_1_ == 'p') && (local_48._1_1_ == 'T')) && (local_48._7_1_ == 'k')) &&
              ((local_28._4_1_ == 'd' && (local_40._3_1_ == '4')))) &&
             ((local_38._4_1_ == 'e' && ((local_40._2_1_ == '_' && ((char)local_48 == 'H')))))) &&
            (local_28._2_1_ == 'r')) &&
           ((((local_28._3_1_ == '3' && (local_30._1_1_ == '_')) && (local_48._2_1_ == 'B')) &&
            (((local_30._5_1_ == 'r' && (local_48._3_1_ == '{')) &&
             ((local_30._2_1_ == 'b' && ((local_48._5_1_ == 'r' && (local_40._5_1_ == '4')))))))))) &&
          (((local_30._6_1_ == '3' &&
            (((local_38._3_1_ == 'v' && (local_40._4_1_ == 'p')) && (local_28._1_1_ == '1')))) &&
           (((local_30._3_1_ == '3' && (local_38._1_1_ == 'n')) &&
            (((local_48._4_1_ == 'b' && (((char)local_28 == '4' && (local_40._1_1_ == 'n')))) &&
             ((char)local_38 == ',')))))))) &&
         ((((((((char)local_40 == '3' && (local_48._6_1_ == '0')) && (local_38._7_1_ == 't')) &&
             ((local_40._7_1_ == 't' && ((char)local_30 == '0')))) &&
            ((local_40._6_1_ == 'r' && ((local_28._5_1_ == '}' && (local_38._5_1_ == 'r')))))) &&
           (local_38._6_1_ == '_')) && ((local_38._2_1_ == '3' && (local_30._4_1_ == '_')))))) {
        puts("Yes! That\'s right!");
      }
      else {
        puts("No... not that");
      }
      return 0;
    }

From this code we can understand that chars get reassembled following the order of the undefined8 locals to form the flag. It starts at ((char)local_48 == 'H') and local_28._5_1_ == '}’ is where it ends, so after a bit of tidying we could retrieve the following string:


    HTB{br0k3n_4p4rt,n3ver_t0_b3_r3p41r3d}
    ((char)local_48 == 'H')
    (local_48._1_1_ == 'T'))
    (local_48._2_1_ == 'B'))
    (local_48._3_1_ == '{'))
    local_48._4_1_ == 'b'
    ((local_48._5_1_ == 'r'
    (local_48._6_1_ == '0'))
    (local_48._7_1_ == 'k'))
            ...


----------
## Hunting License 
> Solved by Avantika(@iamavu)
    
> STOP! Adventurer, have you got an up to date relic hunting license? If you don't, you'll need to take the exam again before you'll be allowed passage into the spacelanes!

We are provided a binary called `license`  and a docker instance support to get the flag from the server

This challenge just essentially tests out your basics so pretty straight-forward walkthrough

When you run the binary you are greeted with a following prompt

![](https://i.imgur.com/awffQL5.png)


press `y` and move forward 

![](https://i.imgur.com/Xr5NE86.png)


so essentially we have to crack some passwords, alright time to `CTRL+C` and check what binary is and what it does
First thing I like to do is run `checksec` to check how binary is configured
We get following and the the thing that surprise is it doesn’t have PIE enabled

![](https://i.imgur.com/oIRrv7L.png)


Which means the binary will be loaded at same place in memory everytime and it’s not randomized every time you load the binary.
Coolio!
Let’s fire up the binary-dragon, ghidra and check what’s the code doing.
We got our function names intact, so going to the main function

![](https://i.imgur.com/PQ7vAMw.png)


it checks if we are ready or not and then runs `exam()` function which is the juicy function 
let’s have a look at the `exam()` function 

![](https://i.imgur.com/FlUGwCC.png)


well, looks pretty big but it’s checking with `strcmp()` if we enter the right value or not
first one is literally in the code, so the first password is pretty simple - `PasswordNumeroUno`

Second password is something reversed, now I could technically reverse it from the code/ghidra but let’s do the smart work and run the binary in gdb (pwndbg) and set a breakpoint at the address of where it asks us for the second password.
How and why can we do that, simple - NO PIE (read more about it if you are unfamiliar)

Copy the address from the ghidra for the statement where it asks us for the password, set the breakpoint (`break *0xAddress`) and hit run!

![](https://i.imgur.com/Jp1wBMz.png)


As we can see in the disassembly we can see the register holding the second compare value which is `0wTdr0wss4P`, but reversed i.e. `P4ssw0rdTw0` which is our second password.

Let’s have look at final password by the same trick we did above. Find the address from the ghidra, set breakpoint, and hit run and see the stack this time!

![](https://i.imgur.com/x5z2aC5.png)


As we can see the rsp pointing towards the address containing our password which is `ThirdAndFinal!!!`

Which does means that we solved but we need to do some couple of extra answers to question to solve it.

![](https://i.imgur.com/A0vFJjD.png)


Let’s go one by one

1. ELF, as this is a linux binary
2. Check architecture by running `file` command on this binary
3. run `ldd` to see the libraries used for various purposes
4. Get the address of the main function from ghidra
5. Check main function’s disassembly in ghidra and see how many puts call are there
6. Password One, we have it
7. Reversed form of Password Two
8. Password Two, we have it
9. Check the `exam()` function and in the `xor` statement, the fourth parameter in decimal form is our answer to this question
10. Password Three, we have it

We get our flag - `HTB{l1c3ns3_4cquir3d-hunt1ng_t1m3!}`
as always keep hacking : D


----------
##  Needle in a Haystack 
> Solved by Legend

Challenge description


> You've obtained an ancient alien Datasphere, containing categorized and sorted recordings of every word in the forgotten intergalactic common language. Hidden within it is the password to a tomb, but the sphere has been worn with age and the search function no longer works, only playing random recordings. You don't have time to search through every recording - can you crack it open and extract the answer?

In this challenge we are given a ELF 64-bit executable file.

![](https://i.imgur.com/cLU2TWx.png)

![](https://i.imgur.com/N3xkosj.png)


The challenge hint said that the password is `hidden within` so first thing I did was run the `strings` command to check if something is there and found the flag.

![](https://i.imgur.com/dbJZHa3.png)

----------
##  She Shells C Shells
> Solved by warlordsam

Challenge description

![](https://i.imgur.com/0tDZYcp.png)


In this challenge we are given a ELF 64-bit LSB pie executable (not stripped).

![](https://i.imgur.com/AVU3W06.png)


Using ghidra , we can get the flag. Open the binary in  CodeBrowser and find the function called *func_flag.* 

Observing the code snippet from the func_flag ; we can see that  it uses memcmp to compare the user input and stored bits.

      fgets((char *)&local_118,0x100,stdin);
      for (local_c = 0; local_c < 0x4d; local_c = local_c + 1) {
        *(byte *)((long)&local_118 + (long)(int)local_c) =
             *(byte *)((long)&local_118 + (long)(int)local_c) ^ m1[(int)local_c];
      }
      local_14 = memcmp(&local_118,t,0x4d);
      if (local_14 == 0) {
        for (local_10 = 0; local_10 < 0x4d; local_10 = local_10 + 1) {
          *(byte *)((long)&local_118 + (long)(int)local_10) =
               *(byte *)((long)&local_118 + (long)(int)local_10) ^ m2[(int)local_10];
        }
        printf("Flag: %s\n",&local_118);
        uVar1 = 0;
      }
      else {
        uVar1 = 0xffffffff;
      }
      return uVar1;
    }
    

There are three arrays *m1, m2 and t* that we need to look at. The user input is stored in *m1* array and compared with the *t array* which is a stored block of password starting at memory address:0x55fb06ae0200 to check if we got the correct input. It performs XOR on each bit of both arrays.

To get the flag; We can directly see hex values of *t* and *m2* in *memcmp*. Total length is 77 characters. Each bit of *t  and  m2* undergo XOR  to form a flag with format of  HTB{ 

Copying all the hex values present in *t* and *m2;* and performing XOR using online calculator: https://xor.pw/ we can obtain the flag.

For Example: bits in t and m2 respectively are:

1. t: 2c 4a b7 
2. m2 : 64 1e f5 

Flag : HTB....so on 



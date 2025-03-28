# re

## Encrypted Scroll 
> Solved by avantika


![](https://i.imgur.com/KqQ9Bw1.png)


running the chanllege, we are asked for a input, giving the wrong input, we see it rejects us (sad life)
let us decompile the challenge using IDA


    int __fastcall main(int argc, const char **argv, const char **envp)
    {
      char v4\[56]; // [rsp+0h\] [rbp-40h] BYREF
      unsigned __int64 v5; // \[rsp+38h\] [rbp-8h]
    
      v5 = __readfsqword(0x28u);
      anti_debug(argc, argv, envp);
      display_scroll();
      printf(format);
      __isoc99_scanf("%49s", v4);
      decrypt_message(v4);
      return 0;
    }  

this is the main function, which seems to get our input and then decrypt it using `decrypt_message()`, lets decompile that function


    unsigned __int64 __fastcall decrypt_message(const char *a1)
    {
      int i; // \[rsp+1Ch\] [rbp-34h]
      char s2\[40]; // [rsp+20h\] [rbp-30h] BYREF
      unsigned __int64 v4; // \[rsp+48h\] [rbp-8h]
    
      v4 = __readfsqword(0x28u);
      strcpy(s2, "IUC|t2nqm4`gm5h`5s2uin4u2d~");
      for ( i = 0; s2[i]; ++i )
        --s2[i];
      if ( !strcmp(a1, s2) )
        puts("The Dragon's Heart is hidden beneath the Eternal Flame in Eldoria.");
      else
        puts("The scroll remains unreadable... Try again.");
      return v4 - __readfsqword(0x28u);
    }

we have a string, which goes under certain operation and then is compared with our input, i have written a python script to reverse that operation and get us the flag!


    enc = list('IUC|t2nqm4`gm5h`5s2uin4u2d~')
    
    flag = []
    for _ in enc:
        flag.append(chr(ord(_) - 1))
    print("".join(flag))

running this script, we get the flag
`HTB{s1mpl3_fl4g_4r1thm3t1c}` is our flag.


----------
## Sealed Runes
> Solved by avantika


![](https://i.imgur.com/wuPRcFZ.png)


running the challenege, we are asked for input and if given wrong one, it rejects us (sad life). lets try opening up the binary in IDA and have a look at decompiled code.


    int __fastcall main(int argc, const char **argv, const char **envp)
    {
      char input\[56]; // [rsp+0h\] [rbp-40h] BYREF
      unsigned __int64 v5; // \[rsp+38h\] [rbp-8h]
    
      v5 = __readfsqword(0x28u);
      anti_debug(argc, argv, envp);
      display_rune();
      puts(a134m_0);
      printf("Enter the incantation to reveal its secret: ");
      __isoc99_scanf("%49s", input);
      check_input(input);
      return 0;
    }

this is the main function, which simply asks us for our inputs. de-compiling the function `check_input()` we get the following code


    void __fastcall check_input(const char *a1)
    {
      __int64 v1; // rax
      char *s2; // \[rsp+18h\] [rbp-8h]
    
      s2 = (char *)decode_secret();
      if ( !strcmp(a1, s2) )
      {
        puts(a132mtheRuneGlo);
        v1 = decode_flag();
        printf("\x1B[1;33m%s\x1B[0m\n", (const char *)(v1 + 1));
      }
      else
      {
        puts("\x1B[1;31mThe rune rejects your words... Try again.\x1B[0m");
      }
      free(s2);
    }

from what it seems, it decodes the flag and then prints it if we input the right string, lets look at the `decode_flag()`function


    const char *decode_flag()
    {
      const char *v1; // \[rsp+8h\] [rbp-8h]
    
      v1 = (const char *)base64_decode(flag);
      reverse_str(v1);
      return v1;
    }

looks like we found the `flag`, lets us check the string


![](https://i.imgur.com/MKQJh1X.png)


base64 decoding this string (`LmB9ZDNsNDN2M3JfYzFnNG1fM251cntCVEhgIHNpIGxsZXBzIHRlcmNlcyBlaFQ=` )gives us a string which looks like if reversed would gives us the flag, so cyberchef it is!


![](https://i.imgur.com/d7MCFlA.png)


`HTB{run3_m4g1c_r3v34l3d}` is our flag.


----------
## ImpossiMaze
> Solved by avantika

Running the binary gives us weird screen, on our terminal with gibberish everywhere, so i was quite confused.

Decompiling `main()` gave me this


    __int64 __fastcall main(int a1, char **a2, char **a3)
    {
      int v3; // ebx
      int i; // ebx
      unsigned int v5; // r12d
      int v6; // ebx
      unsigned int v7; // ebp
      int v8; // eax
      int v9; // r14d
      int *v10; // rbp
      int j; // ebx
      int v12; // edx
      int v14; // \[rsp+Ch\] [rbp-6Ch]
      int v15; // \[rsp+10h\] [rbp-68h]
      int v16; // \[rsp+14h\] [rbp-64h]
      char v17\[24]; // [rsp+20h\] [rbp-58h] BYREF
      unsigned __int64 v18; // \[rsp+38h\] [rbp-40h]
    
      v18 = __readfsqword(0x28u);
      initscr();
      cbreak();
      noecho();
      curs_set(0);
      keypad(stdscr, 1);
      v3 = getmaxy(stdscr);
      v15 = getmaxx(stdscr) / 2;
      v16 = v3 / 2;
      for ( i = 0; i != 113; i = wgetch(stdscr) )
      {
        v9 = getmaxy(stdscr);
        v14 = getmaxx(stdscr);
        if ( i == 260 )
        {
          v15 -= v15 > 1;
        }
        else if ( i > 260 )
        {
          v15 += i == 261;
        }
        else if ( i == 258 )
        {
          ++v16;
        }
        else if ( i == 259 )
        {
          v16 -= v16 > 1;
        }
        werase(stdscr);
        wattr_on(stdscr, 0x100000uLL, 0LL);
        wborder(stdscr, 0LL, 0LL, 0LL, 0LL, 0LL, 0LL, 0LL, 0LL);
        if ( v14 > 2 )
        {
          v5 = 1;
          do
          {
            v7 = 1;
            if ( v9 > 2 )
            {
              do
              {
                v8 = sub_1249(v5, v7);
                if ( v8 > 60 )
                {
                  v6 = (unsigned int)(v8 - 61) < 0x78 ? 32 : 86;
                }
                else
                {
                  LOBYTE(v6) = 65;
                  if ( v8 <= 30 )
                    v6 = (unsigned int)v8 < 0x1F ? -37 : 86;
                }
                if ( wmove(stdscr, v7, v5) != -1 )
                  waddch(stdscr, (unsigned int)(char)v6);
                ++v7;
              }
              while ( v7 != v9 - 1 );
            }
            ++v5;
          }
          while ( v14 - 1 != v5 );
        }
        wattr_off(stdscr, 0x100000uLL, 0LL);
        wattr_on(stdscr, 0x200000uLL, 0LL);
        if ( wmove(stdscr, v16, v15) != -1 )
          waddch(stdscr, 0x58uLL);
        wattr_off(stdscr, 0x200000uLL, 0LL);
        snprintf(v17, 0x10uLL, "%d:%d", v9, v14);
        if ( wmove(stdscr, 0, 0) != -1 )
          waddnstr(stdscr, v17, -1);
        if ( v9 == 13 && v14 == 37 )
        {
          wattr_on(stdscr, 0x80000uLL, 0LL);
          wattr_on(stdscr, 0x200000uLL, 0LL);
          v10 = (int *)&unk_40C0;
          for ( j = 6; j != 30; ++j )
          {
            v12 = j;
            if ( wmove(stdscr, 6, v12) != -1 )
              waddch(stdscr, byte_4120[*v10]);
            ++v10;
          }
          wattr_off(stdscr, 0x200000uLL, 0LL);
          wattr_off(stdscr, 0x80000uLL, 0LL);
        }
      }
      endwin();
      return 0LL;
    }

looking at this, looks like it is using `ncurses` lib to print stuff on screen and this variable
`v9 = getmaxy(stdscr);` is max y axis value of the terminal size and `v14 = getmaxx(stdscr);` is the max x axis value of terminal size.


    if ( v9 == 13 && v14 == 37 )
        {
          wattr_on(stdscr, 0x80000uLL, 0LL);
          wattr_on(stdscr, 0x200000uLL, 0LL);
          v10 = (int *)&unk_40C0;
          for ( j = 6; j != 30; ++j )
          {
            v12 = j;
            if ( wmove(stdscr, 6, v12) != -1 )
              waddch(stdscr, byte_4120[*v10]);
            ++v10;
          }
          wattr_off(stdscr, 0x200000uLL, 0LL);
          wattr_off(stdscr, 0x80000uLL, 0LL);
        }

now, this part of the code, is the Y is 13 and X is 37, it will print whatever is stored in `byte_4120` on the screen, lets do that now.
we will re-size the terminal to match our size of 13 by 37


![](https://i.imgur.com/7Y3Cbtb.png)


`HTB{th3_curs3_is_brok3n}` is our flag.


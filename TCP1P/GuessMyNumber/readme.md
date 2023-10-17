# Guess My Number

### Text: My friend said if i can guess the right number, he will give me something. Can you help me?

nc ctf.tcp1p.com 7331

&nbsp;

Score: 100

&nbsp;

Included Files: [dist.zip](dist.zip)


## Writeup

In this challenge you are given an address of a program as well as the executable for that program. When running the program you are met with this text.

Program output test
```
=======              WELCOME TO GUESSING GAME               =======
======= IF YOU CAN GUESS MY NUMBER, I'LL GIVE YOU THE FLAG  =======

Your Guess : 
```

When guessing a number you are met with the message 

```
=======              WELCOME TO GUESSING GAME               =======
======= IF YOU CAN GUESS MY NUMBER, I'LL GIVE YOU THE FLAG  =======

Your Guess : 8
Wrong, Try again harder!
```

I started by checking if there were any symbols exported by the executable. I used the *nm* command to list all symbols.

Part of symbol dump output
```
00000000000012e2 T main
                 U printf@GLIBC_2.2.5
                 U puts@GLIBC_2.2.5
                 U rand@GLIBC_2.2.5
0000000000001130 t register_tm_clones
                 U srand@GLIBC_2.2.5
0000000000004058 B stdout@GLIBC_2.2.5
                 U system@GLIBC_2.2.5
000000000000122b T vuln
```

The last function *vuln* looks like it will be a useful function. The next step was to run this program using GDB. I set a breakpoint at the function vuln.

```as
   0x000055555555522b <+0>:     push   %rbp
   0x000055555555522c <+1>:     mov    %rsp,%rbp
=> 0x000055555555522f <+4>:     sub    $0x10,%rsp
   0x0000555555555233 <+8>:     movl   $0x0,0x2e27(%rip)        # 0x555555558064 <key>
   0x000055555555523d <+18>:    mov    $0x539,%edi
   0x0000555555555242 <+23>:    call   0x555555555060 <srand@plt>
   0x0000555555555247 <+28>:    call   0x5555555550b0 <rand@plt>
   0x000055555555524c <+33>:    mov    %eax,-0x4(%rbp)
   0x000055555555524f <+36>:    lea    0xe67(%rip),%rax        # 0x5555555560bd
   0x0000555555555256 <+43>:    mov    %rax,%rdi
   0x0000555555555259 <+46>:    mov    $0x0,%eax
   0x000055555555525e <+51>:    call   0x555555555050 <printf@plt>
   0x0000555555555263 <+56>:    mov    0x2dee(%rip),%rax        # 0x555555558058 <stdout@GLIBC_2.2.5>
   0x000055555555526a <+63>:    mov    %rax,%rdi
   0x000055555555526d <+66>:    call   0x555555555070 <fflush@plt>
   0x0000555555555272 <+71>:    lea    0x2deb(%rip),%rax        # 0x555555558064 <key>
   0x0000555555555279 <+78>:    mov    %rax,%rsi
   0x000055555555527c <+81>:    lea    0xe48(%rip),%rax        # 0x5555555560cb
   0x0000555555555283 <+88>:    mov    %rax,%rdi
   0x0000555555555286 <+91>:    mov    $0x0,%eax
   0x000055555555528b <+96>:    call   0x555555555090 <__isoc99_scanf@plt>
   0x0000555555555290 <+101>:   mov    -0x4(%rbp),%eax
   0x0000555555555293 <+104>:   lea    0x1467f3(%rax),%edx
   0x0000555555555299 <+110>:   mov    0x2dc5(%rip),%eax        # 0x555555558064 <key>
   0x000055555555529f <+116>:   xor    %edx,%eax
   0x00005555555552a1 <+118>:   cmp    $0xcafebabe,%eax
   0x00005555555552a6 <+123>:   jne    0x5555555552d0 <vuln+165>
   0x00005555555552a8 <+125>:   lea    0xe1f(%rip),%rax        # 0x5555555560ce
   0x00005555555552af <+132>:   mov    %rax,%rdi
   0x00005555555552b2 <+135>:   call   0x555555555030 <puts@plt>
   0x00005555555552b7 <+140>:   lea    0xe2d(%rip),%rax        # 0x5555555560eb
   0x00005555555552be <+147>:   mov    %rax,%rdi
   0x00005555555552c1 <+150>:   call   0x555555555040 <system@plt>
   0x00005555555552c6 <+155>:   mov    $0x0,%edi
   0x00005555555552cb <+160>:   call   0x5555555550a0 <exit@plt>
   0x00005555555552d0 <+165>:   lea    0xe21(%rip),%rax        # 0x5555555560f8
   0x00005555555552d7 <+172>:   mov    %rax,%rdi
   0x00005555555552da <+175>:   call   0x555555555030 <puts@plt>
   0x00005555555552df <+180>:   nop
   0x00005555555552e0 <+181>:   leave
   0x00005555555552e1 <+182>:   ret
```

The first interesting part of this function is calling the srand and rand functions.

```as
   0x000055555555523d <+18>:    mov    $0x539,%edi
   0x0000555555555242 <+23>:    call   0x555555555060 <srand@plt>
   0x0000555555555247 <+28>:    call   0x5555555550b0 <rand@plt>
   0x000055555555524c <+33>:    mov    %eax,-0x4(%rbp)
```

The code is setting a seed for its random number generator. In this case it is always using the same number *0x539*. It is then calling the rand() function and storing the result in some local variable stored at -0x4(%rbp).

```as
   0x0000555555555272 <+71>:    lea    0x2deb(%rip),%rax        # 0x555555558064 <key>
   0x0000555555555279 <+78>:    mov    %rax,%rsi
   0x000055555555527c <+81>:    lea    0xe48(%rip),%rax        # 0x5555555560cb
   0x0000555555555283 <+88>:    mov    %rax,%rdi
   0x0000555555555286 <+91>:    mov    $0x0,%eax
   0x000055555555528b <+96>:    call   0x555555555090 <__isoc99_scanf@plt>
```

In the enxt section of code you can see in the first instruction we are loading the pointer to some variable named key as well as another value. Both of these parameters then become the arguments for the scanf function which means that the user's input will be stored in the buffer key.

GDB output of first parameter in scanf
```as
0x5555555560cb: 37 '%'  100 'd' 0 '\000'
```

The next section provides a computation using the value received from the "random" number generator as well as the input that we provided.
```as
   0x0000555555555290 <+101>:   mov    -0x4(%rbp),%eax
   0x0000555555555293 <+104>:   lea    0x1467f3(%rax),%edx
   0x0000555555555299 <+110>:   mov    0x2dc5(%rip),%eax        # 0x555555558064 <key>
   0x000055555555529f <+116>:   xor    %edx,%eax
   0x00005555555552a1 <+118>:   cmp    $0xcafebabe,%eax
   0x00005555555552a6 <+123>:   jne    0x5555555552d0 <vuln+165>
```
It first adds the random number to the value 0x1467f3 in the first two instructions. It then XORs the result with the number we provided. This result is then compared to the value 0xcafebabe. If the numbers are equal we do not take the jump and instead execute the following puts and system methods. 

```as
   0x00005555555552a8 <+125>:   lea    0xe1f(%rip),%rax        # 0x5555555560ce
   0x00005555555552af <+132>:   mov    %rax,%rdi
   0x00005555555552b2 <+135>:   call   0x555555555030 <puts@plt>
   0x00005555555552b7 <+140>:   lea    0xe2d(%rip),%rax        # 0x5555555560eb
   0x00005555555552be <+147>:   mov    %rax,%rdi
   0x00005555555552c1 <+150>:   call   0x555555555040 <system@plt>
```


Looking at the contents of the pointer provided to the system command it looks like the program will execute the shell command "cat flag.txt"

Contents
```as
0x5555555560eb: 99 'c'  97 'a'  116 't' 32 ' '  102 'f' 108 'l' 97 'a'  103 'g'
0x5555555560f3: 46 '.'  116 't' 120 'x' 116 't' 0 '\000'
```

All we need to do now is to find the correct input that will cause the program not to take the jump. Using the equation *key ^ 0x1467f3 + 0x118561dc = 0xcafebabe*. The solution to this is *key = 3682327394*. By inputting this number you will get the flag.


```
=======              WELCOME TO GUESSING GAME               =======
======= IF YOU CAN GUESS MY NUMBER, I'LL GIVE YOU THE FLAG  =======

Your Guess : 3682327394
TCP1P{r4nd0m_1s_n0t_th4t_r4nd0m_r19ht?_946f38f6ee18476e7a0bff1c1ed4b23b}
```

```
## Flag: TCP1P{r4nd0m_1s_n0t_th4t_r4nd0m_r19ht?_946f38f6ee18476e7a0bff1c1ed4b23b}
```
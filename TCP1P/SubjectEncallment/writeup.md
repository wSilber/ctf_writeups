# Subject Encallment

### Text: If there's something strange. In your neighborhood. Who you gonna call?

&nbsp;

Score: 100

&nbsp;

Included Files: [chall](chall)

---
 ## Writeup
In this challenge you are given a program that contains the flag. The flag itself is encrypted and would be very time consuming to decrypt the flag on your own. Luckily the program contains a method printFlag() which will automatically decrypt and print out the flag.

Decompiled printFlag method
```
void printFlag(void)

{
  long lVar1;
  undefined8 *puVar2;
  undefined8 local_b8;
  undefined4 local_b0;
  undefined4 local_ac;
  undefined4 local_a8;
  undefined4 local_a4;
  undefined4 local_a0;
  undefined4 local_9c;
  undefined4 local_98;
  undefined4 local_94;
  undefined4 local_90;
  undefined4 local_8c;
  undefined4 local_88;
  undefined4 local_84;
  undefined4 local_80;
  undefined4 local_7c;
  undefined4 local_78;
  undefined4 local_74;
  undefined4 local_70;
  undefined4 local_6c;
  undefined4 local_68;
  undefined4 local_64;
  undefined4 local_60;
  undefined4 local_5c;
  undefined4 local_58;
  undefined4 local_54;
  undefined4 local_50;
  undefined4 local_4c;
  undefined4 local_48;
  undefined4 local_44;
  undefined4 local_40;
  undefined4 local_3c;
  undefined4 local_38;
  undefined4 local_34;
  undefined4 local_30;
  undefined4 local_2c;
  undefined4 local_28;
  undefined4 local_24;
  int local_c;
  
  puVar2 = &local_b8;
  for (lVar1 = 0x14; lVar1 != 0; lVar1 = lVar1 + -1) {
    *puVar2 = 0;
    puVar2 = puVar2 + 1;
  }
  local_b8._0_4_ = 0x41;
  local_b8._4_4_ = 10;
  local_b0 = 0x4c;
  local_ac = 0x78;
  local_a8 = 0x45;
  local_a4 = 0x32;
  local_a0 = 0x69;
  local_9c = 99;
  local_98 = 0x47;
  local_94 = 0x11;
  local_90 = 0x7a;
  local_8c = 0x5a;
  local_88 = 0x30;
  local_84 = 0x4a;
  local_80 = 0x27;
  local_7c = 0x74;
  local_78 = 0x58;
  local_74 = 0x40;
  local_70 = 0x21;
  local_6c = 0x133;
  local_68 = 0x16;
  local_64 = 0x56;
  local_60 = 0x26;
  local_5c = 0x59;
  local_58 = 0x2a;
  local_54 = 0x74;
  local_50 = 0x25;
  local_4c = 0x6d;
  local_48 = 0xaa;
  local_44 = 0xc;
  local_40 = 0x52;
  local_3c = 0x16;
  local_38 = 300;
  local_34 = 0x28;
  local_30 = 0x4c;
  local_2c = 0x16;
  local_28 = 0x40;
  local_24 = 0x39;
  phase1();
  phase2();
  phase3();
  phase4();
  phase5();
  phase6();
  phase7();
  phase8();
  phase9();
  phase10();
  phase11();
  phase12();
  phase13();
  phase14();
  phase9();
  phase2();
  phase12();
  phase2();
  phase6();
  phase7();
  phase8();
  phase11();
  for (local_c = 0; local_c < 0x26; local_c = local_c + 1) {
    putchar(*(uint *)(keys + (long)local_c * 4) ^ *(uint *)((long)&local_b8 + (long)local_c * 4));
  }
  puts("");
  return;
}
```

Although we could reverse the decypting process to get the flag, this would be very timeconsuming and it would be easier to just run this method directly. Unfortunately this method is not called anywhere in the program. An easy way to call this method is to run this program in GDB and jump to the address of the printFlag() method. This can be acheived by setting a breakpoint at main and using the jump command to jump to the printFlag() method. After executing the printFlag() method the flag will be printed to the terminal.

GDB command output
```
(gdb) b main
Breakpoint 1 at 0x1e26
(gdb) b printFlag
Breakpoint 2 at 0x1ba9
(gdb) r
Starting program: /mnt/c/Users/silbe/Documents/ctfs/tcp/chall (1)
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x0000555555555e26 in main ()
(gdb) jump printFlag
Continuing at 0x555555555ba9.

Breakpoint 2, 0x0000555555555ba9 in printFlag ()
(gdb) c
Continuing.
TCP1P{here_my_number_so_call_me_maybe}
```

## Flag: TCP1P{here_my_number_so_call_me_maybe}
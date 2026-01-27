Date : 25 January 2026

This is a x86_64 binary composed of 6 levels where we have to reverse engineer the binary to find the 6 passwords.

when running `file` on the binary :
```
bomb: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=7dd166a66acce52fc6103bbf61a0c32b7e667841, for GNU/Linux 3.2.0, with debug_info, not stripped
```

Disassembly view of the main function using `objdump` :
```
0000000000001449 <main>:
    1449:       f3 0f 1e fa             endbr64
    144d:       53                      push   %rbx
    144e:       83 ff 01                cmp    $0x1,%edi
    1451:       0f 84 f8 00 00 00       je     154f <main+0x106>
    1457:       48 89 f3                mov    %rsi,%rbx
    145a:       83 ff 02                cmp    $0x2,%edi
    145d:       0f 85 21 01 00 00       jne    1584 <main+0x13b>
    1463:       48 8b 7e 08             mov    0x8(%rsi),%rdi
    1467:       48 8d 35 96 1b 00 00    lea    0x1b96(%rip),%rsi        # 3004 <_IO_stdin_used+0x4>
    146e:       e8 6d fe ff ff          call   12e0 <fopen@plt>
    1473:       48 89 05 1e 42 00 00    mov    %rax,0x421e(%rip)        # 5698 <infile>
    147a:       48 85 c0                test   %rax,%rax
    147d:       0f 84 df 00 00 00       je     1562 <main+0x119>
    1483:       e8 a9 06 00 00          call   1b31 <initialize_bomb>
    1488:       48 8d 3d f9 1b 00 00    lea    0x1bf9(%rip),%rdi        # 3088 <_IO_stdin_used+0x88>
    148f:       e8 6c fd ff ff          call   1200 <puts@plt>
    1494:       48 8d 3d 2d 1c 00 00    lea    0x1c2d(%rip),%rdi        # 30c8 <_IO_stdin_used+0xc8>
    149b:       e8 60 fd ff ff          call   1200 <puts@plt>
    14a0:       e8 b1 07 00 00          call   1c56 <read_line>
    14a5:       48 89 c7                mov    %rax,%rdi
    14a8:       e8 fa 00 00 00          call   15a7 <phase_1>
    14ad:       e8 ec 08 00 00          call   1d9e <phase_defused>
    14b2:       48 8d 3d 3f 1c 00 00    lea    0x1c3f(%rip),%rdi        # 30f8 <_IO_stdin_used+0xf8>
    14b9:       e8 42 fd ff ff          call   1200 <puts@plt>
    14be:       e8 93 07 00 00          call   1c56 <read_line>
    14c3:       48 89 c7                mov    %rax,%rdi
    14c6:       e8 00 01 00 00          call   15cb <phase_2>
    14cb:       e8 ce 08 00 00          call   1d9e <phase_defused>
    14d0:       48 8d 3d 66 1b 00 00    lea    0x1b66(%rip),%rdi        # 303d <_IO_stdin_used+0x3d>
    14d7:       e8 24 fd ff ff          call   1200 <puts@plt>
    14dc:       e8 75 07 00 00          call   1c56 <read_line>
    14e1:       48 89 c7                mov    %rax,%rdi
    14e4:       e8 50 01 00 00          call   1639 <phase_3>
    14e9:       e8 b0 08 00 00          call   1d9e <phase_defused>
    14ee:       48 8d 3d 66 1b 00 00    lea    0x1b66(%rip),%rdi        # 305b <_IO_stdin_used+0x5b>
    14f5:       e8 06 fd ff ff          call   1200 <puts@plt>
    14fa:       e8 57 07 00 00          call   1c56 <read_line>
    14ff:       48 89 c7                mov    %rax,%rdi
    1502:       e8 44 02 00 00          call   174b <phase_4>
    1507:       e8 92 08 00 00          call   1d9e <phase_defused>
    150c:       48 8d 3d 15 1c 00 00    lea    0x1c15(%rip),%rdi        # 3128 <_IO_stdin_used+0x128>
    1513:       e8 e8 fc ff ff          call   1200 <puts@plt>
    1518:       e8 39 07 00 00          call   1c56 <read_line>
    151d:       48 89 c7                mov    %rax,%rdi
    1520:       e8 9f 02 00 00          call   17c4 <phase_5>
    1525:       e8 74 08 00 00          call   1d9e <phase_defused>
    152a:       48 8d 3d 39 1b 00 00    lea    0x1b39(%rip),%rdi        # 306a <_IO_stdin_used+0x6a>
    1531:       e8 ca fc ff ff          call   1200 <puts@plt>
    1536:       e8 1b 07 00 00          call   1c56 <read_line>
    153b:       48 89 c7                mov    %rax,%rdi
    153e:       e8 18 03 00 00          call   185b <phase_6>
    1543:       e8 56 08 00 00          call   1d9e <phase_defused>
    1548:       b8 00 00 00 00          mov    $0x0,%eax
    154d:       5b                      pop    %rbx
    154e:       c3                      ret
    154f:       48 8b 05 1a 41 00 00    mov    0x411a(%rip),%rax        # 5670 <stdin@GLIBC_2.2.5>
    1556:       48 89 05 3b 41 00 00    mov    %rax,0x413b(%rip)        # 5698 <infile>
    155d:       e9 21 ff ff ff          jmp    1483 <main+0x3a>
    1562:       48 8b 4b 08             mov    0x8(%rbx),%rcx
    1566:       48 8b 13                mov    (%rbx),%rdx
    1569:       48 8d 35 96 1a 00 00    lea    0x1a96(%rip),%rsi        # 3006 <_IO_stdin_used+0x6>
    1570:       bf 01 00 00 00          mov    $0x1,%edi
    1575:       e8 56 fd ff ff          call   12d0 <__printf_chk@plt>
    157a:       bf 08 00 00 00          mov    $0x8,%edi
    157f:       e8 6c fd ff ff          call   12f0 <exit@plt>
    1584:       48 8b 16                mov    (%rsi),%rdx
    1587:       48 8d 35 95 1a 00 00    lea    0x1a95(%rip),%rsi        # 3023 <_IO_stdin_used+0x23>
    158e:       bf 01 00 00 00          mov    $0x1,%edi
    1593:       b8 00 00 00 00          mov    $0x0,%eax
    1598:       e8 33 fd ff ff          call   12d0 <__printf_chk@plt>
    159d:       bf 08 00 00 00          mov    $0x8,%edi
    15a2:       e8 49 fd ff ff          call   12f0 <exit@plt>
```
i am sorry for the at&t syntax : (

As we can see there is a multitude of levels each one characterized by a function.
# First level
when running `strings` on the code we can see the password for the first level show up:
```
Usage: %s [<input_file>]
That's number 2.  Keep going!
Halfway there!
Good work!  On to the next...
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
Phase 1 defused. How about the next one?
So you got that one.  Try this one.
I am just a renegade hockey mom.
Wow! You've defused the secret stage!
So you think you can stop the bomb with ctrl-c, do you?
Curses, you've found the secret phase!
But finding it and solving it are quite different...
Congratulations! You've defused the bomb!
Well...
```

I suspected the string : ( I am just a renegade hockey mom. ), and it is the first password.
# Second level
from a high view we can see :
```
   0x00000000000015cb <+0>:     endbr64
   0x00000000000015cf <+4>:     push   rbp
   0x00000000000015d0 <+5>:     push   rbx
   0x00000000000015d1 <+6>:     sub    rsp,0x28
   0x00000000000015d5 <+10>:    mov    rax,QWORD PTR fs:0x28
   0x00000000000015de <+19>:    mov    QWORD PTR [rsp+0x18],rax
   0x00000000000015e3 <+24>:    xor    eax,eax
   0x00000000000015e5 <+26>:    mov    rsi,rsp
   0x00000000000015e8 <+29>:    call   0x1c11 <read_six_numbers>
   0x00000000000015ed <+34>:    cmp    DWORD PTR [rsp],0x1
   0x00000000000015f1 <+38>:    jne    0x15fd <phase_2+50>
   0x00000000000015f3 <+40>:    mov    rbx,rsp
   0x00000000000015f6 <+43>:    lea    rbp,[rsp+0x14]
   0x00000000000015fb <+48>:    jmp    0x1612 <phase_2+71>
   0x00000000000015fd <+50>:    call   0x1be5 <explode_bomb>
   0x0000000000001602 <+55>:    jmp    0x15f3 <phase_2+40>
   0x0000000000001604 <+57>:    call   0x1be5 <explode_bomb>
   0x0000000000001609 <+62>:    add    rbx,0x4
   0x000000000000160d <+66>:    cmp    rbx,rbp
   0x0000000000001610 <+69>:    je     0x161d <phase_2+82>
   0x0000000000001612 <+71>:    mov    eax,DWORD PTR [rbx]
   0x0000000000001614 <+73>:    add    eax,eax
   0x0000000000001616 <+75>:    cmp    DWORD PTR [rbx+0x4],eax
   0x0000000000001619 <+78>:    je     0x1609 <phase_2+62>
   0x000000000000161b <+80>:    jmp    0x1604 <phase_2+57>
   0x000000000000161d <+82>:    mov    rax,QWORD PTR [rsp+0x18]
   0x0000000000001622 <+87>:    xor    rax,QWORD PTR fs:0x28
   0x000000000000162b <+96>:    jne    0x1634 <phase_2+105>
   0x000000000000162d <+98>:    add    rsp,0x28
   0x0000000000001631 <+102>:   pop    rbx
   0x0000000000001632 <+103>:   pop    rbp
   0x0000000000001633 <+104>:   ret
   0x0000000000001634 <+105>:   call   0x1220 <__stack_chk_fail@plt>

```

we can see:
- takes six separated digits as arguments by the function read six numbers.

we will give it 6 argument and see what it do with it.
arguments : 1 2 3 4 5 6
and break at the instruction after read six numbers function.

```
4: x/40xg $rsp
0x7fffffffdc30: 0x0000000200000001      0x0000000400000003
0x7fffffffdc40: 0x0000000600000005      0xca9b237595421500
```

we can see from the stack our 6 digits displayed with 32 bit chunks.

from there we can understand the logic after this function, it checks for the first digit i entered if it is equal to 1 if not explode bomb, then a move of the address of RSP to RBX.

```
   0x00000000000015f3 <+40>:    mov    rbx,rsp
   0x00000000000015f6 <+43>:    lea    rbp,[rsp+0x14]
   0x00000000000015fb <+48>:    jmp    0x1612 <phase_2+71>
```

and the load effective address after looking a the code loads the last digit to RBP so it acts like a stop of the loop that is coming up:

```
   0x0000000000001609 <+62>:    add    rbx,0x4
   0x000000000000160d <+66>:    cmp    rbx,rbp
   0x0000000000001610 <+69>:    je     0x161d <phase_2+82>
   
   0x0000000000001612 <+71>:    mov    eax,DWORD PTR [rbx]
   0x0000000000001614 <+73>:    add    eax,eax
   0x0000000000001616 <+75>:    cmp    DWORD PTR [rbx+0x4],eax
   0x0000000000001619 <+78>:    je     0x1609 <phase_2+62>
   0x000000000000161b <+80>:    jmp    0x1604 <phase_2+57>
```

the loop in a high level language could look like this :
```
if dword[rsp] = 1 then continue
	else explode

rbx = rsp
rbp = rsp + 0x14
jump testing

loop:
	rbx = rbx + 4
	if rbx = rbp then exit the function good job
		else continue

testing:
	eax = [rbx]
	eax = eax x 2
	if [rbx + 0x4] = eax then go to loop
		else explode bomb
```

from there we could guess the password. it always take the digit before double it and check the double with the next number.

correct input:
```
1 2 4 8 16 32
```
# Phase 3
phase 3:
```
Dump of assembler code for function phase_3:
   0x0000555555555639 <+0>:     endbr64
   0x000055555555563d <+4>:     sub    rsp,0x18
   0x0000555555555641 <+8>:     mov    rax,QWORD PTR fs:0x28
   0x000055555555564a <+17>:    mov    QWORD PTR [rsp+0x8],rax
   0x000055555555564f <+22>:    xor    eax,eax
   0x0000555555555651 <+24>:    lea    rcx,[rsp+0x4]
   0x0000555555555656 <+29>:    mov    rdx,rsp
   0x0000555555555659 <+32>:    lea    rsi,[rip+0x1caf]        # 0x55555555730f
   0x0000555555555660 <+39>:    call   0x5555555552c0 <__isoc99_sscanf@plt>
   0x0000555555555665 <+44>:    cmp    eax,0x1
   0x0000555555555668 <+47>:    jle    0x555555555688 <phase_3+79>
   0x000055555555566a <+49>:    cmp    DWORD PTR [rsp],0x7
   0x000055555555566e <+53>:    ja     0x555555555704 <phase_3+203>
   0x0000555555555674 <+59>:    mov    eax,DWORD PTR [rsp]
   0x0000555555555677 <+62>:    lea    rdx,[rip+0x1b22]        # 0x5555555571a0
   0x000055555555567e <+69>:    movsxd rax,DWORD PTR [rdx+rax*4]
   0x0000555555555682 <+73>:    add    rax,rdx
   0x0000555555555685 <+76>:    notrack jmp rax
   0x0000555555555688 <+79>:    call   0x555555555be5 <explode_bomb>
   0x000055555555568d <+84>:    jmp    0x55555555566a <phase_3+49>
   0x000055555555568f <+86>:    mov    eax,0x274
   0x0000555555555694 <+91>:    sub    eax,0x24c
   0x0000555555555699 <+96>:    add    eax,0x2b0
   0x000055555555569e <+101>:   sub    eax,0x7e
   0x00005555555556a1 <+104>:   add    eax,0x7e
   0x00005555555556a4 <+107>:   sub    eax,0x7e
   0x00005555555556a7 <+110>:   add    eax,0x7e
   0x00005555555556aa <+113>:   sub    eax,0x7e
   0x00005555555556ad <+116>:   cmp    DWORD PTR [rsp],0x5
   0x00005555555556b1 <+120>:   jg     0x5555555556b9 <phase_3+128>
   0x00005555555556b3 <+122>:   cmp    DWORD PTR [rsp+0x4],eax
   0x00005555555556b7 <+126>:   je     0x5555555556be <phase_3+133>
   0x00005555555556b9 <+128>:   call   0x555555555be5 <explode_bomb>
   0x00005555555556be <+133>:   mov    rax,QWORD PTR [rsp+0x8]
   0x00005555555556c3 <+138>:   xor    rax,QWORD PTR fs:0x28
   0x00005555555556cc <+147>:   jne    0x555555555710 <phase_3+215>
   0x00005555555556ce <+149>:   add    rsp,0x18
   0x00005555555556d2 <+153>:   ret
   0x00005555555556d3 <+154>:   mov    eax,0x0
   0x00005555555556d8 <+159>:   jmp    0x555555555694 <phase_3+91>
   0x00005555555556da <+161>:   mov    eax,0x0
   0x00005555555556df <+166>:   jmp    0x555555555699 <phase_3+96>
   0x00005555555556e1 <+168>:   mov    eax,0x0
   0x00005555555556e6 <+173>:   jmp    0x55555555569e <phase_3+101>
   0x00005555555556e8 <+175>:   mov    eax,0x0
   0x00005555555556ed <+180>:   jmp    0x5555555556a1 <phase_3+104>
   0x00005555555556ef <+182>:   mov    eax,0x0
   0x00005555555556f4 <+187>:   jmp    0x5555555556a4 <phase_3+107>
   0x00005555555556f6 <+189>:   mov    eax,0x0
   0x00005555555556fb <+194>:   jmp    0x5555555556a7 <phase_3+110>
   0x00005555555556fd <+196>:   mov    eax,0x0
   0x0000555555555702 <+201>:   jmp    0x5555555556aa <phase_3+113>
   0x0000555555555704 <+203>:   call   0x555555555be5 <explode_bomb>
   0x0000555555555709 <+208>:   mov    eax,0x0
   0x000055555555570e <+213>:   jmp    0x5555555556ad <phase_3+116>
   0x0000555555555710 <+215>:   call   0x555555555220 <__stack_chk_fail@plt>
```

at first glance it first encounters a scan function then succeeded with 2 comparison the first with RAX then with whats inside a dword of the address of RSP. I also see that it is composed of two part.

after the fonction:
RAX takes the number of digits inside the input.
The inputs get saved into the stack as Dwords.

then an address is calculated and jumped into then comparison against values are done.

pseudo high level code :
```
input = scan
if digits in input <= 1 explode
	else continue

if digit[1] > 7 or > 5 explode
	else continue

rax = dword[0x5555555571a0 + digit[1] * 4]
rax = 0x5555555571a0 + rax
jump rax

switch (rax)
	(0x00005555555556d3)
		digit 2 == -0x24c + 0x2b0 - 0x7e or explode

	(0x00005555555556da)
		digit 2 == 0x2b0 - 0x7e or explode

	(0x00005555555556e1) or (0x00005555555556ef) or (0x00005555555556fd)
		digit 2 == -0x7e or explode

	(0x00005555555556e8) or (0x00005555555556f6) or (0x0000555555555709)
		digit 2 == 0 or explode

	(0x0000555555555704)
		explode
```

this is from this part of the code:
```
 0x0000555555555660 <+39>:    call   0x5555555552c0 <__isoc99_sscanf@plt>

   0x0000555555555665 <+44>:    cmp    eax,0x1
   0x0000555555555668 <+47>:    jle    explode_bomb

   0x000055555555566a <+49>:    cmp    DWORD PTR [rsp],0x7
   0x000055555555566e <+53>:    ja     explode_bomb

   0x0000555555555674 <+59>:    mov    eax,DWORD PTR [rsp]
   0x0000555555555677 <+62>:    lea    rdx,[rip+0x1b22]        # 0x5555555571a0
   0x000055555555567e <+69>:    movsxd rax,DWORD PTR [rdx+rax*4]
   0x0000555555555682 <+73>:    add    rax,rdx
   0x0000555555555685 <+76>:    notrack jmp rax
   0x0000555555555688 <+79>:    call   explode_bomb


   0x0000555555555694 <+91>:    sub    eax,0x24c
   0x0000555555555699 <+96>:    add    eax,0x2b0

   0x000055555555569e <+101>:   sub    eax,0x7e
   0x00005555555556a1 <+104>:   add    eax,0x7e

   0x00005555555556a4 <+107>:   sub    eax,0x7e
   0x00005555555556a7 <+110>:   add    eax,0x7e

   0x00005555555556aa <+113>:   sub    eax,0x7e
   0x00005555555556ad <+116>:   cmp    DWORD PTR [rsp],0x5
   0x00005555555556b1 <+120>:   jg     0x5555555556b9 <phase_3+128>

   0x00005555555556b3 <+122>:   cmp    DWORD PTR [rsp+0x4],eax
   0x00005555555556b7 <+126>:   je     0x5555555556be <phase_3+133>
   0x00005555555556b9 <+128>:   call   0x555555555be5 <explode_bomb>
   0x00005555555556be <+133>:   mov    rax,QWORD PTR [rsp+0x8]
   0x00005555555556c3 <+138>:   xor    rax,QWORD PTR fs:0x28
   0x00005555555556cc <+147>:   jne    0x555555555710 <phase_3+215>
   0x00005555555556ce <+149>:   add    rsp,0x18
   0x00005555555556d2 <+153>:   ret


   0x00005555555556d3 <+154>:   mov    eax,0x0
   0x00005555555556d8 <+159>:   jmp    0x555555555694 <phase_3+91>
   0x00005555555556da <+161>:   mov    eax,0x0
   0x00005555555556df <+166>:   jmp    0x555555555699 <phase_3+96>
   0x00005555555556e1 <+168>:   mov    eax,0x0
   0x00005555555556e6 <+173>:   jmp    0x55555555569e <phase_3+101>
   0x00005555555556e8 <+175>:   mov    eax,0x0
   0x00005555555556ed <+180>:   jmp    0x5555555556a1 <phase_3+104>
   0x00005555555556ef <+182>:   mov    eax,0x0
   0x00005555555556f4 <+187>:   jmp    0x5555555556a4 <phase_3+107>
   0x00005555555556f6 <+189>:   mov    eax,0x0
   0x00005555555556fb <+194>:   jmp    0x5555555556a7 <phase_3+110>
   0x00005555555556fd <+196>:   mov    eax,0x0
   0x0000555555555702 <+201>:   jmp    0x5555555556aa <phase_3+113>
   0x0000555555555704 <+203>:   call   0x555555555be5 <explode_bomb>
   0x0000555555555709 <+208>:   mov    eax,0x0
   0x000055555555570e <+213>:   jmp    0x5555555556ad <phase_3+116>
   0x0000555555555710 <+215>:   call   0x555555555220 <__stack_chk_fail@plt>
```

now we need to find a value of the first digit that has to be :
- digit[1] <= 5
-  digit[1] * 4 + 0x5555555571a0 = X

in sort of when we jump to a known X the digit[2] is equal to the succession of operations before arriving to the comparison.

for this we have to calculate an address then set digit[1] and digit[2] accordingly as the pseudo code says.

lets say that we want to jump to the address X
then according to this equation we can get the according digit[1] value to the address and set digit[2] according to the address as well:
```
digit[1] = (X - 0x5555555571a0) / 4
```


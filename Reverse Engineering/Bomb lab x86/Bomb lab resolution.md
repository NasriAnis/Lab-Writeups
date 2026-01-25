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

I suspected the string : (I am just a renegade hockey mom.), and it is the first password.
# Second level

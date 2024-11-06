# Reverse_Engineering
## Gdb baby step1:
This challenge required us to figure out what was in the eax register at the end of the main function.
First I downloaded the file debugger0_a on my system and saved it in my home directory. Then on terminal, I ran the command `file debugger0_a` to check the file type of debugger which returned this: 
```
debugger0_a: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=15a10290db2cd2ec0c123cf80b88ed7d7f5cf9ff, for GNU/Linux 3.2.0, not stripped
```
Here, I had to use a debugger to dissassemble main. The preferred debugger is gdb which stands for Gnu Debugger, but I was working with macOS so I faced difficulty in using gdb. I tried various methods of downloading it but none worked so I used lldb. I ran the command ` lldb debugger0_a` to load the file debugger0_a for debugging by lldb. Then I ran the command `disassemble -n main` to disassemble the function main. This gave the following output:
```
(lldb) disassemble -n main
debugger0_a`main:
debugger0_a[0x1129] <+0>:  endbr64 
debugger0_a[0x112d] <+4>:  push   rbp
debugger0_a[0x112e] <+5>:  mov    rbp, rsp
debugger0_a[0x1131] <+8>:  mov    dword ptr [rbp - 0x4], edi
debugger0_a[0x1134] <+11>: mov    qword ptr [rbp - 0x10], rsi
debugger0_a[0x1138] <+15>: mov    eax, 0x86342
debugger0_a[0x113d] <+20>: pop    rbp
debugger0_a[0x113e] <+21>: ret    
```
From the output we can observe that the eax register value is 0x86342 which is in hexadecimal format. So, to convert it to decimal  I	ran the command `(lldb) print 0x86342` which gave `(int) 549698` as the output.

References: [LLDB commands](https://www.kodeco.com/books/advanced-apple-debugging-reverse-engineering/v3.0/chapters/A-appendix-a-lldb-cheatsheet)
Flag link: picoCTF{549698}


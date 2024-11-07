# Reverse_Engineering
## Challenge 1: Gdb baby step1

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

New concepts:
1. I learned how debuggers like gdb and lldb can be used to disassemble functions.
2. gdb and lldb are similar in function but their commands and syntax differ.
3. Registers: They are small, fast storage locations within a computer's CPU that hold data, addresses, or instructions.
4. EAX register is a general purpose register

References: [LLDB commands](https://www.kodeco.com/books/advanced-apple-debugging-reverse-engineering/v3.0/chapters/A-appendix-a-lldb-cheatsheet)
Flag link: picoCTF{549698}


## Challenge 2: ARMssembly 1

This challenge required us to find out the argument for the program to print `win` with variables 68, 2 and 3. First, I ran the command `file chall_1.S` to check the file type. 
```
mahikakapil@Mahikas-MacBook-Air cryptonite_tp2_Mahika % file chall_1.S
chall_1.S: assembler source text, ASCII text
```
I ran the command `vim chall_1.S` to open the file in Vim which is a text editor.  

```
func:
        sub     sp, sp, #32
        str     w0, [sp, 12]
        mov     w0, 68
        str     w0, [sp, 16]
        mov     w0, 2
        str     w0, [sp, 20]
        mov     w0, 3
        str     w0, [sp, 24]
        ldr     w0, [sp, 20]
        ldr     w1, [sp, 16]
        lsl     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 24]
        sdiv    w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 12]
        sub     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w0, [sp, 28]
        add     sp, sp, 32
        ret
        .size   func, .-func
        .section        .rodata
        .align  3
```
If we analyze the above code, `str     w0, [sp, 12]` stores the user input at the offset stack +12.  Also `mov     w0, 68` means that the value 68 will be moved to the w0 register. The hint for this challenge was ‘shift’ which made me think that lsl could be of importance here. ` lsl     w0, w1, w0` in this, the value of w1 is the value at address (stack+16) which is 68,  will be shifted by 2 and then stored in w0. 68 in binary is 1000100 which on shifting left by 2 gives 001000000 that is equal to 272 in decimal. Now w0 will contain the value 272. Then, the value from stack+28, 272, is loaded to w0 and the value from stacck+24, 3, is loaded into w1.  ` sdiv    w0, w1, w0` store 272//3 in w0 at stack+28. So the value of w0 at stack+28 corresponds to 90.  Then 90 will be loaded to w1 and user input will be loaded to w0. The difference 90 and the user input will then be stored in w0 at stack +28.

```
.LC0:
        .string "You win!"
        .align  3
.LC1:
        .string "You Lose :("
        .text
        .align  2
        .global main
        .type   main, %function
main:
        stp     x29, x30, [sp, -48]!
        add     x29, sp, 0
        str     w0, [x29, 28]
        str     x1, [x29, 16]
        ldr     x0, [x29, 16]
        add     x0, x0, 8
        ldr     x0, [x0]
        bl      atoi
        str     w0, [x29, 44]
        ldr     w0, [x29, 44]
        bl      func
        cmp     w0, 0
        bne     .L4
        adrp    x0, .LC0
        add     x0, x0, :lo12:.LC0
        bl      puts
        b       .L6
.L4:
        adrp    x0, .LC1
```
On analyzing the rest of the code, we can see `cmp     w0, 0
        bne     .L4` which means that the program will go to .L4 label if the result of w0-0 is not zero. Our goal is to jump to the .LC0 label, hence, w0 should be 0.  So if we combine our earlier observations and this new observation, the user input should be 90 to store 0 in w0. Finally, by converting 90 to hex we get 0x5A. By removing 0x, making it lowercase and 32 bits the final result we get is 0000005a. The flag is picoCTF{0000005a}.

New concepts:
1. MOV instruction is used for moving data from one storage space to another.  It can also move constant values to a destination register. Syntax: mov destination, source
2. STR instruction stores a register value into memory.
3. LSL (Logical Shift Left) is an instruction that moves the right-hand bits of a register to the left.
4. LDR is Load instruction that loads a value from memory into the register.
5. cmp instruction subtracts the two operands


Errors:
1. I was trying to debug chall_1.S using lldb directly but it gave the following error: 
```
(lldb) target create "chall_1.S"
error: '/Users/mahikakapil/cryptonite_tp2_Mahika/chall_1.S' doesn't contain any 'host' platform architectures: arm64, armv7, armv7f, armv7k, armv7s, armv7m, armv7em, armv6m, armv6, armv5, armv4, arm, thumbv7, thumbv7k, thumbv7s, thumbv7f, thumbv7m, thumbv7em, thumbv6m, thumbv6, thumbv5, thumbv4t, thumb, x86_64, x86_64, arm64, arm64e, arm64, arm64e
```
The file had to be converted to an executable binary first, then had to be debugged using lldb.  I tried converting  it into an executable form using the command ` clang -o chall_1 chall_1.S` but it gave the following errors:
```
chall_1.S:6:2: error: unknown directive
 .type func, %function
 ^
chall_1.S:31:2: error: unknown directive
 .size func, .-func
 ^
chall_1.S:32:18: error: unexpected token in '.section' directive
 .section .rodata
                 ^
chall_1.S:42:2: error: unknown directive
 .type main, %function
 ^
chall_1.S:69:2: error: unknown directive
 .size main, .-main
 ^
chall_1.S:71:20: error: unexpected token in '.section' directive
 .section .note.GNU-stack,"",@progbits
                   ^
```
Then I finally came across Vim editor and used the `vim <filename>` command to open the file on terminal.

2. I initially spent a lot of time trying to make the file executable but instead had to focus more on ananlyzing the assembly code.


References: [ARM Assembly cheatsheet](https://cheatography.com/syshella/cheat-sheets/arm-assembly/)

Flag: picoCTF{0000005a}

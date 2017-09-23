---
layout: post
title: programming in Assembly with NASM !
categories:
- blog
---

#Assembly programming - 64 bit linux (NASM)

####understanding cpu architecture
64-bit registers are :
```assembly
rax, rbx, rcx, rdx, rbp, rsp, rsi, rdi, r8, r9, r10, r11, r12, r13, r14, and r15 — these are also 
available in 32-bit flavors r8d–r15d
```
Most of the time, operands that are smaller than 64 bits zero-extend to 64 bits

#### Data Types
The fundamental data types are:<br>
`bytes` - eight bits,<br>
`words` - a word is 2 bytes<br>
`doublewords` - a doubleword is 4 bytes<br>
`quadwords` - a quadword is 8 bytes<br>
`double quadwords` - a double quadword is 16 bytes (128 bits) <br>




#### purpose of registers ([Reference](https://en.wikipedia.org/wiki/X86))
Although the main registers (with the exception of the instruction pointer) are "general-purpose" in the 32-bit and 64-bit versions of the instruction set and can be used for anything, it was originally envisioned that they be used for the following purposes:
```assembly
AL/AH/AX/EAX/RAX: Accumulator
BL/BH/BX/EBX/RBX: Base index (for use with arrays)
CL/CH/CX/ECX/RCX: Counter (for use with loops and strings)
DL/DH/DX/EDX/RDX: Extend the precision of the accumulator (e.g. combine 32-bit EAX and EDX for 64-bit integer operations in 32-bit code)
SI/ESI/RSI: Source index for string operations.
DI/EDI/RDI: Destination index for string operations.
SP/ESP/RSP: Stack pointer for top address of the stack.
BP/EBP/RBP: Stack base pointer for holding the address of the current stack frame.
IP/EIP/RIP: Instruction pointer. Holds the program counter, the current instruction address.
```
#### Hello World

```assembly
;nasm -felf64 hello.asm && ld hello.o && ./a.out

        global  _start

        section .text
_start:
        ; write(1, message, 13)
        mov     rax, 1                  ; system call 1 is write
        mov     rdi, 1                  ; file handle 1 is stdout
        mov     rsi, message            ; address of string to output
        mov     rdx, 13                 ; number of bytes
        syscall                         ; invoke operating system to do the write

        ; exit(0)
        mov     eax, 60                 ; system call 60 is exit
        xor     rdi, rdi                ; exit code 0
        syscall                         ; invoke operating system to exit
message:
        db      "Hello, World", 10      ; note the newline at the end
```
#### Calling conventions (ABI) ([Reference System V ABI 64-bit](https://en.wikipedia.org/wiki/X86))
Calling conventions are platform-specific, each with official documentation — typically called the application binary interface (ABI)

Parameters to functions are passed in the registers `rdi`, `rsi`, `rdx`, `rcx`, `r8`, `r9`, and further values are passed on the stack in reverse order. Parameters passed on the stack may be modified by the called function. Functions are called using the call instruction that pushes the address of the next instruction to the stack and jumps to the operand. Functions return to the caller using the ret instruction that pops a value from the stack and jump to it. The stack is 16-byte aligned just before the call instruction is called.
Functions preserve the registers `rbx`, `rsp`, `rbp`, `r12`, `r13`, `r14`, and `r15`; while `rax`, `rdi`, `rsi`, `rdx`, `rcx`, `r8`, `r9`, `r10`, `r11` are scratch registers. The return value is stored in the `rax` register, or if it is a 128-bit value, then the higher 64-bits go in rdx. Optionally, functions push rbp such that the caller-return-rip is 8 bytes above it, and set rbp to the address of the saved rbp. This allows iterating through the existing stack frames. This can be eliminated by specifying the -fomit-frame-pointer GCC option.
Signal handlers are executed on the same stack, but 128 bytes known as the red zone is subtracted from the stack before anything is pushed to the stack. This allows small leaf functions to use 128 bytes of stack space without reserving stack space by subtracting from the stack pointer. The red zone is well-known to cause problems for x86-64 kernel developers, as the CPU itself doesn't respect the red zone when calling interrupt handlers. This leads to a subtle kernel breakage as the ABI contradicts the CPU behavior. The solution is to build all kernel code with -mno-red-zone or by handling interrupts in kernel mode on another stack than the current (and thus implementing the ABI).

#### Pseudo-Instructions ([Reference](http://www.nasm.us/doc/nasmdoc3.html#section-3.2))

Pseudo-instructions are things which, though not real x86 machine instructions, are used in the instruction field anyway because that's the most convenient place to put them. The current pseudo-instructions are DB, DW, DD, DQ, DT, DO, DY and DZ; their uninitialized counterparts RESB, RESW, RESD, RESQ, REST, RESO, RESY and RESZ; the INCBIN command, the EQU command, and the TIMES prefix.

3.2.1 DB and Friends: Declaring Initialized Data

DB, DW, DD, DQ, DT, DO, DY and DZ are used, much as in MASM, to declare initialized data in the output file. They can be invoked in a wide range of ways:
```assembly
      db    0x55                ; just the byte 0x55 
      db    0x55,0x56,0x57      ; three bytes in succession 
      db    'a',0x55            ; character constants are OK 
      db    'hello',13,10,'$'   ; so are string constants 
      dw    0x1234              ; 0x34 0x12 
      dw    'a'                 ; 0x61 0x00 (it's just a number) 
      dw    'ab'                ; 0x61 0x62 (character constant) 
      dw    'abc'               ; 0x61 0x62 0x63 0x00 (string) 
      dd    0x12345678          ; 0x78 0x56 0x34 0x12 
      dd    1.234567e20         ; floating-point constant 
      dq    0x123456789abcdef0  ; eight byte constant 
      dq    1.234567e20         ; double-precision float 
      dt    1.234567e20         ; extended-precision float
```
DT, DO, DY and DZ do not accept numeric constants as operands.

3.2.2 RESB and Friends: Declaring Uninitialized Data

RESB, RESW, RESD, RESQ, REST, RESO, RESY and RESZ are designed to be used in the BSS section of a module: they declare uninitialized storage space. Each takes a single operand, which is the number of bytes, words, doublewords or whatever to reserve. As stated in section 2.2.7, NASM does not support the MASM/TASM syntax of reserving uninitialized space by writing DW ? or similar things: this is what it does instead. The operand to a RESB-type pseudo-instruction is a critical expression: see section 3.8.

For example:
```assembly
buffer:         resb    64              ; reserve 64 bytes 
wordvar:        resw    1               ; reserve a word 
realarray       resq    10              ; array of ten reals 
ymmval:         resy    1               ; one YMM register 
zmmvals:        resz    32              ; 32 ZMM registers 
```

3.4 Constants

NASM understands four different types of constant: numeric, character, string and floating-point.

#### Numeric Constants

A numeric constant is simply a number. NASM allows you to specify numbers in a variety of number bases, in a variety of ways: you can suffix H or X, D or T, Q or O, and B or Y for hexadecimal, decimal, octal and binary respectively, or you can prefix 0x, for hexadecimal in the style of C, or you can prefix $ for hexadecimal in the style of Borland Pascal or Motorola Assemblers. Note, though, that the $ prefix does double duty as a prefix on identifiers (see section 3.1), so a hex number prefixed with a $ sign must have a digit after the $ rather than a letter. In addition, current versions of NASM accept the prefix 0h for hexadecimal, 0d or 0t for decimal, 0o or 0q for octal, and 0b or 0y for binary. Please note that unlike C, a 0 prefix by itself does not imply an octal constant!

Numeric constants can have underscores (_) interspersed to break up long strings.

Some examples (all producing exactly the same code):
```assembly
        mov     ax,200          ; decimal 
        mov     ax,0200         ; still decimal 
        mov     ax,0200d        ; explicitly decimal 
        mov     ax,0d200        ; also decimal 
        mov     ax,0c8h         ; hex 
        mov     ax,$0c8         ; hex again: the 0 is required 
        mov     ax,0xc8         ; hex yet again 
        mov     ax,0hc8         ; still hex 
        mov     ax,310q         ; octal 
        mov     ax,310o         ; octal again 
        mov     ax,0o310        ; octal yet again 
        mov     ax,0q310        ; octal yet again 
        mov     ax,11001000b    ; binary 
        mov     ax,1100_1000b   ; same binary constant 
        mov     ax,1100_1000y   ; same binary constant once more 
        mov     ax,0b1100_1000  ; same binary constant yet again 
        mov     ax,0y1100_1000  ; same binary constant yet again
```

###Programs for practise
```asm
; Hello World Program (Calculating string length)
; Compile with: nasm -f elf helloworld-len.asm
; Link with (64 bit systems require elf_i386 option): ld -m elf_i386 helloworld-len.o -o helloworld-len
; Run with: ./helloworld-len
 
SECTION .data
msg     db      'Hello, brave new world!', 0Ah ; we can modify this now without having to update anywhere else in the program
 
SECTION .text
global  _start
 
_start:
 
    mov     ebx, msg        ; move the address of our message string into EBX
    mov     eax, ebx        ; move the address in EBX into EAX as well (Both now point to the same segment in memory)
 
nextchar:
    cmp     byte [eax], 0   ; compare the byte pointed to by EAX at this address against zero (Zero is an end of string delimiter)
    jz      finished        ; jump (if the zero flagged has been set) to the point in the code labeled 'finished'
    inc     eax             ; increment the address in EAX by one byte (if the zero flagged has NOT been set)
    jmp     nextchar        ; jump to the point in the code labeled 'nextchar'
 
finished:
    sub     eax, ebx        ; subtract the address in EBX from the address in EAX
                            ; remember both registers started pointing to the same address (see line 15)
                            ; but EAX has been incremented one byte for each character in the message string
                            ; when you subtract one memory address from another of the same type
                            ; the result is number of segments between them - in this case the number of bytes
 
    mov     edx, eax        ; EAX now equals the number of bytes in our string
    mov     ecx, msg        ; the rest of the code should be familiar now
    mov     ebx, 1
    mov     eax, 4
    int     80h
 
    mov     ebx, 0
    mov     eax, 1
    int     80h
```

## Flowchart
```txt
1.)Define Sections: Declare your data section for initialized variables (var1, var2, etc.) and your bss section for the uninitialized result variable.
2.)Load Data: Move the values into registers (e.g., eax, ebx).
3.)Perform Operation: Use appropriate arithmetic instructions (add, sub, imul, idiv).
4.)Store Result: Move the value from the register to the result memory location.
5.)Exit: Use the appropriate system calls to terminate the program cleanly.
```
## Example: result = -var1 * 10
```ASM
section .data
    var1 dd 5
    ten  dd 10

section .bss
    result resd 1

section .text
    global _start
_start:
    mov eax, [var1]
    neg eax          ; -var1
    imul eax, [ten]  ; eax = eax * 10
    mov [result], eax
    
    ; Exit
    mov eax, 1
    mov ebx, 0
    int 0x80
```
## Example: result = var1 + var2 + var3 + var4
```ASM
section .data
    var1 dd 1
    var2 dd 2
    var3 dd 3
    var4 dd 4

section .bss
    result resd 1

section .text
    global _start
_start:
    mov eax, [var1]
    add eax, [var2]
    add eax, [var3]
    add eax, [var4]
    mov [result], eax
    
    mov eax, 1
    int 0x80
```
## Challenges: 
```txt
One significant challenge I encountered was managing signed versus unsigned integers, 
as I had to learn that assembly requires explicit instructions like imul and idiv to correctly handle negative values. 
Additionally, I found it difficult to map the register states in GDB to my source code, but I overcame this by adopting the stepi command to meticulously observe how each individual instruction altered the CPU registers.
```

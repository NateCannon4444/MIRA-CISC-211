## Variables and constants
```.asm
section .text
        global _start

_start:
        ; use this space for the main body of your program
        ; ======== write your code below ===========
        
        ; Load var1 into eax
        mov eax, [var1]
        ; Add var2 to eax
        add eax, [var2]
        ; Store the result in 'result'
        mov [result], eax

        ; ======== write your code above ===========

        mov eax,1      ; set eax register to 1 (do not remove this line)
        int 0x80       ; interrupt 0x80 (do not remove this line)

segment .bss
        ; use this space for uninitialized variable (result)
        result resd 1  ; reserve 1 double word (4 bytes) for the result

segment .data
        ; use this space for initialized variables (var1 and var2)
        var1 dd 10     ; define double word 10
        var2 dd 15     ; define double word 15
        ```

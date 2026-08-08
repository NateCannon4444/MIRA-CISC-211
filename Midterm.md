## Question 1
```ASM
; =====================================================================
; Variable Values:
;   var1 = 10
;   var2 = 2
;   var3 = 5
;   result = (10 + 2) / (5 - 2) = 12 / 3 = 4 (Remainder = 0)
; =====================================================================

section .data
    var1    dw 10
    var2    dw 2
    var3    dw 5
    result  dw 0
    
    msg     db "Result: 4", 0Ah
    msg_len equ $ - msg

section .text
    global _start

_start:
    ; 1. Calculate denominator: (var3 - var2)
    mov ax, [var3]          ; AX = var3 (5)
    sub ax, [var2]          ; AX = 5 - 2 = 3
    mov bx, ax              ; Store denominator in BX

    ; 2. Calculate numerator: (var1 + 2)
    mov ax, [var1]          ; AX = var1 (10)
    add ax, 2               ; AX = 10 + 2 = 12

    ; 3. Perform division: Numerator (AX) / Denominator (BX)
    ; In x86 16-bit division, DX:AX is divided by the operand. 
    ; We clear DX for unsigned/positive division.
    xor dx, dx              
    idiv bx                 ; AX = Quotient (12 / 3 = 4), DX = Remainder (0)

    mov [result], ax        ; Store quotient into result

    ; 4. Display result on console using sys_write
    mov eax, 4              ; sys_write
    mov ebx, 1              ; stdout
    mov ecx, msg            ; message address
    mov edx, msg_len        ; message length
    int 80h

    ; 5. Exit program
    mov eax, 1              ; sys_exit
    xor ebx, ebx
    int 80h
```
## Question 2
```txt
a\b   b′(0)   b(1)
a′(0)   0     1
a(1)    1     1

Minterms mapped from expression:
a ⋅ b -> Row 1, Col 1 (Value = 1)
a' ⋅ b -> Row 0, Col 1 (Value = 1)
a ⋅ b' -> Row 1, Col 0 (Value = 1)

Grouping and Simplification:
Group 1 (Vertical Column for b=1): Covers cells (a', b) and (a, b). This simplifies to b.
Group 2 (Horizontal Row for a=1): Covers cells (a, b') and (a, b). This simplifies to a.

Simplified: Y = a + b
```
## Question 3
```ASM
section .data
    number      dw 7               ; Test number (change to test even/odd)
    even_msg    db "even number", 0Ah
    even_len    equ $ - even_msg
    odd_msg     db "odd number", 0Ah
    odd_len     equ $ - odd_msg

section .text
    global _start

_start:
    mov ax, [number]        ; Load number into AX
    xor dx, dx              ; Clear DX for division
    mov bx, 2               ; Divisor = 2
    idiv bx                 ; AX = number / 2, DX = remainder (0 or 1)

    cmp dx, 0               ; Check if remainder is 0
    je is_even              ; If DX == 0, it's even

    ; --- Odd Handler ---
    mov eax, 4              ; sys_write
    mov ebx, 1              ; stdout
    mov ecx, odd_msg        ; Load "odd number" message
    mov edx, odd_len
    int 80h
    jmp exit_prog

is_even:
    ; --- Even Handler ---
    mov eax, 4              ; sys_write
    mov ebx, 1              ; stdout
    mov ecx, even_msg       ; Load "even number" message
    mov edx, even_len
    int 80h

exit_prog:
    ; Terminate program
    mov eax, 1              ; sys_exit
    xor ebx, ebx
    int 80h
```

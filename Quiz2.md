```ASM
section .text
        global _start

_start:
        ; Pass x, y, and z via the stack
        push dword [z]
        push dword [y]
        push dword [x]

        call add_three_vars    ; Call the function

        ; Clean up arguments passed on the stack (3 variables * 4 bytes = 12 bytes)
        add esp, 12

        ; Store the returned result (in eax) into the 'result' variable
        mov [result], eax

        ; Exit program
        mov eax, 1
        xor ebx, ebx
        int 0x80

; Function: add_three_vars
add_three_vars:
        push ebp               ; Set up stack frame
        mov ebp, esp

        ; Access parameters via stack offsets:
        ; [ebp + 8]  = x
        ; [ebp + 12] = y
        ; [ebp + 16] = z
        mov eax, [ebp + 8]     ; Load x into eax
        add eax, [ebp + 12]    ; Add y
        add eax, [ebp + 16]    ; Add z, sum is now in eax (return value)

        ; Deallocate stack memory / restore stack frame
        mov esp, ebp
        pop ebp
        ret                    ; Return to calling program (result is in eax)

segment .bss
        result resd 1          ; Uninitialized variable to store the sum

segment .data
        x dd 5                 ; Initialized variable x
        y dd 10                ; Initialized variable y
        z dd 15                ; Initialized variable z
```

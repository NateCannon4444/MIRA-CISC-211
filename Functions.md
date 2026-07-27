## Flowchart
```txt
1.) Initialization (_start): Set register bl to the ASCII value of 'A' (65).

2.) Loop Check (print_loop): Compare the current character in bl against 'Z' (90).
If bl > 'Z', jump to the termination routine (sys_exit).

3.) Procedure Execution (call print_char): Preserve registers (ecx, edx) on the stack.
Safely map the single-byte character from register bl onto the stack memory to satisfy Linux's buffer pointer requirement for sys_write.
Invoke sys_write (eax = 4) to output the character, followed by a separate sys_write call to output the line feed (newline).
Restore stack alignment and registers via pop operations.

4.) Increment & Repeat: Increment bl by 1 to target the next sequential alphabet letter and loop back.
```
## code snippet
```ASM
section .data
        newline db 10          ; Line feed character

section .text
        global _start

_start:
        mov bl, 'A'            ; Initialize starting character 'A'

print_loop:
        call print_char        ; Call procedure to print character and newline
        inc bl                 ; Move to the next character
        cmp bl, 'Z'            ; Check if we have passed 'Z'
        jle print_loop         ; If less than or equal to 'Z', repeat loop

        ; Exit program
        mov eax, 1             ; sys_exit system call
        xor ebx, ebx           ; Exit status 0
        int 0x80

; Procedure to print the character in bl followed by a newline
print_char:
        push edx
        push ecx
        
        ; Store character below stack to safely pass a 1-byte pointer to sys_write
        mov [esp-4], bl        
        sub esp, 4
        
        ; Print character using sys_write
        mov eax, 4
        mov ebx, 1
        mov ecx, esp           ; Pointer to character on stack
        mov edx, 1             ; Number of bytes
        int 0x80

        ; Print newline
        mov eax, 4
        mov ebx, 1
        mov ecx, newline
        mov edx, 1
        int 0x80

        add esp, 4             ; Clean up stack space used for character
        pop ecx
        pop edx
        ret
```
## Challenges: 
```txt
A challenge was passing single-byte memory buffers for system calls. Linux's sys_write system call expects a memory address (ecx) pointing to a data buffer rather than an immediate value inside a register. Managing temporary stack space (esp) to stream single characters without declaring permanent variables in the data segment required precise pointer arithmetic.
Another challenge was preserving register state ccross procedures. Ensuring that calling the print_char routine did not inadvertently overwrite registers being used by the main loop boundary checks required careful implementation of push and pop instructions.
```

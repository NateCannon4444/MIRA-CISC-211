## Flowchart
```txt
1.) Start: Initialize register bl with the ASCII value for 'A' (65)
2.) Loop Check: Compare bl with 'Z' (90). If bl > 'Z', jump to Exit
3.) Procedure Call: Call print_char to handle writing the character and a line feed to standard output using Linux system calls (sys_write)
4.) Increment: Increment bl by 1 to move to the next alphabetical character
5.) Repeat / Exit: Loop back to the comparison step or terminate the program using sys_exit (eax = 1, int 0x80)
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
        ; Print character
        mov eax, 4             ; sys_write system call
        mov ebx, 1             ; File descriptor 1 (stdout)
        mov ecx, bl            ; Point to the character in bl (Note: using stack or buffer is cleaner, 
                               ; but for a quick sys_write of 1 byte, we can use a temporary stack location)
        
        ; Using the stack to safely pass a 1-byte variable to sys_write
        push edx
        push ecx
        
        mov [esp-4], bl        ; Store character below stack
        sub esp, 4
        
        mov eax, 4
        mov ebx, 1
        mov ecx, esp           ; Pointer to character
        mov edx, 1             ; Number of bytes
        int 0x80

        ; Print newline
        mov eax, 4
        mov ebx, 1
        mov ecx, newline
        mov edx, 1
        int 0x80

        add esp, 4             ; Clean up stack
        pop ecx
        pop edx
        ret
```
## Challenges: 
```txt
A challenge was passing 1-Byte Values to sys_write. Linux system calls expect pointers (ecx) to memory buffers rather than immediate register contents for data. Managing stack allocation to safely stream single-byte characters without declaring permanent variables in data segments required careful register and stack pointer management (esp).
Another challenge was svoiding gdb. Because debugging tools were restricted for this specific task, verifying register states and loop boundaries had to be done purely through careful tracing of ASCII bounds ('A' through 'Z') and correct system call numbers (4 for write, 1 for exit).
```

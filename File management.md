## Design & Implementation Challenges:
```txt
1.) File Pointer Positioning (sys_lseek): Managing the file offset correctly required understanding how sys_lseek (19) interacts with the file descriptor.
Specifying 2 in the EDX register to target the end of the file ensured that the appended text was safely added
right after the initial contents without overwriting existing data.
2.) File Permissions and Descriptors: Preserving the file descriptor returned by sys_creat into a .bss variable (fd) was critical
so that subsequent sys_write, sys_lseek, and sys_close operations targeted the correct file stream without losing reference.
3.) Debugging via GDB: Since the lab mandated debugging using gdb, stepping through system call returns (EAX) helped
verify that file creation and appending operations executed without throwing error flags (which would return negative values in EAX).
```
## code snippet
```ASM
section .data
    file_name db "quotes.txt", 0
    
    ; Initial text content
    text1 db "To be, or not to be, that is the question.", 0xA
          db "A fool thinks himself to be wise, but a wise man knows himself to be a fool.", 0xA
    len1 equ $ - text1

    ; Text to append
    text2 db "Better three hours too soon than a minute too late.", 0xA
          db "No legacy is so rich as honesty.", 0xA
    len2 equ $ - text2

section .bss
    fd resw 1              ; Reserve space for file descriptor

section .text
    global _start

_start:
    ; -------------------------------------------------------------
    ; Task 1: Create 'quotes.txt' and write initial contents
    ; -------------------------------------------------------------
    mov eax, 8             ; sys_creat (32-bit syscall 8)
    mov ebx, file_name     ; pointer to filename string
    mov ecx, 0777          ; read, write, execute permissions (rwxrwxrwx)
    int 0x80

    test eax, eax
    js exit_error          ; Jump if error occurred
    mov [fd], eax          ; Save file descriptor

    ; Write initial quotes to file
    mov eax, 4             ; sys_write
    mov ebx, [fd]          ; file descriptor
    mov ecx, text1         ; pointer to initial text
    mov edx, len1          ; number of bytes
    int 0x80

    ; -------------------------------------------------------------
    ; Task 2: Append new quotes using sys_lseek (syscall number 19)
    ; -------------------------------------------------------------
    ; Move file pointer to the end of the file (Reference position: 2)
    mov eax, 19            ; sys_lseek
    mov ebx, [fd]          ; file descriptor
    mov ecx, 0             ; offset value (0 bytes from end)
    mov edx, 2             ; reference position: 2 = End of file
    int 0x80

    ; Write appended quotes to file
    mov eax, 4             ; sys_write
    mov ebx, [fd]          ; file descriptor
    mov ecx, text2         ; pointer to text to append
    mov edx, len2          ; number of bytes
    int 0x80

    ; -------------------------------------------------------------
    ; Close the file
    ; -------------------------------------------------------------
    mov eax, 6             ; sys_close
    mov ebx, [fd]
    int 0x80

    ; Exit program successfully
    mov eax, 1             ; sys_exit
    xor ebx, ebx           
    int 0x80

exit_error:
    ; Exit program with error code
    mov eax, 1
    mov ebx, 1
    int 0x80
```

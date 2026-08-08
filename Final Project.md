## Video link
```txt
https://sdccd.us-west-2.instructuremedia.com/embed/d219dd13-a3ad-4b37-ba6f-61b0f683490b
```
## Flowchart for task 1
```txt
1.) Start: Initialize the plaintext message buffer with "HELLO" and the secret key buffer with "world", setting the loop counter index register (esi) to 0 and message length (ecx) to 5.
2.) Loop Check (Encryption): Compare the current index esi with ecx (5). If esi $\ge$ ecx, jump to the decryption phase.
3.) XOR Operation: Load the plaintext byte at [plain_buf + esi], apply a bitwise xor instruction against the corresponding key byte at [key_buf + esi], and store the result in the encryption buffer (enc_buf).
4.) Increment & Repeat: Increment index esi by 1 and loop back to the encryption check step.
5.) Decryption Phase: Reset index esi to 0. Compare esi with ecx. Load each byte from enc_buf, apply the xor operation with the key a second time to reverse the encryption, store it in dec_buf, increment esi, and repeat until decrypted.
6.) File I/O & Exit: Create and open output.txt using system call sys_creat (eax = 8), write the formatted text labels, buffers, and results using sys_write (eax = 4), close the file descriptor via sys_close (eax = 6), and terminate the program using sys_exit (eax = 1, int 0x80).
```
## code for task 1
```ASM
section .data
    msg_prompt      db "Plain text: ", 0
    msg_prompt_len  equ $ - msg_prompt
    key_prompt      db "Key: ", 0
    key_prompt_len  equ $ - key_prompt

    out_plain       db "Plain text: "
    plain_buf       db "HELLO", 0Ah
    len_plain       equ $ - out_plain

    out_key         db "Key: "
    key_buf         db "world", 0Ah
    len_key         equ $ - out_key

    out_enc         db "Encrypted text: "
    enc_buf         db "     ", 0Ah      ; Placeholder for 5-char encrypted text + newline
    len_enc         equ $ - out_enc

    out_dec         db "Decrypted text: "
    dec_buf         db "     ", 0Ah      ; Placeholder for 5-char decrypted text + newline
    len_dec         equ $ - out_dec

    filename        db "output.txt", 0

section .bss
    file_descriptor resb 4

section .text
    global _start

_start:
    ; --- Step 1: Encrypt plaintext using XOR with the key ---
    mov ecx, 5              ; Length of the message ("HELLO" is 5 bytes)
    xor esi, esi            ; Index counter i = 0

encrypt_loop:
    cmp esi, ecx
    jge encrypt_done

    mov al, [plain_buf + esi]   ; Load character from plaintext
    xor al, [key_buf + esi]     ; XOR with corresponding key character
    mov [enc_buf + esi], al     ; Store in encryption buffer

    inc esi
    jmp encrypt_loop

encrypt_done:

    ; --- Step 2: Decrypt encrypted text back to plaintext ---
    xor esi, esi            ; Reset index counter

decrypt_loop:
    cmp esi, ecx
    jge decrypt_done

    mov al, [enc_buf + esi]     ; Load encrypted character
    xor al, [key_buf + esi]     ; XOR again with the key to restore original
    mov [dec_buf + esi], al     ; Store in decryption buffer

    inc esi
    jmp decrypt_loop

decrypt_done:

    ; --- Step 3: Write results to output.txt ---
    ; Open file for writing (create/truncate)
    mov eax, 8              ; sys_creat
    mov ebx, filename       ; filename
    mov ecx, 0644h          ; permissions
    int 80h
    mov [file_descriptor], eax

    ; Write Plain text
    mov eax, 4              ; sys_write
    mov ebx, [file_descriptor]
    mov ecx, out_plain
    mov edx, len_plain
    int 80h

    ; Write Key
    mov eax, 4
    mov ebx, [file_descriptor]
    mov ecx, out_key
    mov edx, len_key
    int 80h

    ; Write Encrypted text
    mov eax, 4
    mov ebx, [file_descriptor]
    mov ecx, out_enc
    mov edx, len_enc
    int 80h

    ; Write Decrypted text
    mov eax, 4
    mov ebx, [file_descriptor]
    mov ecx, out_dec
    mov edx, len_dec
    int 80h

    ; Close file
    mov eax, 6              ; sys_close
    mov ebx, [file_descriptor]
    int 80h

    ; --- Exit Program ---
    mov eax, 1              ; sys_exit
    xor ebx, ebx            ; exit code 0
    int 80h
```
```txt
nasm -f elf32 encryption.asm -o encryption.o
ld -m elf_i386 encryption.o -o encryption
./encryption
```
```txt
cat output.txt
```
## Flowchart for task 2
```txt
1.) Start: Initialize the counter argument with 100000000 (100 million) and push it onto the stack before calling the count function for either the function-based or recursive approach.
2.) Approach Evaluation:
For Function-Based: Enter the loop, decrement the counter register (ecx), and use a conditional jump (jnz) to repeat until ecx reaches 0.
For Recursive: Check the base case (argument <= 0). If not, decrement the argument by 1, push the new value onto the stack, call count recursively, and unwind the stack frames upon reaching the base case.
3.) Performance Timing: Wrap the program execution inside the Linux time command to measure real, user, and system durations.
4.) Output Redirection & Termination: Redirect the timing output to counter_fun.txt or counter_rec.txt, append the counter completion string, and terminate the program using sys_exit (eax = 1, int 0x80).
```
## code for task 2
```ASM
section .data
    msg         db "Counter value: 100000000", 0Ah

section .text
    global _start

_start:
    ; Pass a much larger number to create a measurable workload
    push dword 100000000
    call count
    add esp, 4              ; Clean up stack

    ; Exit program
    mov eax, 1
    xor ebx, ebx
    int 80h

count:
    push ebp
    mov ebp, esp
    
    mov ecx, [ebp + 8]      ; Get argument (100,000,000)
    
count_loop:
    dec ecx
    jnz count_loop          ; Loop until counter reaches 0

    mov esp, ebp
    pop ebp
    ret
```
```ASM
section .data
    msg         db "Counter value: 100000000", 0Ah

section .text
    global _start

_start:
    push dword 100000000
    call count
    add esp, 4              ; Clean up stack

    ; Exit program
    mov eax, 1
    xor ebx, ebx
    int 80h

count:
    push ebp
    mov ebp, esp

    mov eax, [ebp + 8]      ; Get argument
    cmp eax, 0              ; Base case check: if n == 0, return
    jle count_done

    dec eax                 ; n - 1
    push eax                ; Push argument for recursive call
    call count
    add esp, 4              ; Clean up stack

count_done:
    mov esp, ebp
    pop ebp
    ret
```
```txt
nasm -f elf32 counter_fun.asm -o counter_fun.o
ld -m elf_i386 counter_fun.o -o counter_fun
(time ./counter_fun) > counter_fun.txt 2>&1
echo "Counter output: 100000000" >> counter_fun.txt

nasm -f elf32 counter_rec.asm -o counter_rec.o
ld -m elf_i386 counter_rec.o -o counter_rec
(time ./counter_rec) > counter_rec.txt 2>&1
echo "Counter output: 100000000" >> counter_rec.txt
```
```txt
cat counter_fun.txt
cat counter_rec.txt
```


## Flowchart
## Task 1: Optimized EBX Counter
```txt
1.) Start -> Initialize the counter register ebx to a starting value (e.g., ebx = 0).
2.) Loop Condition -> Compare ebx to a target limit (e.g., cmp ebx, 10).
3.) Branch -> If the target is met, jump out of the loop (jge exit_loop).
4.) Increment/Process -> Increment ebx (inc ebx) efficiently using optimized loop structures.
5.) Repeat -> Jump back to the loop check.
```
## Task 2: First 10 Fibonacci Numbers (Target: 55)
```txt
1.) Start -> Initialize sequence variables or registers for the first two Fibonacci terms (F_0 = 0, F_1 = 1) and set a loop counter to 8 (since 2 are already handled, leaving 8 iterations to reach the 10th number).
2.) Loop Start -> Add the previous two terms together to generate the next term (next = t1 + t2).
3.) Shift Values -> Update terms: move t2 into t1, and the new term into t2.
4.) Decrement Counter -> Subtract 1 from the loop counter.
5.) Check -> If the counter > 0, repeat the loop. Otherwise, the final register/variable will hold 55.
```
## Task 3: Largest Element in an Array of Length 3
```txt
1.) Start -> Define an array of 3 integers in the .data section.
2.) Load Initial -> Load the base address of the array, set the loop counter to 3, and move the first array element into a tracking register (eax).
3.) Iteration Check -> Loop through the remaining 2 elements by updating the array pointer offset.
4.) Comparison -> Compare each subsequent array element with the current maximum in eax. If greater, overwrite eax.
5.) Store & Exit -> Store the final maximum value into an output variable and invoke the exit system call (int 0x80).
```
## Code snippet
```ASM
section .data
    ; Task 3: Integer array of length 3
    arr         dd 15, 78, 42
    arr_len     dd 3

section .bss
    ; Uninitialized variable to store the largest array element result
    largest_arr_elem resd 1
    fib_result       resd 1

section .text
    global _start

_start:
    ; ==========================================
    ; TASK 1: Generate a counter using the EBX register
    ; Optimized loop version counting up to 10
    ; ==========================================
    xor ebx, ebx            ; Clear ebx (ebx = 0)
    mov ecx, 10             ; Set loop limit counter to 10

counter_loop:
    inc ebx                 ; Increment counter in ebx
    dec ecx                 ; Decrement loop control register
    jnz counter_loop        ; Repeat until ecx reaches 0 (ebx will equal 10)


    ; ==========================================
    ; TASK 2: Calculate the 10th Fibonacci number starting from 0
    ; Expected final result: 55
    ; ==========================================
    mov eax, 0              ; F(0) = 0
    mov ebx, 1              ; F(1) = 1
    mov ecx, 8              ; Remaining iterations (10 total - 2 initial handled)

fib_loop:
    test ecx, ecx           ; Check if counter is 0
    jz fib_done
    
    mov edx, eax            ; Save F(n-2) temporarily
    mov eax, ebx            ; Move F(n-1) to current position
    add ebx, edx            ; F(n) = F(n-1) + F(n-2)
    
    dec ecx                 ; Decrement counter
    jmp fib_loop

fib_done:
    mov [fib_result], ebx   ; Store final Fibonacci number (55) in memory


    ; ==========================================
    ; TASK 3: Define an integer array of length 3 and find the largest element
    ; ==========================================
    mov esi, arr            ; Load base address of array into esi
    mov ecx, [arr_len]      ; Load array length (3) into ecx
    
    mov eax, [esi]          ; Assume the first element is the largest
    add esi, 4              ; Advance pointer to the next 4-byte integer
    dec ecx                 ; Decrement element counter

find_max_loop:
    cmp ecx, 0
    jle max_done

    mov edx, [esi]          ; Load next element into edx
    cmp edx, eax            ; Compare current element with current max (eax)
    jle skip_update
    mov eax, edx            ; Update max if edx > eax

skip_update:
    add esi, 4              ; Point to next element
    dec ecx                 ; Decrement counter
    jmp find_max_loop

max_done:
    mov [largest_arr_elem], eax ; Store largest value (78) into memory


    ; ==========================================
    ; Exit Program (Linux syscall: sys_exit)
    ; ==========================================
    mov eax, 1              ; System call number for exit
    xor ebx, ebx            ; Exit status code 0
    int 0x80                ; Call kernel
```

## Challenges: 
```txt
Some challenges were handling array traversal correctly required keeping track of byte offsets (multiplying element indices by 4 for 32-bit dd integers) using index registers like esi
Keeping track of rolling values (F_{n-2}, F_{n-1}, and F_n) across iterations required careful register swapping without losing intermediate calculation results.
Also GDB Register Inspection: Verifying the EBX counter and the Fibonacci final result step-by-step using GDB required precise breakpoint placement (break _start) and using stepi alongside print commands to track changes to registers in real-time.
```

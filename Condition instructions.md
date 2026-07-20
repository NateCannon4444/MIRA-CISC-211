## Flowchart
## Part A: Even/Odd Sequence Generation (up to 20)
```txt
1.) Start -> Initialize a counter register (e.g., ecx = 1 for odd numbers or ecx = 0 for even numbers).
2.) Loop Check -> Compare counter with 20 (cmp ecx, 20).
3.) Condition -> If counter > 20, jump to exit/next task. Otherwise, proceed.
4.) Action -> Store or process the current value of the counter.
5.) Increment -> Add 2 to the counter (add ecx, 2) to maintain the sequence of only even or only odd numbers.
6.) Repeat -> Jump back to the loop check.
```
## Part B: Find the Largest Among Five Integers
```txt
1.) Start -> Load the first initialized variable into a comparison register (e.g., eax = nums[0]).
2.) Iteration -> Point to the next array element using an index or pointer.
3.) Compare -> Compare the current element with eax (cmp current_val, eax).
4.) Conditional Jump -> If the current value is greater (jg), update eax with the new value.
5.) Otherwise, skip.Loop/Repeat -> Repeat for all remaining elements (total of 5 values).
6.) Store Result -> Move the final value from eax into the uninitialized variable largest.
7.) End -> Exit program via Linux system call (sys_exit).
```
## Code snippet
```ASM

section .data
    ; Task 2: Initialized variables for five integer values
    nums        dd 12, 45, 23, 89, 34
    num_count   dd 5

section .bss
    ; Task 2: Uninitialized variable named 'largest'
    largest     resd 1

section .text
    global _start

_start:
    ; ==========================================
    ; TASK 1: Generate a sequence of odd/even numbers up to 20
    ; Let's generate odd numbers: 1, 3, 5, ..., 19
    ; ==========================================
    mov ecx, 1          ; Start sequence at 1 (odd)

generate_loop:
    cmp ecx, 20         ; Compare counter with 20
    jge task2_setup     ; If ecx >= 20, move to Task 2

    ; (Optional place to store or process the sequence value in ecx)
    
    add ecx, 2          ; Increment by 2 to keep sequence odd
    jmp generate_loop

task2_setup:
    ; ==========================================
    ; TASK 2: Find the largest number among five integers
    ; ==========================================
    mov esi, nums       ; Load the base address of the array into esi
    mov ecx, [num_count]; Load loop counter (5 elements)
    
    mov eax, [esi]      ; Assume the first element is the largest initially
    add esi, 4          ; Move pointer to the next integer (4 bytes per 'dd')
    dec ecx             ; Decrement count since first element is handled

find_largest_loop:
    cmp ecx, 0          ; Check if all elements have been checked
    jle finish          ; If count <= 0, finish loop

    mov edx, [esi]      ; Load next number into edx
    cmp edx, eax        ; Compare current number with current largest (eax)
    jle not_larger      ; If edx <= eax, skip update
    mov eax, edx        ; Otherwise, update eax with the new largest value

not_larger:
    add esi, 4          ; Move to the next array element
    dec ecx             ; Decrement loop counter
    jmp find_largest_loop

finish:
    ; Store the final result into the uninitialized variable 'largest'
    mov [largest], eax

    ; Exit program (Linux syscall: sys_exit)
    mov eax, 1          ; System call for exit
    xor ebx, ebx        ; Exit code 0
    int 0x80            ; Call kernel
```

## Challenges: 
```txt
Some challenges included keeping track of which registers are used for loop counters, array indexing, and conditional comparisons without accidentally overwriting data.
Choosing the correct conditional jump instructions (such as jg for signed greater-than versus ja for unsigned) depending on whether the input integers are negative.
Also Setting up breakpoints (break _start) and using the watch command (watch (int) largest) correctly to monitor memory changes in real-time requires precise familiarity with GDB commands, particularly when stepping through instructions via stepi.
```

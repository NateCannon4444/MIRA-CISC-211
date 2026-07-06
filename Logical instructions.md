## Flowchart
```txt
Initialize var1 in .data section.
Phase 1 (XOR): Load var1 into EAX -> Execute xor eax, eax -> Store into result.
Phase 2 (TEST): Load val into EAX -> Execute test eax, 1 -> Observe ZF (Zero Flag) in EFLAGS -> Move result to memory.
Exit -> Terminate program
```
## Create a file named logical.asm
```asm
section .data
    my_val dd 10        ; Example value: 10 (Binary 1010)

section .bss
    result resd 1

section .text
    global _start

_start:
    ; Task 1: Demonstrate XORing operand with itself
    mov eax, 55
    xor eax, eax        ; EAX is now 0
    mov [result], eax

    ; Task 2: Practical use of TEST instruction
    ; Purpose: Check if a number is even or odd by testing the LSB.
    ; If the last bit is 0, the number is even (ZF=1).
    mov eax, [my_val]
    test eax, 1         ; AND operation with 1, flags only
    
    ; Logic: If ZF is 1, the number was even.
    ; In GDB, check 'info registers' or layout regs to see ZF change.
    
    ; Exit
    mov eax, 1
    mov ebx, 0
    int 0x80
```
## Challenges:
```txt
One challenge I faced was understanding that the TEST instruction does not alter the destination operand,
which initially made me think my code wasn't working until I realized I needed to monitor the EFLAGS register in GDB rather than the destination register itself.
 Additionally, mapping the logical bitwise operations to the binary representations in the registers was difficult,
but using the stepi command allowed me to verify how each logic gate transformed the data in real-time.
```

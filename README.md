# mpmc
---

### 1. Binary Search for a 16-bit Number

This program searches for a key element `X` in a list of 16-bit numbers using the Binary Search algorithm.

```assembly
.MODEL SMALL
.DATA
    ARR DW 1,2,3,4,5,6,7          ; Sorted array of 16-bit numbers
    LEN DW ($ - ARR)/2            ; Length of the array (number of elements)
    MSG1 DB "KEY IS FOUND$"
    MSG2 DB "KEY IS NOT FOUND$"
    X DW 6                        ; Key element to search for

.CODE
    MOV AX, @DATA
    MOV DS, AX

    MOV SI, 0000H                 ; SI = LOW index (starting index)
    MOV DI, LEN                   ; DI = HIGH index (last index)
    ADD DI, DI                    ; Convert index to byte offset
    SUB DI, 2                     ; Adjust for 0-based index in bytes

RPT:
    CMP SI, DI                    ; Compare LOW and HIGH
    JA KNF                        ; If LOW > HIGH, key not found

    MOV AX, X                     ; AX = key
    MOV BX, SI                    ; BX = LOW
    ADD BX, DI                    ; BX = LOW + HIGH
    SHR BX, 1                     ; BX = (LOW + HIGH) / 2 (MID index in bytes)

    CMP AX, ARR[BX]               ; Compare key with middle element
    JE KF                         ; If equal, key found
    JB NEXT                       ; If key < middle element, search left half

    ; If key > middle element, search right half
    MOV SI, BX                    ; LOW = MID
    ADD SI, 2                     ; Move to next element (byte offset)
    JMP RPT

NEXT:
    MOV DI, BX                    ; HIGH = MID
    SUB DI, 2                     ; Move to previous element (byte offset)
    JMP RPT

KF:
    LEA DX, MSG1                  ; Display "KEY IS FOUND"
    MOV AH, 09H
    INT 21H
    JMP EXIT

KNF:
    LEA DX, MSG2                  ; Display "KEY IS NOT FOUND"
    MOV AH, 09H
    INT 21H

EXIT:
    MOV AH, 4CH                   ; Exit to DOS
    INT 21H
END
```

### 2. Bubble Sort for 16-bit Numbers

This program sorts a given set of 16-bit numbers in ascending order using the Bubble Sort algorithm.

```assembly
.MODEL SMALL
.DATA
    ARR DW 0005H, 0004H, 0003H, 0002H, 0001H  ; Unsorted array
    LEN DB ($ - ARR)/2                        ; Length of the array

.CODE
    MOV AX, @DATA
    MOV DS, AX

    MOV CL, LEN                   ; CL = number of elements
    DEC CL                        ; Number of passes = n-1

LOOP2:                            ; Outer loop for number of passes
    MOV SI, OFFSET ARR            ; SI points to start of array
    MOV BL, CL                    ; BL = number of comparisons for this pass

LOOP1:                            ; Inner loop for comparisons
    MOV AX, [SI]                  ; Load first element
    ADD SI, 2                     ; Move SI to point to second element
    CMP AX, [SI]                  ; Compare first and second elements
    JB NEXT                       ; If first < second, no swap needed

    ; Swap the two elements
    MOV DX, [SI]                  ; DX = second element
    MOV [SI], AX                  ; Store first element in second position
    MOV [SI-2], DX                ; Store second element in first position

NEXT:
    DEC BL                        ; Decrement comparison counter
    JNZ LOOP1                     ; Repeat for this pass if comparisons left

    DEC CL                        ; Decrement pass counter
    JNZ LOOP2                     ; Repeat for next pass if passes left

    INT 3                         ; Stop program (breakpoint)
END
```

### 3. String Palindrome Check

This program reverses a given string and checks if it is a palindrome, displaying the appropriate message.

```assembly
.MODEL SMALL
.DATA
    STR DB "MODEL"                ; String to check
    LEN EQU ($ - STR)             ; Length of the string
    RSTR DB LEN DUP('$')          ; Buffer for reversed string
    MSG DB "Reverse string is: $"
    MSG1 DB "String is Palindrome$"
    MSG2 DB "String is Not Palindrome$"

.CODE
    MOV AX, @DATA
    MOV DS, AX
    MOV ES, AX                    ; For CMPSB instruction

    ; --- Reverse the string ---
    LEA SI, STR                   ; SI points to original string
    LEA DI, RSTR                  ; DI points to reversed string buffer
    ADD DI, LEN - 1               ; DI points to last character of buffer
    MOV CX, LEN                   ; CX = length of string

RPT:
    MOV AL, [SI]                  ; Get character from original string
    MOV [DI], AL                  ; Store it in reversed buffer
    INC SI                        ; Move to next character in original
    DEC DI                        ; Move to previous position in buffer
    LOOP RPT                      ; Repeat for all characters

    ; --- Display original string ---
    LEA DX, STR
    MOV AH, 09H
    INT 21H

    ; --- Display reversed string ---
    LEA DX, MSG
    MOV AH, 09H
    INT 21H
    LEA DX, RSTR
    MOV AH, 09H
    INT 21H

    ; --- Compare original and reversed strings for palindrome ---
    LEA SI, STR                   ; SI points to original string
    LEA DI, RSTR                  ; DI points to reversed string
    MOV CX, LEN                   ; CX = length of string
    REPE CMPSB                    ; Compare byte by byte
    JNE NOTPAL                    ; If not equal, it's not a palindrome

    LEA DX, MSG1                  ; Display "String is Palindrome"
    MOV AH, 09H
    INT 21H
    JMP EXIT

NOTPAL:
    LEA DX, MSG2                  ; Display "String is Not Palindrome"
    MOV AH, 09H
    INT 21H

EXIT:
    MOV AH, 4CH                   ; Exit to DOS
    INT 21H
END
```

### 4. Compute nCr using Recursion

This program computes the binomial coefficient nCr using a recursive procedure.

```assembly
.MODEL SMALL
.DATA
    N DW 6                        ; Value of n
    R DW 2                        ; Value of r
    NCR DW 0                      ; Variable to store the result

.CODE
    MOV AX, @DATA
    MOV DS, AX

    MOV AX, N                     ; AX = n
    MOV BX, R                     ; BX = r
    CALL NCR_PROC                 ; Call recursive procedure

    MOV AH, 4CH                   ; Exit to DOS
    INT 21H

; Recursive procedure to calculate nCr
; Input: AX = n, BX = r
; Output: Updates global variable NCR
NCR_PROC PROC
    CMP AX, BX                    ; Check if n == r
    JZ N1                         ; If yes, nCr = 1
    CMP BX, 0                     ; Check if r == 0
    JZ N1                         ; If yes, nCr = 1
    CMP BX, 1                     ; Check if r == 1
    JZ N2                         ; If yes, nCr = n
    MOV CX, AX                    ; CX = n
    DEC CX                        ; CX = n - 1
    CMP CX, BX                    ; Check if r == n - 1
    JZ N2                         ; If yes, nCr = n

    ; Recursive calls: nCr = (n-1)Cr + (n-1)C(r-1)
    PUSH AX                       ; Save n
    PUSH BX                       ; Save r
    DEC AX                        ; AX = n - 1
    CALL NCR_PROC                 ; Compute (n-1)Cr
    POP BX                        ; Restore r
    POP AX                        ; Restore n

    DEC AX                        ; AX = n - 1
    DEC BX                        ; BX = r - 1
    CALL NCR_PROC                 ; Compute (n-1)C(r-1)
    JMP LAST

N1:
    ADD NCR, 1                    ; nCr = 1
    RET

N2:
    ADD NCR, AX                   ; nCr = n
    RET

LAST:
    RET
NCR_PROC ENDP
END
```

### 5. Display System Time and Date

This program reads the current time and date from the system and displays it in a standard format on the screen.

```assembly
.MODEL SMALL
.DATA
    MSG1 DB "TIME IS$"
    MSG2 DB 10,13,"DATE IS$"

.CODE
    MOV AX, @DATA
    MOV DS, AX

    ; --- Display Time ---
    LEA DX, MSG1                  ; Display "TIME IS"
    MOV AH, 09H
    INT 21H

    MOV AH, 2CH                   ; Get system time
    INT 21H                       ; CH = hour, CL = minute, DH = second

    MOV AL, CH                    ; Display hour
    CALL DISPLAY
    MOV AL, CL                    ; Display minute
    CALL DISPLAY
    MOV AL, DH                    ; Display second
    CALL DISPLAY

    ; --- Display Date ---
    LEA DX, MSG2                  ; Display "DATE IS"
    MOV AH, 09H
    INT 21H

    MOV AH, 2AH                   ; Get system date
    INT 21H                       ; CX = year, DH = month, DL = day

    MOV BX, DX                    ; Save month (BH) and day (BL)
    MOV AL, BL                    ; Display day
    CALL DISPLAY
    MOV AL, BH                    ; Display month
    CALL DISPLAY

    MOV DL, '/'                   ; Display '/'
    MOV AH, 02H
    INT 21H

    ; --- Display Year ---
    MOV AX, CX                    ; AX = year
    MOV DX, 0
    MOV BX, 10
    DIV BX                        ; AX = quotient (year/10), DX = remainder (last digit)
    MOV CL, DL                    ; Save last digit in CL
    MOV DX, 0
    DIV BX                        ; AX = quotient (year/100), DX = next digit
    ADD DL, 30H                   ; Convert to ASCII
    MOV AH, 02H
    INT 21H                       ; Display first digit of year

    MOV DL, CL                    ; Retrieve last digit
    ADD DL, 30H                   ; Convert to ASCII
    MOV AH, 02H
    INT 21H                       ; Display last digit of year

    MOV AH, 4CH                   ; Exit to DOS
    INT 21H

; Procedure to display time components (HH:MM:SS)
DISPLAY PROC
    PUSH AX                       ; Save AX
    MOV DL, ':'                   ; Display ':'
    MOV AH, 02H
    INT 21H
    POP AX                        ; Restore AX
    AAM                           ; Convert packed BCD in AL to unpacked (AH=high digit, AL=low digit)
    MOV DX, AX                    ; DX = unpacked digits
    ADD DX, 3030H                 ; Convert to ASCII
    XCHG DH, DL                   ; Exchange to print high digit first
    MOV AH, 02H
    INT 21H                       ; Display high digit
    MOV DL, DH
    INT 21H                       ; Display low digit
    RET
DISPLAY ENDP
END
```

### 6. ARM Assembly Programs

These programs demonstrate basic data transfer, arithmetic, and logical operations on the ARM processor.

#### 6a. Data Transfer Instructions

```assembly
AREA PROG1, CODE, READONLY
ENTRY

START
    LDR R2, =0x05               ; Load the count of elements
    LDR R0, =SOURCE             ; R0 points to the source data
    LDR R1, =DEST               ; R1 points to the destination

UP
    LDR R3, [R0], #4            ; Load word from source, increment source pointer
    STR R3, [R1], #4            ; Store word to destination, increment dest pointer
    SUBS R2, #1                 ; Decrement counter, set flags
    BNE UP                      ; Loop until counter is zero

STOP
    B STOP

    AREA SOURCE, DATA, READONLY
    DCD 0x10, 0x20, 0x30, 0x40, 0x50   ; Source data

    AREA DEST, DATA, READWRITE
END
```

#### 6b. Arithmetic Instructions

```assembly
AREA PROG2, CODE, READONLY
ENTRY

START
    LDR R1, =0x00000006         ; R1 = 6
    LDR R2, =0x00000001         ; R2 = 1

    ADD R5, R1, R2              ; R5 = R1 + R2 = 7
    SUB R6, R1, R2              ; R6 = R1 - R2 = 5
    MUL R7, R1, R2              ; R7 = R1 * R2 = 6

STOP
    B STOP
END
```

#### 6c. Logical Operations

```assembly
AREA PROG3, CODE, READONLY
ENTRY

START
    LDR R1, =0x00000003         ; R1 = 3 (0011 binary)
    LDR R2, =0x00000007         ; R2 = 7 (0111 binary)

    AND R3, R1, R2              ; R3 = R1 AND R2 = 3 (0011)
    ORR R4, R1, R2              ; R4 = R1 OR R2 = 7 (0111)
    EOR R5, R1, R2              ; R5 = R1 XOR R2 = 4 (0100)

STOP
    B STOP
END
```

### 7. C Programs for ARM (KEIL)

These are simple C programs for the ARM microprocessor (LPC2148) to demonstrate arithmetic and logical operations.

#### Program 1: Arithmetic Operations

```c
#include <lpc21xx.h>

int main(void) {
    int a = 6, b = 2;
    int sum, sub, mul, div;

    sum = a + b;        // sum = 8
    sub = a - b;        // sub = 4
    mul = a * b;        // mul = 12
    div = a / b;        // div = 3

    while(1);           // Infinite loop to stop execution
}
```

#### Program 2: Logical Operations

```c
#include <lpc21xx.h>

int main(void) {
    int a = 3, b = 7;   // 3 = 0011, 7 = 0111
    int and, or, xor, not;

    and = a & b;        // AND = 0011 = 3
    or = a | b;         // OR = 0111 = 7
    xor = a ^ b;        // XOR = 0100 = 4
    not = ~a;           // NOT (of 3) = FFFFFFC on a 32-bit machine

    while(1);           // Infinite loop
}
```

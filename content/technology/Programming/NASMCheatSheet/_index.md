---
title: "NASM Cheat Sheet"
tag: programming
---

## Arithmetic
 - **Subtraction** 
	Subtract a value in a register or memory location (second operand) from a register or memory location (first operand) and store the result in the first operand location.
	- SUB <operand 1>, <operand 2>
	- SUB dest, src 
	- dest -= src
- **Addition**
	Add the value in a register or memory location (first operand) and an immediate value or the value in a register or memory location (second operand), and store the result in the first operand location
	- ADD dest, src
	- dest += src
- **Multiplication**
	- MUL
		Multiply unsigned value in the specified register or memory location by the value in AL, AX, EAX, or RAX and stores the result in AX, DX:AX, EDX:EAX, or RDX:RAX (depends on operand size). It puts the high-order bits of the product in AH, DX, EDX or RDX.
		- MUL register
			- mem8 - contents of AL * reg/memLocation and stores result in AX
			- mem64 - contents of RAX * reg/memLocation and stores the result in RDX:RAX (High bits stored in RDX. Low in RAX)
	- IMUL - **signed multiplication**- pg.180
		Multiplies two signed operands. The number of operands determines the form of the instruction
		- One operand: Specified register * RAX; product stored in RDX:RAX (High bits stored in RDX. Low in RAX)
		- Two operands: First operand * Second Operand; stored in first operand location
		- Three operands: Second operand * Third Operand; stored in First operand 
- **Division** 
	- DIV -  **unsigned divide** - pg.174
		Perform unsigned division of RDX:RAX by the contents of a 64-bit register or memory location and store the quotient in RAX and the remainder in RDX.
		- DIV src
		- RDX:RAX / src 
		- 
--- 
## Rotate
- RCL
	- rotate left with carry
- ROL
	- rotate left without carry
- RCR
	- Rotate right with carry
- ROR
	- Rotate right without carry
- - - 
## Registers 
### General Purpose Registers

|Registers|   |   |   |   |Description|
|---|---|---|---|---|---|
|64-bit|32-bit|16-bit|8 high bits of lower 16 bits|8-bit|
|RAX|EAX|AX|AH|AL|Accumulator|
|RBX|EBX|BX|BH|BL|Base|
|RCX|ECX|CX|CH|CL|Counter|
|RDX|EDX|DX|DH|DL|Data (commonly extends the A register)|
|RSI|ESI|SI|-|SIL|Source index for string operations|
|RDI|EDI|DI|-|DIL|Destination index for string operations|
|RSP|ESP|SP|-|SPL|Stack Pointer|
|RBP|EBP|BP|-|BPL|Base Pointer (meant for stack frames)|
|R8|R8D|R8W|-|R8B|General purpose|
|R9|R9D|R9W|-|R9B|General purpose|
|R10|R10D|R10W|-|R10B|General purpose|
|R11|R11D|R11W|-|R11B|General purpose|
|R12|R12D|R12W|-|R12B|General purpose|
|R13|R13D|R13W|-|R13B|General purpose|
|R14|R14D|R14W|-|R14B|General purpose|
|R15|R15D|R15W|-|R15B|General purpose|


![x86_64-registers-3396821814.png](/x86_64-registers-3396821814.png)

### Register Behavior Across Function Calls

|Registers|RAX|RBX|RCX|RDX|RSI|RDI|RBP|RSP|R8|R9|R10|R11|R12|R13|R14|R15|RIP|
|---------|---|---|---|---|---|---|---|---|--|--|---|---|---|---|---|---|---|
|Preserve across function calls|   |YES|   |   |   |   |YES|YES|   |   |   |   |YES|YES|YES|YES|N/A|
|Function parameter|   |   |4th|3rd|2nd|1st|   |   |5th|6th|   |   |   |   |   |   |N/A|
|Return register|1st|   |   |2nd|   |   |   |   |   |   |   |   |   |   |   |   |N/A|
|Kernel|\#NR|   |   |3rd|2nd|1st|   |   |5th|6th|4th|   |   |   |   |   |N/A
 
---
## The Stack
- LIFO - Last In First Out
- push
	- Automatically increments RSP by 8 bytes
	- Has to be a 64 bit register so that the stack is aligned
- pop
	- Automatically decrements RSP by 8 bytes
	- Has to be a 64 bit register so that the stack is aligned 
- The stack in intel,amd grows down
- Use RBP instead of RSP to access values on the stack 
	- This is because RSP is volatile 
		`mov ax, [RBP - 2]` first word
		`mov ax,  [RPB - 4]` second word
## Arrays
- Array addressing
	- size * offset + base
signed movsx

## Floating Point

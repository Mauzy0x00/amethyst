# NASM
Everything is a pointer* :]
## Segments 
   1. .data - Initialized data goes here
   2. .bss - Uninitialized data goes here
   3. .text - Program code goes here
## C instructions
   1. printf("Hello %d", input1);
	   1. %d tells the compiler that it will print a decimal and will then read the next argument 'input1'.
	   2. **zero out rax before printf if not using floating point values**. 1 in RAX for floating point
	   3. %-5d %5d aligns by 5 characters -- I think?
   2. scanf("%d", & input1);
	   1. %d tells the compiler that it will read in a decimal value and the next argument tells the compiler where to store that data.
## General notes
- 2s compliment trick for negative values
- keep all numbers from right to left until you hit the first 1
	- 1010 → 0110
	- 0110 → 1010
	- 0011 → 1101

 - Registers are not guaranteed to be cleared
 - **Initialized data** will start with d (define)  
	 - Ex. db (byte) dw (word 2-byte) dd (double 4-byte)
 - **Uninitialized data** will start with res (reserve)
	 - Reserving space in memory, but not assigning a value
	 - Ex. resb (byte) resw (word 2-byte) resd (double 4-byte)
 - Endian-ness 
	 -  Describes how a value is stored in memory
	 -  **Big Endian**
		 - Stored the typical way we would think
		 - Example: the double 0x12345678 is stored in memory as 12 34 56 78 
        - **Little Endian**
	        - Stored kinda 'backwards' (Least significant byte first)
	        - Example: the double 0x12345678 is stored as 78 56 34 12 
	        - Used on Intel and AMD (x86-64) processors 
 - Moving data
	 - Moving signed data: mov 0xFFFF to byte storage
		 - Becomes 0xFF (-1) is the same as 0xFFFF (-1)
		 - movezx  - zero out high bits and move larger to smaller register
		 - movesx  - smaller to larger register
	- Remember that a mov is really a copy 
	- Register/Stack movement
		- push    rax ; push contents of eax onto the stack
		- pop      rax ; pop stack top into eax
		- NOTE: pusha & popa do not exist in 64-bit NASM
		- **NOTE: for every push there needs to be a pop**
		- xchg    bl, bh ; swap contents of bl and bh
 - Arithmetic
	 - Add/Sub take source and destination
	 - At least one operand must be a resgister (no memory to memory)
		 - add   rax, rcx    ; RAX = RAX + RCX
		 - add   rcx, 21     ; RCX = RCX + 21
		 - add   rax, \[rcx]
	- Unsigned Multiplication
		-  mul    \<source>
		- Source can be register or memory address 
	- Division
		- works similar to mul
		- div only does unsigned division.
		- **The remainder**: (modulo) is stored in AH
		- **NOTE: Always need to clear RDX before dividing (16-bit or larger)**
		- When clearing.. use:
			- xor    rdx, rdx  
			- mov  rdx, 0
			- An xor operation is faster than mov
- Comparison
	- cmp   src1,     src2
	- This sets a compare flag
		- Unsigned: zero flag (ZF), carry flag (CF) compute src1 - src2, 
		- Signed: zero flag (ZF), overflow flag is (OF), sign flag (SF) compute src1 - src2
- Set 
	- setg   ah   ; AH = one of GREATER flag set
- Logic 
	- AND, OR, XOR
	- TEST
	- NOT
- Control Structures
	- Branch instruction
		- jcc (jump based on some condition code)
		- jmp (Unconditional, always taken, think goto)
			- Three address modes:
				- Short: 128 bytes away  (jmp short \<target>)
				- Near: 32K bytes away (jmp \<target>)
				- Far: (jmp far \<target>)
## Chapter 3
### Bit operations 
- Logical - AND, OR, NOT, XOR, NOR, NAND
- Shifts - Can multiply or divide by 2 with a shift
	- Arithmetic
	- Logical
- Operations
	- Bit masking (subnet mask)
	- Searching for specific bit sequences in input/data
	- Conditional branching (true/false)
	- Comparisons 
	- Faster math
		- Multiply/Divide, left-shift/right shift
#### Shifts
- op    register, imm
- op    register, cl              (low 8 bits of CX -- ECX, counter register)
	- Last bit always shifted out (captured in) to carry flag
- Logical - shifts bits left/right by amount
	- SHL: logical shift left (in zeros)
	- SHR: logical shift right (in zeros)
- Arithmetic
	- SAL: arithmetic shift left 
		Same as SHL
	- SAR: arithmetic shift right
	       1001  <- assumes signed 
		1100  <- shift 1 right and copy the sign bit | last 1 bit sent to CF (carry flag)
- Rotate
	- ROL: rotate left (MSB becomes LSB)
	- ROR: rotate right (LSB becomes MSB)
	- RCL: rotate left with carry (carry becomes LSB, MSB becomes carry)
	- RCR: rotate right with carry (carry becomes MSB, LSB becomes carry)
- Endian-ness
	- bswap ; swap byte order
	- set everything to one
		- xor EAX, EAX
		- not EAX       - does all the opposite 
### Subprograms
 - Methods, functions, procedure 
 - Make sure that the work done in a function call is worth it
 - Sequential execution 
 - Branches 
	 - Transfer control to some other instruction or location
	 - Often done via the jump instruction
		 - jg jz jc
	 - Unconditional jump
	 - jmp
 - Call
	 - Transfer control to a region of code (function, procedure, method, subprogram)
	 - When a subprogram is done, control returns to the statement that follows the "call"
	 - Promotes code reuse, modularity, improved readability & understanding, etc
	 - Before using call, ensure all arguments being sent to the function are in the correct address 
	 - When calling to a method you add parameters to the argument registers... Why? Because that is the standard. 
	  Call =
		 `push RIP`
		 `mov  RIP, <target>`
 - Data is stored on the stack
 - Addressing modes
	 - Immediate
	 - register
	 - direct
	 - indirect
	 - Segment registers
		 - mov ax, ss:disp\[rbx]
- If you use RBX, R12, R13, R14, R15 in your sub program you must save them via the stack before you start using them and when done, pop them in the order that you pushed them
	 Enter = 
		 `push  RBP`
		 `mov   RBP, RSP`
	 Leave = 
		 `mov   RSP, RBP`
		 `pop   RBP`
	 Ret = 
		  `pop  RIP`
### The Stack
- LIFO last in first out
- Manipulated with push & pop
- Stack grows "down" towards the heap
- Stores Activation Records (ARs) for subprograms 
- Call pushes RIP onto the stack and mov function into RIP
	-  Call is similar to a macro where is just does those two things
- The when you pop the stack it returns the stack pointer to where it was originally and the CPU will move to other things or what it was doing before. 
- **# of pushes and pops need to match**
####  Calling Conventions 
- When creating functions. Explicitly and IN DETAIL describe the function, all registers and what they are doing. This is essentially your documentation for the function. This helps you and anyone reading your code.
- Jochen recommends using RBP as the first local var 

 #### C: Local Variable Addresses 
	 - lea 
  C: Return Values
  - RDX:RAX
Any time you call lib C you have to make sure the stack is aligned to 16-bytes
 C: Data types
	- Global
	- Static
	- Automatic
	- Register
	- Volatile
You can change the aggressiveness of program optimization at compile time
#### Upon call
Caller 
- must store passed parameters
- store return address
- pass control to callee
    `push   RIP`
	`mov   RIP, asm_main`
	RIP is also your return address
Callee 
- Create new activation record on stack
- Preserve registers
- Allocate storage for local variables
#### Upon return
 Callee
 - Release allocated storage
 - Restore preserved registers 
 - store any returns values
 - restore AR for caller 
 - Get return address & pass control
 Caller
 - Resume control
### Loop
- Decrements the count register (RCX) by 1, then if RCX is not 0 and the ZF flag meets the condition specified it jumps to the target instruction specified by the signed 8-bit relative offset. The size of the count register used depends on the address-size attribute of the LOOPcc instruction
### Arrays 
- Collection of like data
- A block of contiguous memory
- Declaration depends on type of array
- Arrays defined in .text are stored in the stack. Dynamically allocated arrays reside in the heap
- Like everything else, **it is all just addresses**
- GCC wants stack to be 16 byte aligned 
	- So if you need space for 1 + 2\*4 +50\*2 = 109 ...
		- The real value to subtract from RSP should be 112, not 109 to ensure the stack is 8 byte aligned 
#### Accessing Array Elements 
- Accessing array elements is different for the array layout. In other words, if you create an array of double words you access data as \[array1] = array1\[0] \[array1+1] = array\[1] but for a word you increment by 2 because you are doing address arithmetic 
#### 2 Dimensional arrays
- Row major vs Column major
#### Array/String instructions
- RSI - Source index register (reading)
- RDI - Destination index register (writing)
- lodsb, sw, sd,sq  - load from accumulator 
- stosb, sw, sd,sq  - store into accumulator
- movsb, sw, sd, sq
- Repeat instructions
	- REP, REPE, REPZ, REPNE, REPNZ
##### Direction flags
- CLD - Clear direction flag:
	- Index registers get incremented 
- STD - Set direction flag
	- Index registers get decremented 
##### Common instructions 
- Bound - Check array bound
### LEA
- Load Effective Address (lea)
- Calculates address for you 
```
enter  4,0
;; .... code 
lea    rax, [rbp-4] ; rax = address of local dword
```

# Structures 
- When creating a struct, the memory allocated for each variable will have unused space to fit for the largest data type. GCC compiles code to by double-word aligned. 
- You can re-define how memory is stored using typedef and aligned()
### Memory Alignment 
- offsetof(S, b)
	- returns offset for element b in struct S 
- Bit fields
	- unsigned a : 3      // 3 bit field 
	- unsigned b : 10    // 10-bit field 
### NASM
- struct and endstruct 
	- Define data layout for a struct
- istruc and iend to instantiate an instance of a struct 
	- Allocate memory and assign values
- Make the struct local

### Floating Point
- IEEE standard 754 
- Float is 32 bit
	- sign bit
	- 8 bit exponent 
	- 23 bit significand  
- Double is 64 bit
	- sign bit 
	- 11 bit exponent 
	- 52 bit sgnificand
- Bias
	- Depending on double or float, subtract the bias from some value 0-255 to equal the exponent value
- Convert the following to IEEE single precision:
	-> 0.375
		1. Convert to binary
		Repeated multiplication X2
		> 0.375 * 2 = ==0==.75 
		> 0.75 * 2   = ==1==.5      
		> 0.5 * 2     = ==1==.0    <- decimal is 0 so done
		    > Float Binary value = 0.011	
		    > 1.1 * 2 ^-2
	 > | sign |      exponent       |                                 fraction                                     | 
	 >	|    0   |   0 1 1 1 1 1 0 1    |  1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0  |
	 
- Floating Point Arithmetic 
	- Don't check equivalency in floating point values 
	- Don't do if (x\==0)
	- Do if (|x| <  upselon)
- Instructions
	-Must use st0-st7 which worlds like a stack
	- FLD src    ; load single/double/extended precision number
	- FILD src   ; load/conver word/double-word int from meory 
	- FLD1         ; load 1
	- FLDZ        ; load 0

	- FST dst    ; store single/double precision number
	- FSTP dst  ; store single/double/extended precision number then pop st0
	- FIST dst    ; convert to int & store word/double word int number

	- FXCH  stX  ; swap st0 with stX
	- FREE stX    ; Delete register stX (marks it empty, not really empty)

	 - FADD src
	 - FADDP src

	- FSUB src
	- FSUBR src
	- FSUB
	- FSUBR  dst, st0  ; dst-=st0  ->  Changes order

### ARM 
- A fraction of the amount of instructions
- UK Computing company in 1978
- Targeted portable devices 
- Nokia 6110, first ARM GSM phone (1997)
- In 2013, ARM was in over 90% of mobile devices sold. 
- Still follows Classical Von Neuman Model 
- All program files define an "AREA" instead of a section
	- AREA
	- MyPROG
	- CODE
	- READONLY
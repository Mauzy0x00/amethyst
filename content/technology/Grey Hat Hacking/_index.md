The Ethical Hacker's Handbook
# Chapter 1

### Vulnerability Disclosure

- As long as we have humans developing software there will be vulnerabilities. Therefore, as long as we have vulnerabilities, users are at risk.
- It is therefore incumbent upon security professionals and researchers to prevent, find and fix these vulnerabilities before an attacker takes advantage of them, harming the user. _This is the ultimate mission of the grey hat hacker_

### White Hat Hacking

### Black Hat Hacking

### Grey Hat Hacking

## Know The Enemy: Black Hat Hacking

- [[The Art of War]] Sun Tzu - "If you know the enemy and know yourself, you need not fear the result of a hundred battles. If you know yourself but not the enemy, for every victory gained you will also suffer a defeat."

### Advance Persistent Threats (APTs)

- Use of advance forms of attack and they are persistent in nature.
    
    - Significant threat to the enterprise.
### Lockheed Martin Cyber Kill Chain

- A model to detail the persistent nature of APTs developed by Lockheed Martin in 2011.

![CyberKillChain.png](/static/CyberKillChain.png)

 - This model also describes the cost to remediate the further along the attacker gets.
 - Extension of the DoD targeting doctrine, using intelligence which gives indicators of enemy behavior. 
	 - In other words this shows common patterns of attackers 
## MITRE ATT&CK Framework 
- Goes deeper than the Cyber Kill Chain and allows us to get to tactics, techniques and procedures (TTP) of the attacker.
#### Cyber Threat Emulation (CTE)
- Once you know how the adversary acts, you can emulate their TTP and determine if your systems are secure 
# Chapter 2
Programming Survival Skills
## C programming language 
- `strcpy` - The most dangerous functions used in C.
	- This is dangerous because there are no checks on the size of the input and can lead to a [[Buffer Overflow]]. 
- `strncpy` - The safer alternative,  has a `<width>` parameter to ensure only a certain number of characters are copied from the source string to the destination string 

###### Compiling with GCC

| Option                         | Description                                                                                                               |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| `-o <filename>`                | Saves the compiled binary with this name.                                                                                 |
| `-S`                           | Produces a file containing assembly instructions saved with an .s file extension                                          |
| `-ggdb`                        | Produces extra debugging information; Useful when using GNU debugger (gdb)                                                |
| `-c`                           | Compiles without linking; produces object files with an .o exention                                                       |
| `-mpreferred-stack-boundary=2` | Compiles the program using a DWORD size stack, simplifying the debugging process while you learn                          |
| `fno-stack-protector`          | Disables the stack protection; introduces with GCC 4.1. This option is useful when you're learning about buffer overflows |
| `-z execstack`                 | Enables an executable stack. This option is useful when you're learning about buffer overflows                            |
## Computer Memory
- Bit 
- Nibble - 4 bits
- Byte - 8 bits
- Word - 2 bytes
- DWORD - 2 Words 
- QWORD - 4 Words 
### Random Access Memory (RAM)
- Any piece of stored data can be retrieved at any time. Thus the term *random access*. 
- With Intel/AMD, the memory is 32-bit or 48-bit addressable.
	- This means that the address bus the processor uses to select a particular memory address is 32 or 48 bits wide.
	- Therefore, an x86 processor the most memory that can be addressed is 256 Terabytes 
	- On x64 addressing can be expanded but 256TB has been sufficient. 
### Endian
- The order of bytes written into memory first
- Big-Endian - High order of bytes are written first (Motorolla)
- Little-Endian - Low order of bytes are written first (Intel)
### Segmentation of Memory 
- Each process needs to have access to memory and you don't want different processes overwriting a memory location of another process or task.
	(Critical Seciton)
- We segment memory to get around this. 
- Registers are used to store and keep track of the current segments a process maintains. 
- Offset registers are used to keep track of where in the segment the critical pieces of data are kept. 
- Each process running gets its own virtual address space and the amount of space depends on the system architecture, system settings and the OS.
- For example: A 32-bit Windows process is by default allocated 4GB.
	- 2Gb assigned to the user-mode 
	- 2Gb assigned to the kernel-mode
- Only a small portion of the virtual space is mapped to physical memory 
#### Programs in Memory
- When processes are put loaded into memory they are broken into different sections. 
##### 6 main sections
- **.text**
	- Code section
	- Contains machine instructions 
	- Marked as readable and executable
	- Size is fixed at runtime and will cause an access violation if a write attempt is made
- **.data**
	- Store global initialized variables
	- Marked as readable
	- Size is fixed at runtime
- **.bss**
	- "Below Stack Section"
	- Store specific types of global uninitialized variables
	- Size is fixed at runtime 
	- Readable, not writable
- **Heap**
	- Store dynamically allocated variables. 
	- Grows from lower-addressed memory to higher-addressed memory 
	- `malloc()`, `realloc()`, `free()`
	- Readable, writable, not executable 
		- Not executable because if an attacker gains control of a process then shellcode execution could easily be run in this region
- **Stack**
	- Recursively track function calls 
	- Stores local variables 
	- Grows from higher-addressed memory to lower-addressed memory 
	- A multi-threaded application has a unique stack for each thread
- **Environment/Arguments**
	- Stores a copy of system-level variables that may be required by the process during runtime
	- Command-line arguments stored here
	- ie. shell name, path, hostname
	- Writeable which allows its use in format string and [[Buffer Overflow]] exploits
The memory space of a process looks like this:
![[program memory sections.png]]
#### Buffers
- A place to receive and hold data until it is handled by a process 
- Fixed length declared in .data or .bss
#### Strings 
- Continuous arrays of character data in memory 
- Referenced in memory by the address of the first character 
- Terminated with a null character (\\0) in C
- '\\0' is an example of an escape character such as \\n (newline) and \\r (return)
#### Pointers 
- Special pieces of memory that hold the address of another piece of memory 
- Used because moving data around registers is slow. It is faster to pass pointers or locations of data in memory 
- Stored in 4 or 8 bytes of contiguous memory depending on whether the application is 32 or 64-bit. 
- Example:
	```
	int *pointer;
	printf("%d", *pointer)
	```
	- * is used to dereference the pointer, pointer, and display the value 
## Intel Processors 
(page 36)
### [[NASM Cheat Sheet#Registers]]

## Python Survival Skills

--- 
# Chapter 11
page 219
## Advanced Linux Exploits
--- 
# Chapter 12 
page 233
## Linux Kernel Exploits 
### The Linux Kernel
- Exploitation of the kernel follows the same principles as with user-space memory corruption bugs and its uncontrained access to memory and other resources provide attackers with unlimited power over affected systems. 
- These bugs can be found on kernel modules, drivers, system calls and other memory management implementation.
### Environment Setup 
- Qemu, Linux Kernel 5.14.17 and a simple kernel module that has bee deliberately made vulnerable to demonstrate the process of bypassing multiple Linux kernel runtime and compile-time exploit mitigation. 

---
# Chapter 14
page 281
## [[Windows Kernel Exploitation]] 

### The Windows Kernel 
- The kernel is implemented as the kernel layer, the executive layer and drivers.
- The kernel and executive layers are implemented in the kernel image, ntoskrnl.exe
- **Kernel Layer**
	- Contains code for thread scheduling, locking, synchronization and basic kernel object management 
- **Executive Layer**
	- Contains code for security enforcement, object management, memory management, logging, and Windows Management Instrumentation, among other things. 
- **Kernel Drivers**
	- Most kernel drivers are .sys files, but a few kernel components are DLLs (ie, hall.dll, ci.dll)
	- A .sys file is a **Portable Executable** file just like an EXE or DLL
	![[General Architecture of Windows.png]]
	- **User-mode**
		- Run on top of the Windows Subsystem (kernel32.dll, user32.dll etc), are built for the native API directly (ntdll.dll and win32u.dll), or are run as minimal/pico processes and talk directly to the System Service Dispatcher (AKA system call handler) which takes requests from user mode and dispatches them to the kernel.
	- Crossing the line between User-mode and Kernel-mode there is a jump in addresses. Memory is segmented like this due to historical and processor-specific reasons. It just so happens there are two distinct canonical memory spaces with a large non-canonical gap in the middle to divide memory belonging to kernel space (ring 0) and user space (ring 3).
	- Some drivers like graphics might talk directly to hardware while others use the **Hardware Abstraction Layer** (HAL). The HAL is an architecture- and platform-agnostic library for interacting with hardware. Recent versions of windows 10 (20H1+) implement the HAL inside of the kernel image and hal.dll is just a forwarding DLL that is still around for compatibility.
### Kernel Drivers
- Extensions to the kernel that can help the system:
	- Interact with previously unknown devices or file systems
	- Provide an interface for kernel introspection to user mode 
	- Modify how the kernel functions 
		- Heavily discouraged by Microsoft. MS introduced Kernel Patch Protection (AKA PatchGaurd) to prevent developers from tampering with core system routines and data structures. 
- Boot drivers
	- Loaded at boot by the bootloader
- Other drivers are loaded by service manager post boot 
- Only admins or users with *SeLoadDriverPrivilege* can load drivers on a Windows System
- Microsoft does not consider the boundary between sys admin and the kernel a security boundary since admins can load (nearly) arbitrary drivers anyway.
	- Drivers must have an acceptable digital signature in order to be loaded since kernel-mode code signing (KMCS) is enforced by default on all 64-bit machines.
##### Driver functions
- Can provide I/O routines in the form of *major functions*
- Windows Driver Kit (WDK) defines 28 major functions such as:
	1. Create
	2. Close 
	3. Power 
	4. I/O control
	5. Read 
	6. Write 
	7. Query Information 
	8. Set Information 
	9. Shutdown
	
	- These are constant integer values defined in the WDK headers
	- Major function's symbol names begin with `IRP_MJ_` and are indices into the major function array of the `_Driver_Object` starting at 0x70.
	- Major function handlers are also called driver dispatch routines and have the following protoype:
		```
		NTSTATUS DriverDispatch(
			_DEVICE_OBJECT *DeviceObject,
			_IRP *Irp
		)
		```

- Handlers for each major function are set inside of a driver's `_Driver_Object` structure when the driver is initialized.
	- The `_Driver_Object` structure contains information about the driver 
		- Name of the driver 
		- Linked list of devices associated with the driver 
		- Optional unload routine 
			- Called when 'driver unload' is requested
		- Memory bounds of the driver (start and size)
- A driver can create other associated `_Driver_Object` structures 
	- These represent a device that the caller driver is responsible for 
		- These devices cannot be backed by hardware 
		- An example of a driver not backed by hardware is Sysinternal Process Explorer.
			- Process Explorer is a Microsoft signed driver that is loaded when the tool starts and user-mode APIs are used to communicate with it. The driver then creates a user mode accessible device object and services requests from user mode via the I/O system in the kernel. Finally, the kernel's I/O system dispatches requests to the major function handler routine which is defined in the `_Driver_Object` the device belongs. 
##### Driver I/O
- **I/O Request Packet (IRP)**
	- Describes an I/O request to the device and has many fields
	- Notable fields:
		- `AssociatedIrp.SystemBuffer
			- Often includes and/or output buffer for the request
		- `Tail.Overlay.CurrentStackLocation
			- Information about the request relevant to the specific device being called. 
			- Important information in the `CurrentStackLocation` (IO_STACK_LOCATION) includes:
				- `MajorFunction` : current major function being requested.
				- `Parameters` : A massive union that contains different information depending on the major function being called.
- With device I/O control the `MajorFunction` will be `IRP_MJ_DEVICE_CONTROL` (14)
	- The `Parameters` field will describe the I/O Control (IOCTL) code being called as well as the input and output buffer sizes.
	- The input and output buffer will be in the `AssociatedIrp.SystemBuffer` field of the `_IRP` for most IOCTL calls.
		[IOCTL codes](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/defining-i-o-control-codes)

- Example request:
![[IO request.png]]
1. The subsystem calls an I/O system service to open a named file.
    
2. The I/O manager calls the object manager to look up the named file and to help it resolve any symbolic links for the file object. It also calls the security reference monitor to check that the subsystem has the correct access rights to open that file object.
    
3. If the volume is not yet mounted, the I/O manager suspends the open request temporarily and calls one or more file systems until one of them recognizes the file object as something it has stored on one of the mass-storage devices the file system uses. When the file system has mounted the volume, the I/O manager resumes the request.
    
4. The I/O manager allocates memory for and initializes an IRP for the open request. To drivers, an open is equivalent to a "create" request.
    
5. The I/O manager calls the file system driver, passing it the IRP. The file system driver accesses its I/O stack location in the IRP to determine what operation it must carry out, checks parameters, determines if the requested file is in cache, and, if not, sets up the next-lower driver's I/O stack location in the IRP.
    
6. Both drivers process the IRP and complete the requested I/O operation, calling kernel-mode support routines supplied by the I/O manager and by other system components (not shown in the previous figure).
    
7. The drivers return the IRP to the I/O manager with the I/O status block set in the IRP to indicate whether the requested operation succeeded or why it failed.
    
8. The I/O manager gets the I/O status from the IRP, so it can return status information through the protected subsystem to the original caller.
    
9. The I/O manager frees the completed IRP.
    
10. The I/O manager returns a handle for the file object to the subsystem if the open operation was successful. If there was an error, it returns appropriate status to the subsystem.

### Kernel Debugging
- A user-land (ring 3) debugger is only capable of debugging individual programs that run on top of the kernel. 
- A kernel-land (ring 0) debugger is required to debug the kernel. 
- Kernel debugging is usually done between two systems
		1. System running the debugger
		2. System being debugged 
	- Two systems are needed because unlike in a ring 3 debugger where a single program is suspended, stopping the whole kernel would prevent you from interacting with the system to run commands or resume it.
	- One exception to this is known as "local" kernel debugging
		- This allows the convenience of debugging the currently running system's kernel 
		- Drawbacks of this kind of debugging:
			- You cannot halt the running system. This means you can't set or inject any breakpoints or debug on a crash. 
			- Values in memory might be changing rapidly
- WinDBG is the only officially supported (and thus recommended) ring0 debugger for Windows. 
	- Offers many different transports over which to debug the kernel 
	- Network debugging is the most reliable, efficient and consistent setup for kernel debugging. 
## Lab 14-1: Setting Up Kernel Debugging
### Debugee
- As admin, run `bcdedit.exe /debug on`
- As admin, run `bcdedit.exe /dbgsettings net hostip:1.1.1.1 port:50000`
- Take note of your generated key and local ip address 
- Reboot to enter debug mode
### Debugger 
- Open WinDBG
- File > Attach to kernel
- Enter the port, key and IP address
- Once connected, hit 'Break'. Prompt should become active at breakpoint (int 3) that is at a kernel address starting with `0xfffff`
## Lab 14-3: Reverse Engineering the Driver 
- When searching for vulnerabilities in compiled programs it is good practice to start looking for function calls that manipulate memory 
	- `strcpy`, `memcpy`, `memmove`
- Open cross-references to the `memmove` function (x key in IDA)
	- Review each and trace the arguments (rcx, rdx, r8)
	- See if any can be controlled. 
	- Keep in mind that `_IRP->AssociatedIrp.SystemBuffer` and `_IO_STACK_LOCATION->Parameters.DeviceIoControl` structures are directly controllable from usermode.
	- Also keep in mind that `System Buffer` and `InputBufferSize` were moved into `DeviceExtension` at offsets 0 and 8 respectively.
	- We can look for positions where we can manipulate data. This is easier when we see jumps and tests comparisons
- Note the `memmove` call in `sub_15294`
	- Locate which values are passed into the parameters for `memmove` by tracing back in the program. This way we can determine where the parameter values originate
- We now know that:
	- rax : from rbx+0x10
	- r9 : from rbx 
	- rcx : from rdi in the major function handler
- We determined earlier that rdi holds a pointer to `DeviceExtension` which holds a pointer to `SystemBuffer` (a user input buffer!) at offset 0 and the size of the user input buffer at offset 8
	- This means that r9 in sub\_15294 is a pointer to the input buffer which should give us control of at least the source/destination *and* the size of the call to `memmove`

- Next, we need to figure out how to reach this code path.
	- We look for which IOCTL codes lead to he preceding block. We see two: 
		- One zeros out edx 
		- One moves 1 into dl
	- Remember that in `sub_15294`:`loc_152E1` there is the test of dl which determines if we use the pointer from r9 as the source or the destination of the `memmove` call.
	- Tracing up the two, we see the two IOCTL codes:
		- `mov dl,1`:
		![[Pasted image 20240814013758.png]]
		- `xor edx,edx`
		![[Pasted image 20240814013851.png]]
## Lab 14-4: Interacting with the Driver
- Attach kernel debugger and get the offset of the `memmove` function.
	- In IDA, we can calculate the offset by having our cursor on the function and  running `get_screen_ea() - get_imagebase()`
	- Note: Offset=  \<function> - \<imagebase>
	- in this case IDA returns 5301h which is ofc `0x5301`
		- Image base: `0x10000` Function: `0x15301
- Set a breakpoint on the function in WinDBG
	- Issue the `bp` command with the driver name and relative offset
		- `bp dbutil_2_3+0x5301`
		- use `.reload` if windbg is angry abt resolving that bp

### The Code
- Written in Rust: https://github.com/Mauzy0x00/dbutil_2_3-Exploit
- Try hitting the breakpoint
	- run `cargo --bin iocltcall 0x9B0C1EC4 112233445566778899101112131415161718192021222324`
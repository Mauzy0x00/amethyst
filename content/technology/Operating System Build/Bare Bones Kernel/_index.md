# Booting the Operating System
- The bootloader, such as GRUB, used to start/load the operating system.
- The operating system needs to handle when the bootloader passes control to it. 
- The kernel is passed a very minimal environment where the stack is not yet setup.
- Because there is no stack yet, we must make sure global variables are set correctly.
	- This is done is assembly 

# Bootstrap Assembly 
`i686-linux-gnu-as boot.s -o boot.o`
1. Multiboot header
	- This marks the program as a kernel. There is magic here ig
2.  Define the value of the stack pointer register (esp) - x86 bruv
	- The stack on x86 bust be 16-byte aligned according to the System V ABI standard and de-facto extensions. 
	- The compiler will assume that the stack is properly aligned and if it is not it will cause strange behavior.
3. Kernel entry point
	- \_Start specifies the entry point to the kernel. Once this is loaded, it won't return because the bootloader has completed it's task and is no longer in memory.
					- [[Further Reading and Concepts#Kernel Entry Point]]
	- At this point the bootloader has loaded into...
		- 32-bit protected mode as x86
		- Interrupts are disabled
		- Paging is disabled
		- Processor is defined in the multiboot standard 
		- There are no security restrictions, safeguards or debugging
			- **The kernel has absolute and complete power over the machine**
4. Set stack pointer
	-  The processor is not fully initialized yet .. missing:
		- Floating point instructions 
		- Instruction set extensions
	- The point between calling the main kernel and setting up the stack is a good time to initialize these features
5. Enter the high-level kernel
	- The [[Further Reading and Concepts#ABI|ABI]] requires the stack is 16-byte aligned at the time of the call instruction (call kernel_main) and will push the return pointer with the size of 4 bytes.
6. Loop forever 
	- If the system doesn't have anything else to do after calling kernel_main, it will infinitely loop. To do that:
		1. Disable interrupts with cli (clear interrupt enable in eflags). They are already disabled by the bootloader, so this is not needed. Mind that you might later enable interrupts and return from kernel_main (which is sort of nonsensical to do).
		2. Wait for the next interrupt to arrive with hlt (halt instruction). Since they are disabled, this will lock up the computer.
		3. Jump to the hlt instruction if it ever wakes up due to a non-maskable interrupt occurring or due to system management mode.
# The kernel
- The bootstrap assembly stub sets up the processor so that high level languages like C can be used. 
### Freestanding and Hosted Environments
- Any programming done in user-space is using a **Hosted Environment**.
	- Hosted means that there is a C standard library and other runtime features. 
	- The alternative is the **Freestanding** version which is what we are using here.
		- This means there is no C standard library, only what is provided by you.
		- Some header files are actually not part of the C standard lib but are the compiler. These remain available even in freestanding C source code.
		> In this case you use <stdbool.h> to get the bool datatype, <stddef.h> to get size_t and NULL, and <stdint.h> to get the intx_t and uintx_t datatypes which are invaluable for operating systems development, where you need to make sure that the variable is of an exact size (if you used a short instead of uint16_t and the size of short changed, your VGA driver here would break!). Additionally you can access the <float.h>, <iso646.h>, <limits.h>, and <stdarg.h> headers, as they are also freestanding. GCC actually ships a few more headers, but these are special purpose.
### Writing the kernel
- This kernel uses the VGA text mode buffer (located at `0xB8000`) as the output device. It sets up a simple driver that remembers the location of the next character in this buffer and provides a primitive for adding a new character. 
- Note: There is no support for line breaks (\\n) (and writing that character will show some VGA specific character instead) and no support for scrolling when the screen is filled up. Adding this is your first task. 

- **IMPORTANT NOTE:** The VGA text mode (as well as the BIOS) is depreciated on newer machines and UEFI only supports pixel buffers. For forward compatibility you might want to start with that. Ask [GRUB](https://wiki.osdev.org/GRUB "GRUB") to set up a framebuffer using appropriate Multiboot flags or call [VESA VBE](https://wiki.osdev.org/Vesa "Vesa") yourself. Unlike VGA text mode, a framebuffer has pixels, so you have to draw each glyph yourself. This means you'll need a different `terminal_putchar`, and you'll need a font (bitmap images for each character). All Linux distro ships [PC Screen Fonts](https://wiki.osdev.org/PC_Screen_Font "PC Screen Font") that you can use, and the wiki article has a simple putchar() example. Otherwise everything else described here still stands (you have to keep track of the cursor position, implement line breaks and scrolling etc.)
### Linking the Kernel
Now, boot.s and kernel.c can be assembled which will produce two object files; Each containing a part of the kernel. To create a full and final kernel you will have to link these object files into the final kernel program which is usable by the bootloader. 
- When developing user-space programs, your toolchain ships with default scripts for linking such programs. However, these are unsuitable for kernel development and you need to provide your own customized linker script.. linker.ld
- Note: Some tutorials suggest linking with i686-elf-id rather than the compiler.

### Verifying Multiboot
- Check if GRUB has a valid Multiboot version 1 header. This is the case for this kernel.
- The Multiboot header is within the first 8 KiB of the actual program file at 4 bytes alignment. 
	- This can break later if a mistake is made in the boot assembly, linker script or anything else that might go wrong. 
	- If the header isn't valid, GRUB will give an error that it can't find a Multiboot header when trying to boot. This code fragment will help with diagnosis:
		- `grub-file --is-x86-multiboot myos.bin`
		- grub-file is quiet but will exit 0 (successfully) if it is a valid multiboot kernel and exit 1 (unsuccessfully) otherwise. 

# Booting the Kernel
## Building a bootable cdrom image
- This is done using the program grub-mkrescue
	- May need to also install grub utility programs and xorriso (v0.5.6+)
```
menuentry "myos" {
	multiboot /boot/myos.bin
}
```
Note that the braces must be placed as shown here. You can now create a bootable image of your operating system by typing these commands:

```
mkdir -p isodir/boot/grub
cp myos.bin isodir/boot/myos.bin
cp grub.cfg isodir/boot/grub/grub.cfg
grub-mkrescue -o myos.iso isodir
``` 

- mformat is a part of GNU mtools. Install it using pacman -S mtools.

---


# Meaty Skeleton
https://wiki.osdev.org/Meaty_Skeleton


# Unix 
Unix is a proprietary operating system that was developed in the 1970s by AT&T Bell Labs. It is mainly used for enterprise-level servers and workstations, and has many variants such as AIS, BSD, Iris, and HP-UX. Unix has a graphical user interface (GUI) similar to Windows, but can also use other GUIs such as Gnome. Unix supports multiple users and multitasking, and was the platform for building the internet and the world wide web. [Unix requires a license to use and is not open-source](https://www.coursera.org/articles/unix-vs-linux)

# Linux
Linux is a free and open-source operating system that was developed in the 1990s by Linus Torvalds as an alternative to Unix. It is widely used on both enterprise and personal computers, and has many versions such as Ubuntu, Debian, Solaris, and Redhat. Linux has many GUI options, such as Gnome, KDE, Unity, and Mate. Linux also supports multiple users and multitasking, and is compatible with most hardware and software. [Linux is community-driven and has a large and active user base](https://www.coursera.org/articles/unix-vs-linux)

# Nix
Nix is a relatively new operating system that was developed in 2003 by Eelco Dolstra. It is based on the Nix package manager, which allows for reproducible and reliable software installations and configurations. Nix is also a free and open-source operating system, and can run on top of other operating systems such as Linux, MacOS, and Windows. Nix has a unique approach to managing system state, using functional programming concepts and immutable data structures. [Nix aims to provide a high-performance, secure, and portable operating system](https://www.softwaretestinghelp.com/unix-vs-linux/)[4](https://www.softwaretestinghelp.com/unix-vs-linux/) .

# Linux
- System calls run in kernel mode on the user's behalf and are provided by the kernel itself 
- A library function calls one or more system calls and these system calls execute in supervisor mode since they are part of the kernel itself 
- Once the system call completes its task, it returns and execution is transferred back to user mode 
- The user space application is typically blocked until the library function and system call return (just like a function call)
- System calls may interact with the kernel proper or with specific drivers and frameworks for the kernel
## Device drivers
- Linux Device model 
	- Built around the concept of busses, devices and drivers
	- All devices are connected to a bus of some kind
	- A bus can be software, not just physical 
	- Busses primarily exist to gather similar devices together and coordinate initialization, shutdown and power management 
	- When a device in the system is found to match a driver, they are bound together. How they are matched depend on the bus.
- Device Types
	- Network devices
	- Block devices 
		- Provide userspace apps access to raw storage devices (hardrives, USB)
		- Visible to applications in /dev
	- Character Devices 
		- Provide userspace apps access to all other devices I/O
		- Visible to apps in /dev
	- MTD devices 
		- Flash memory is a unique device type that has translations to allow them to be used as block and character devices 
- Kernel Frameworks 
	- Device drivers are not directly implemented as character or block devices. They are implemented under a framework to a specific device type (framebuffer, V4L, serial etc..)
	- The framework factors out the common parts of drivers for the same type of devices to reduce code duplication
	- From the userspace, many are still seen as normal character devices 
	- Provides a coherent userspace interface (ioctl numbering and semantics)
- Kernel Layers 
	![[Driver Interface Model.png]]
- Virtual File Systems 
	- System and kernel information is sent to the userspace in the virtual file system
	- This is created dynamically and only exists in memory
	- proc
		- mounted at /proc
		- Contains operating system related information (Processes, memory management params, etc..)
		- This is old and became chaotic 
	- sysfs
		- mounted at /sys
		- Contains a representation of the system as a set of devices and buses 
		- Newer and is preferred to add system information
	- The kernel exports items to the userspace through sysfs
		- `/sys/bus/` contains the list of buses
		- `/sys/devices/` contains the list of devices 
		- `/sys/class/` enumerates devices by class (net, input, block etc..)
## Kernel Modules
- Linux is monolithic but also modular 
- The kernel can dynamically load and unload parts of the kernel code. These are called kernel modules 
- Modules allow the kernel capabilities to be extended without modifying the rest of the code or rebooting the kernel 
- A kernel module can be inserted or removed while the kernel is running
	- This can be done by a root user or userspace script at boot
- Modules keep the kernel size to a minimum and flexible 
- Reduces boot time 
- Has full control and privileges once loaded
#### Module Details
- Naming convention: \<filename>.ko
- Location: `/lib/modules/<kernel_version>`
# Kernel
## Monolithic Kernel 
- Much functionality is in the kernel 
## Micro-kernel 
- More functionality in user mode
- Much smaller

# X Protocol
(xorg)
- Server - The software that manages one display, keyboard, and mouse.
- Client - A program displaying on the screen and taking input from that keyboard and mouse.
1. A client send drawing requests and information requests to the server
2. The server sends back to the client user input, replies to information requests and reports any errors.
	- The client can be running on the same machine or on a machine on the network. 
- X is not limited to a single client 
- Both local and network connections can be operated in the same way using the protocol
	-This makes the network transparent from both the user's POV and from the application programmer's POV
- Can be used over any reliable byte stream
- Window manager - A client that has authority over the layout of windows on a screen. 
	- Some X protocol features are used only by the window manager to enforce this authority. Otherwise, the window manager is just like any other client. 
- Most clients are programmed in C, some in Lisp
### Message Types
- X specifies four types of messages that can be transferred over the network. 
	- Requests
		- Generated by the client
			- Contains a wide rage of information including specifications for drawing a line or changing color value in a cell in a colormap, or an inquiry about the current size of a window 
			- A protocol request can be any multiple of 4 bytes in length
	- Replies
		- Send from the server to the client 
			- Not all requests are answered by replies-only the ones that ask for information.
			- Requests that specify drawing do not generate replies.
			- Requests that inquire about window size to generate replies.
			- 4 bytes in length (minimum of 32 bytes)
	- Events 
		- Sent from the server to the client 
			- Contains information about device action or side effects of a previous request. 
			- Data in these requests is varied because it is a principal method by which clients get information.
			- Stored in 32-byte structure to simplify queuing and handling them. 
	- Errors
		- Similar to an event but is handled differently by clients 
			- Errors are sent to an error-handling routine. Error messages are the same size as events to simplify handling them. 
Works best on Linux; Sucks on Windows

## Set up on Arch Linux
- Install Qemu
- Install virt-manager 
- Allocate memory for virtual machine 
- Manage machine with virt-manager 
## Storage allocation qcow2
- qemu-img create myimage.img mysize
	- where myimage.img is the disk image filename and mysize is its size in kilobytes. You can add an `M` suffix to give the size in megabytes and a `G` suffix for gigabytes.
	- `qemu-img create -f qcow2 my_image.qcow2 15G`
	- `qemu-img convert -f raw -O qcow2 my_image.iso my_image.qcow2`
	- `qemu-system-x86_64 -drive file=Windows11.qcow2,index=0,media=disk,if=virtio -drive file=Win11_23H2_English_x64v2.iso,index=2,media=cdrom -drive file=virtio-win-0.1.240.iso,index=3,media=cdrom -m 8G`
### Windows 11
Need the Proper virtual drivers from virtio and tmp2
	- yay -S tpm2-tools
### Networking
- With ethernet, a bridge can be used. This can also be done with WiFi but it looks like that is not recommended. 
- Not totally sure what i did to fix it. changed a few things at once :(
## Video
- Had issues with artifacts in Kali.
  - I noticed I only had the base package for qemu which might have been missing packages for spice. 
  - Deleted qemu-base and installed qemu-full. Re-setup VM using virtio. now it looks good
A record of issues and changes on my Arch Linux installation
## Screen freezing, shuddering, turning off 
(9/8/23)
#### Description  
   Since updating using `pacman -Syu` a few days ago I observed the screen freezes, glitches, and will get worse over time. it begins to go black with a RGB matrix across the top of the screen.  
#### Investigation  
1. journalctl -xe  
   1. Unit: 'kernal' Message: 'ACPI Error: No handler for Region \[RTCM]'
       Forums say it is safe to ignore this error.     
   2. Unit: 'kwin_x11' Message: 'kwin_core: XCB error: 152 (BadDamage), ... ma>  
       Forums seem to indicate that this is more for development purposes. Whe>  
2. pacman -Qdt  
   There was one orphaned package lua52 (5.2.4-5). Will wait to see if that re>  
   ! This seems to have solved the issue... (9/9/23)  
   [[Pacman |pacman -Qdt]] Solved this issue
   #pacman
## Password Lockout 
(9/9/23)
#### Description
   Because I type very quickly I often input my password incorrectly doing so 3 times will lock me out for 5 minutes. This hurts productivity. I want to remove the lock out.
#### PAM and Security
   pam_faillock.so is enabled by default. (deny = 0 will disable lock)
	   Values in /etc/security/faillock.conf changed 
	   1. unlock_time = 300 
	   2. deny = 5


## Added user Mauzy to group libvert 
(9/9/23)
   This was done so that a password was not required to set up the libvirt network each time a VM was started.
   `sudo gpasswd -a mauzy libvirt`

## Installed KDE wallpaper-engine
(9/14/23)
   Follow Github instructions. Download wallpaper engine from steam using the compatibility setting.
   - It did not work out of the box switching the video backend to 'Mpv' made it work as deisred
(9/20/23)
	Scenes and other types are non-functional
    - `qmake-qt5 -query QT_INSTALL_QML`
	    - this gives the output:
	        `QT_INSTALL_QML`
			`/usr/lib/qt/qml`
		- Moving libWallpaperEngineKde.so and qmldir to that directory
			- `mauzy@Silence ~/Programs/wallpaper-engine-kde-plugin/build/src $ sudo mv libWallpaperEngineKde.so /usr/lib/qt/qml`
			- `sudo mv qmldir /usr/lib/qt/qml`
			- restart kde
			- nope
			- removing file at dir /usr/lib/qt/qml/com/github/catsout/wallpaperEngineKde/libWallpaperEngineKde.so
				- caused:![[Pasted image 20230920233828.png]]
				- Copied the .so plug in file back into the dir
			- Removing `~/.config/plasma-org.kde.plasma.desktop-appletsrc`
				- No change, rebooting
				- That borked all of my kde settings and now my steam wallpaper library isnt there... lol. remember to see what a file is before deleting...
	Deleting plug-in
## Wifi dropping 
(9/23/23)
wlp0s20f3: CTRL-EVENT-SIGNAL-CHANGE above=1 signal=-50 noise=9999 txrate=245000
List running services 
- systemctl list-units --type service --no-pager
Not sure what this does exactly but it does something
- journal -f

## Installed zsh
(29 Sep 2023)
- `chsh -s $(which zsh)`
- WOW this is so much cooler 
- Installed Oh My Zsh for configs and theme management 

## QEMU/KVM
(30 sept 2023)
- My root partition was full and noticed that virt manager created my kali qcow2 in my root partition. I moved the .qcow2 file into my home partition and changed the virtmanager settings. Upon doing that there was still an error and something in the xml was still looking in /var/lib/libvirt/images/kali-linux.qcow2. Going to /etc/libvirt something something I tried to edit the kali-linux.xml with nvim. the header of the file said it was automatically generated and should be edited with virsh edit. Idk how to use this thing ...
- Okay so it's using vim but has no indication of that so it was difficult since im just learning vim. anyway... I changed the path that it looks for to the path that i moved the .qcow2 file
- no bueno... 
---
## Adding a network bridge for QEMU VM

1. `sudo virsh net-start default`

https://wiki.archlinux.org/title/QEMU#Bridged_networking_using_qemu-bridge-helper
sooo.. looks like virt-manager made its own bridge n everything.
Maybe just because i didn't have bridge-utils it didn't work.....


1. Install the bridge utils 
	Note: bridges are not allowed on the hardened kernel. It will break your system.
	- `yay -S bridge-utils`
	
2. Create and start your bridge
	- `ip link add name br0 type bridge`
	- `ip link set dev br0 up`
	
3. Create the qemu directory in `etc` if it is not there and add your new bridge to the bridge.conf to allow it.
	- `! -d /etc/qemu && mkdir /etc/qemu
	- `echo allow br0 > /etc/qemu/bridge.conf
	Note: make sure /etc/qemu has permissions 755
	
4. Only one of the following is needed: 
- `sysctl net.ipv4.ip_forward=1
- `sysctl net.ipv6.conf.default.forwarding=1
- `sysctl net.ipv6.conf.all.forwarding=1
	View current settings saved in kernel memory with `sysctl -a` and grep net.ipv
	
5. Use the qemu bridge helper to configure the bridge:
		`qemu-system-x86_64 -nic bridge,br=_br0_,model=virtio-net-pci`

---
## BurpSuite and Java
- Burpsuite not starting
	- `Unrecognized option: --add-opens=java.base/java.lang=ALL-UNNAMED 
	- `Error: Could not create the Java Virtual Machine.`
- Java 
- archlinux-java is a cli tool 
- `archlinux-java status 
	`Available Java environments:
	  `java-21-openjdk
	 `java-8-openjdk/jre (default)`
	- Because the default is java 8 and jre this error is occurring
- Use `sudo archlinux-java set java-21-openjdk` to change the default
- Burp will now start
## Hyprland XWayland Applications 
- Applications like obsidian discord and spotify are blurry
- Create the electron25-flags.conf file and insert the following lines. This fixes obsidian but not discord and spotify 
```
cat electron25-flags.conf
--enable-features=WaylandWindowDecorations 
--ozone-platform-hint=auto
```
- Hypr.conf and insert the following
```
# unscale XWayland
xwayland {
  force_zero_scaling = true
}

# toolkit-specific scale
env = GDK_SCALE,2
env = XCURSOR_SIZE,32
```
- Display issue with bottom of screen. Looks like part of the screen is missing
- Adding `monitor = ,highres,auto,1.4` 1.4 scale fixed the issue. 
	- Note, scale needs to be easily divisible cannot be an odd number
	- On hyprdots. Changing theme and wallpaper throws an error that 1.4 is not divisible 

# Alpha Network Adapter 
- Install driver for Realtek Semiconductor Corp. **RTL8812AU** 802.11a/b/g/n/ac 2T2R DB WLAN Adapter
	- [rtl8812au-dkms-git (AUR)](https://aur.archlinux.org/packages/rtl8812au-dkms-git/) 
		- This driver did not show the dongle under `ip link`
		- Uninstalled
- Make sure driver module loads on system boot (e.g. create file `/etc/modules_load.d/alfa-wifi-dongle.conf` with just one line: `8812au`, if that is the name of installed driver.).

## Hyprland power consumption
- Laptop having noticeably shorter battery life. 
- According to github. the border angle animation increases power consumption greatly. 
- https://github.com/prasanthrangan/hyprdots/issues/641
- use `Hyprdots power save -ba` to disable border angle animations
- `sudo powertop --html=report.html`
# VSCode and Github 
- An OS keyring couldn't be identified for storing the encryption related data in your current desktop environment
- https://code.visualstudio.com/docs/editor/settings-sync#_troubleshooting-keychain-issues
### Solution
`code --password-store="kwallet5"`
- If that works, make the value persist:
	- Open the command pallet (CTRL+SHIFT+P)
	- Run the Preferences: Configure Runtime Arguments
		- "password-store": "kwallet5" in argv.json
--- 
## yay + pacman cache
- Yay, specifically electron26, is taking up a ton of space in .cache.
- yay and pacman cache source code and binaries here after updates. These files must be handled by the user. 


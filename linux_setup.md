# Hardware
## Caveats
On some machines Ubuntu detects "Built-in Display".
Disable it in `Settings>Displays`, otherwise apps' windows may be
located at this phantom display, invisible on a physical monitor
(but visible via VNC).

# Disk management
## BIOS or EFI/UEFI check in Windows
* Open `C:\Windows\Panther\setupact.log`
* Look for entries like\
`   Code:Callback_BootEnvironmentDetect: Detected boot environment: BIOS
    Code:Callback_BootEnvironmentDetect: Detected boot environment: UEFI`

### Windows + Ubuntu - BIOS mode
This is how to use Windows Boot Menu (not Grub).
This works only when Windows and Linux are installed
on the same physical drive.
Make sure that **Windows Fast Startup** has been disabled.
1) Install Linux
* Create new partition as Primary, ext4, Mount point: /. Assume that the new partition is /dev/sda4.
* Select Device for boot loader installation to be the same as the new partition: /dev/sda4

2) Getting boot sector
Need access to Unix `dd` tool. Two possibilities:
* On windows, use cygwin or git with bash, run as administrator; 
```
    cat /proc/partitions
```
and look for sdaX
```
    dd if=/dev/sdaX of=/cygdrive/c/ubuntu_17.04.bin bs=512 count=1
```
* Reboot the Linux from the installation media again, picking Try without installation.
* Tap the windows disk icon, so it will be mounted e.g. under /media/win10
```
    sudo -i
```
(If you forget the Linux partition path, try `fdisk -l`)
```
    dd if=/dev/sda4 of=/media/ubuntu/win10/ubuntu_17.04.bin bs=512 count=1
```

Notes:
* After substantial updates the boot sector file has to be re-generated.
* MBR consists of 512 bytes, 448 bytes for the bootstrap and 64 bytes for the partition table, so 448 bytes would be sufficient.

3) Add an entry in boot manager
* Automatically, script `add_linux_boot_entry.bat`
* Manually:
```
    bcdedit /create /d "ubuntu_18.04" /application bootsector
```
Here we obtain `{id}` and:
```
    bcdedit /set {id} device partition=d:
    bcdedit /set {id} path \ubuntu_18.04.bin
    bcdedit /displayorder {id} /addlast
```

# Linux setup
This is about Ubuntu 18.04 LTS

## Setup
Select "Try without installing", so u can set up proxy
(so setup may download updates during installation),
run Gparted etc. Before runnig Install Ubuntu from tool bar icon.

Refer Disk Management for notes.

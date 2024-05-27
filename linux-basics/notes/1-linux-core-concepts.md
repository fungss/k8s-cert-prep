# Linux Core Concepts

## Linux Kernel
Core interface between hardware and processes/applications. Performs
- Memory Management
- Process Management
- Device Drivers
- System Calls and Security

It is monolithic (cpu scheduling, mem-mngt by itself) and modular (extends its capability thru use of dynamically loaded kernel module)

To check kernel version
```
uname / uname -r
```
23.4.0 -> kernel 23, major 4, minor 0

4.15.0-72-generic -> patch ver 72 and generic as distro-specific-info

## Kernel Space and User Space
Memory is divied into Kernel Space (kernel mode) and User Space (user mode).

Kernel Space 
- where kernel executes and provide its services
- Process in kernel space has unrestricted access to hardware
- strictly reserved to run kernel code, kernel extensions, and device drivers

User Space 
- all processes run outside kernel space
- has restricted use of cpu and mem
- e.g. utilities, programs written in C/Java/Python, and graphical tools
- applications in user space make system call to manipulate data in mem/disk <-> students make requests to librian for access to books

## Working w/ Hardware
udev as device manager of linux OS. sits in Users Space.
When USB Disk inserted -> Device Driver detected (Kernel Space) and send uevents to udev (Users Space) which then associate the disk w/ a device node /dev/sdb1.

udevadm
- management tool for udev

dmesg
display messages from kernel's ring-buffer

lspci, lsblk, lscpu, lsmem, lshw
- 32/64 bit -> register size, capacity to load data from mem and compute
- socket -> physical slot for cpu
- vCPUs -> Sockets x Cores per socket x Threads

## Linux Boot Sequence
- BIOS POST -> check if hardware connected
- Boot Loader -> loads kernel
- Kernel Initialization
- INIT Process (systemd)

## Systemd Targets (for systemd == Run Levels in sysV)
To reveal Systemd Target
```
who -r
```

## Filesystems and Hierarchy
file types
- regular file
- directory
- specifal files
char files, block files (harddisks and RAMs), links (hard links, soft links == pointer), sockets files, named pipes

```
file or ls -ld path/to/file
```

Filesystem Hierarchy
- home, home dir
- opt, third party program
- mnt, mount filesystems temporary
- tmp, for temporary data
- media, for external media like usb. ```df -hP``` for all mounted filesystems
- dev, for devices
- bin, basic programs and binary
- etc, for config
- lib, for shared library to be imported
- usr, user-specific programs
- var, for logs
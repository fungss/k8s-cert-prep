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
<here>

## Systemd Targets (Run Levels)
## Filesystems and Hierarchy
mnt
var
usr
dev
bin
root
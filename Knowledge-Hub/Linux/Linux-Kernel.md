---
layout: default
title: Linux Kernel
description: Learn about the heart of the operating system.
---

## 🏷️ Table of Contents 

1. [Linux Kernel explained](#linux-kernel-explained) <br>
2. [Kernel mode vs User mode](#kernel-mode-vs-user-mode) <br>
3. [Kernel Ring Buffer](#kernel-ring-buffer) <br>
4. [Kernel vs BIOS](#kernel-vs-bios) <br>
5. [Boot sequence](#boot-sequence) <br>
6. [GRUB](#grub) <br>
7. [Recovery mode](#recovery-mode) <br>
8. [GRUB2](#grub2) <br>
9. [Kernel vs Shell](#kernel-vs-shell) <br>
10. [Kernel Panic](#kernel-panic) <br>
11. [initramfs](#initramfs) <br>
11. [Init Process](#init-process) <br>

&nbsp;

# Linux Kernel explained

The Linux kernel is the heart of the operating system. It acts as the bridge between your computer's hardware and the programs you use every day.

The kernel is responsible for managing CPU usage, allocating memory (RAM), communicating with storage and network devices and allowing applications to safely use hardware.

We can divide kernel into three layers:
1. **Physical Hardware** - base - RAM (memory), CPU, I/O (storage, network)
2. **Kernel** - controls and manages hardware resources
3. **Processes** - running programs/applications (IPC - inter-process communication)

&nbsp;

# Kernel mode vs User mode

Programs run in one of two modes:
1. **Kernel mode** - full access to hardware; can use CPU and memory directly; it's used by the operating system

2. **User mode** - has limited permissions; must communicate through system calls; it's used by normal applications

A crash in user mode usually affects only one application. A crash in kernel mode can bring down the entire operating system.


# Kernel Ring Buffer

The kernel ring buffer is **a temporary memory area** where Linux stores kernel messages.

It contains information such as hardware detection, driver messages, boot logs or system warnings and errors. 

You can read it with command `dmesg`
To clear the buffer (as root): `sudo dmesg --clear`

**The ring buffer exists only in memory, so its contents disappear after the computer is shut down or restarted.**

&nbsp;

# Kernel vs BIOS

Both BIOS and the kernel are involved in starting your computer, but they have completely different jobs.


|BIOS / UEFI|Linux Kernel|
|-----------|--------------|
|Starts first|Starts after GRUB|
|Checks hardware (POST)|Controls CPU, RAM and devices|
|Finds a bootloader|Runs while the computer is on|
Stored on the motherboard|Part of the OS|

&nbsp;

# Boot sequence

1. BIOS/UEFI powers on the hardware.
2. GRUB loads the Linux kernel.
3. Kernel initializes the system.
4. Init (systemd) starts all services.

&nbsp;

# GRUB

GRUB stands for **Grand Unified Bootloader**.

Its job is to load the operating system. 
Without GRUB, the kernel would never start.
Basically GRUB starts after BIOS/UEFI, then loads itself into memory, loads the Linux kernel and then hands control to the kernel. After that, GRUB's job is finished.

RUB can store multiple kernels. If you installed a new kernel and something goes wrong, you can boot an older working version.

&nbsp;

# Recovery mode

Recovery mode is extremely useful for troubleshooting.

For example, if your system gets stuck in a boot loop, you can boot into recovery and repair the installation.

common temporary boot parameter: `systemd.unit=rescue.target`
for emergency mode: `systemd.unit=emergency.target`

To edit boot parameters:
Highlight your Linux entry > press `E` > add the parameter at the end of the linux line > press `Ctrl + X` to boot

Note: Modern systems use the linux line, not linux16, unless you're on older RHEL-based systems.

&nbsp;

# GRUB2

GRUB2 is the modern version of GRUB and is used by nearly all current Linux distributions.


|GRUB|GRUB2|
|--------------|--------------|
|Older bootloader|Modern bootloader|
|Limited FS support|Supports LVM, RAID, GPT, ...|
|Multiple config files|One generated grub.cfg file|
|Less flexible|More powerful and modular|

The main config file: `/boot/grub2/grub.cfg` <br>
You normally do not edit it manually. Instead, regenerate it with: `sudo grub2-mkconfig` <br>
On Debian and Ubuntu, the equivalent command is: `sudo update-grub`

&nbsp;

# Kernel vs Shell

People often confuse these two, but they do completely different jobs.

|Kernel|Shell|
|--------------------|--------------------|
|The core of the operating system.|A command-line interpreter.|
|It communicates directly with hardware and manages system resources.|It allows users to communicate with the kernel by typing commands.|

The shell reads your command, and the kernel actually creates the directory on the disk.

&nbsp;

# Kernel Panic

A kernel panic is Linux's equivalent of the Windows Blue Screen of Death (BSOD).

It happens when the kernel encounters a fatal error and cannot safely continue running.

Common causes: Corrupted kernel files, faulty drivers, hardware failures, filesystem corruption

&nbsp;

# initramfs

initramfs (Initial RAM Filesystem) is a small temporary filesystem loaded into RAM during boot.

Its only job is to prepare the system by loading essential drivers, detecting storage devices and mounting the real root filesystem.

Once the real filesystem is mounted, initramfs hands control over and disappears.

If this process fails, the system may end with a kernel panic.

&nbsp;

# Init Process

The init process is the first userspace process started by the kernel.
Init = initialization.

It always has `PID = 1` 

PID stands for **Process ID**.

Its responsibilities: 
- Starting system services
- Launching networking
- Starting the login screen
- Managing system shutdown and reboot

On most modern Linux systems, the init system is **systemd**.

You can check it with: 'ps -p 1'

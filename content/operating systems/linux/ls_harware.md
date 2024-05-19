---
title: "ls hardware"
tags:
- linux
- hardware
- ls
---

Linux commands to display your hardware information.
<!--more-->
Get the details on what's inside your computer from the command line.

## Overview (`lshw`)

[lshw](https://linux.die.net/man/1/lshw) is a small tool to extract detailed information on the hardware configuration of the machine. 
It can report exact memory configuration, firmware version, mainboard configuration, CPU version and speed, cache configuration, 
bus speed, etc. on DMI-capable x86 or IA-64 systems and on some PowerPC machines (PowerMac G4 is known to work).

```shell
$ sudo lshw
optiplex-7040
    description: Desktop Computer
    product: OptiPlex 7040 (06B9)
    vendor: Dell Inc.
...
```
or for a specific piece of hardware:
```shell
$ sudo lshw -class disc
  *-disk
       description: ATA Disk
       product: SanDisk X300 2.5
       physical id: 0.0.0
       bus info: scsi@0:0.0.0
       logical name: /dev/sda
  ...
```

## CPU Details (`lscpu`)

[lscpu](https://linux.die.net/man/1/lscpu) gathers CPU architecture information from sysfs and /proc/cpuinfo. 
The command output can be optimized for parsing or for easy readability by humans.

```shell
$ lscpu
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         39 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  4
  On-line CPU(s) list:   0-3
Vendor ID:               GenuineIntel
  Model name:            Intel(R) Core(TM) i5-6500T CPU @ 2.50GHz
    CPU family:          6
    Model:               94
    Thread(s) per core:  1
    Core(s) per socket:  4
    Socket(s):           1
    Stepping:            3
    CPU max MHz:         3100.0000
    CPU min MHz:         800.0000
```

## PCI Information (`lspci`)

```shell
$ lspci
00:00.0 Host bridge: Intel Corporation Haswell-ULT DRAM Controller (rev 0b)
00:02.0 VGA compatible controller: Intel Corporation Haswell-ULT Integrated Graphics Controller (rev 0b)
00:03.0 Audio device: Intel Corporation Haswell-ULT HD Audio Controller (rev 0b)
00:14.0 USB controller: Intel Corporation 8 Series USB xHCI HC (rev 04)
00:16.0 Communication controller: Intel Corporation 8 Series HECI #0 (rev 04)
00:1b.0 Audio device: Intel Corporation 8 Series HD Audio Controller (rev 04)
00:1c.0 PCI bridge: Intel Corporation 8 Series PCI Express Root Port 1 (rev e4)
00:1c.2 PCI bridge: Intel Corporation 8 Series PCI Express Root Port 3 (rev e4)
00:1c.3 PCI bridge: Intel Corporation 8 Series PCI Express Root Port 4 (rev e4)
00:1c.4 PCI bridge: Intel Corporation 8 Series PCI Express Root Port 5 (rev e4)
00:1d.0 USB controller: Intel Corporation 8 Series USB EHCI #1 (rev 04)
00:1f.0 ISA bridge: Intel Corporation 8 Series LPC Controller (rev 04)
00:1f.2 SATA controller: Intel Corporation 8 Series SATA Controller 1 [AHCI mode] (rev 04)
00:1f.3 SMBus: Intel Corporation 8 Series SMBus Controller (rev 04)
02:00.0 Network controller: Intel Corporation Wireless 7260 (rev 73)
03:00.0 Unassigned class [ff00]: Realtek Semiconductor Co., Ltd. RTL8411B PCI Express Card Reader (rev 01)
03:00.1 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 12)
04:00.0 3D controller: NVIDIA Corporation GK107M [GeForce GT 750M] (rev a1)
```

## Graphics card/GPU information

You can use the command `hwinfo --gfxcard --short` or `sudo lshw -C display` to display information about the graphics
card(s) in your system. For example:

```shell
$ hwinfo --gfxcard --short

graphics card:
                       nVidia GK107M [GeForce GT 750M]
                       Intel Haswell-ULT Integrated Graphics Controller

Primary display adapter: #21
```
OR
```shell
$ sudo lshw -C display
  *-display
       description: VGA compatible controller
       product: Haswell-ULT Integrated Graphics Controller
       vendor: Intel Corporation
       physical id: 2
       bus info: pci@0000:00:02.0
       logical name: /dev/fb0
       version: 0b
       width: 64 bits
       clock: 33MHz
       capabilities: msi pm vga_controller bus_master cap_list rom fb
       configuration: depth=32 driver=i915 latency=0 resolution=1920,1080
       resources: irq:48 memory:e3000000-e33fffff memory:c0000000-cfffffff ioport:5000(size=64) memory:c0000-dffff
  *-display UNCLAIMED
       description: 3D controller
       product: GK107M [GeForce GT 750M]
       vendor: NVIDIA Corporation
       physical id: 0
       bus info: pci@0000:04:00.0
       version: a1
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress bus_master cap_list
       configuration: latency=0
       resources: memory:e2000000-e2ffffff memory:d0000000-dfffffff memory:e0000000-e1ffffff ioport:3000(size=128)
```

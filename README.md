DiscoBSD
========

2.11BSD-based UNIX-like Operating System for STM32 and PIC32 Microcontrollers
-----------------------------------------------------------------------------

DiscoBSD is a 2.11BSD-based UNIX-like operating system for microcontrollers,
with a focus on high portability to memory constrained devices without a
memory management unit.

This microcontroller-focused operating system is the continuation of RetroBSD,
a 2.11BSD-based OS targeting only the MIPS-based PIC32MX7.
DiscoBSD is multi-platform, as it also supports Arm Cortex-M4 STM32F4 devices.

Source code to the system is freely available under a BSD-like license.

History
-------

[DiscoBSD][1] began as an undergraduate [Directed Study][2] in the winter of
2020 at the University of Victoria, Canada, as a case study of [RetroBSD][3]
to port the operating system to the Arm Cortex-M4 architecture, and to enable
portabilty in the hosting environment and target architectures and platforms.
The paper [*Porting the Unix Kernel*][4] details this initial porting effort.

Work on DiscoBSD has progressed in earnest since the completion of the
Directed Study, with the DiscoBSD/stm32 port booting multi-user in
August 2022. The system is quite usable on supported development boards.

And work continues...

[1]: https://DiscoBSD.org
[2]: https://github.com/chettrick/CSC490
[3]: https://RetroBSD.org
[4]: https://github.com/chettrick/CSC490/raw/master/project_outputs/Porting_the_Unix_Kernel-CSC490-Christopher_Hettrick.pdf

DiscoBSD Resource Requirements
------------------------------

A basic, minimal system uses 128 Kbytes of flash and 128 Kbytes of RAM.

The kernel is loaded into the flash and only uses 32 Kbytes of RAM.
User programs each use the remaining 96 Kbytes of RAM, via swapping.
Devices with more RAM can be used to run larger user programs.

An SD card, at least 256 Mbytes in size, is required for the root file system.

Installing and Running
----------------------

Installation consists of loading the kernel into the microcontroller's flash
memory, and imaging the SD card with the file `sdcard.img`.

The `make` target `installfs` uses the `dd` utility to image the SD card
attached to the host operating system at `SDCARD`, such as `/dev/rsdXc` or
`/dev/sdX` or `/dev/rdiskX`, replacing `X` with the actual drive number or
letter, as the case may be.

For example, imaging an SD card attached at `sd2` on an OpenBSD host
operating system through the raw i/o device:

    $ SDCARD=/dev/rsd2c gmake installfs

Communication with the DiscoBSD console requires a serial port. A USB to TTL
device or the built-in VCP USB serial port on development boards can be used.

    $ cu -l /dev/cuaU0 -s 115200

Depending on the host system, other serial port utilities such as `screen`,
`minicom`, `putty`, or `teraterm` may be used.

Log in to DiscoBSD with user `root` and a blank password.
Shutdown DiscoBSD with the `halt`, `shutdown`, or `reboot` commands.

Manual pages for commands are available through the `man` command.

Building
--------

DiscoBSD is cross-built on UNIX-like host operating systems.

From the source tree root, run:

    $ gmake

which will build a file system image in the file `distrib/stm32/sdcard.img`
and kernels in the files `sys/stm32/${BOARD}/unix.elf`.

DiscoBSD/stm32 is the default port, but DiscoBSD/pic32 may be built via:

    $ MACHINE=pic32 MACHINE_ARCH=mips gmake

which will build a file system image in the file `distrib/pic32/sdcard.img`
and kernels in the files `sys/pic32/${BOARD}/unix.hex`.

Debugging
---------

DiscoBSD/stm32 is debugged through OpenOCD and GDB. The `make` targets for
debugging are `ocd` and `gdb-ocd`.

Debug a particular development board via:

    $ BOARD=f412gdisco gmake ocd

in one terminal, and:

    $ BOARD=f412gdisco gmake gdb-ocd

in another terminal.

Additional Information
----------------------

Port-specific information can be found in `distrib/${MACHINE}/README.md`
for [stm32][5] and [pic32][6].

[5]: distrib/stm32/README.md
[6]: distrib/pic32/README.md

DiscoBSD/stm32 dmesg
--------------------

```
2.11 BSD UNIX for STM32, rev G279 #1: Sat Dec 31 18:17:10 PST 2022
     chris@stm32.discobsd.org:/sys/stm32/f412gdisco
cpu: STM32F412xx rev C, 100 MHz, bus 50 MHz
oscillator: phase-locked loop, clock source: high speed external
uart2: pins tx=PA2/rx=PA3, af=7, console
sd0: port sdio0
sd0: type SDHC, size 31178752 kbytes
sd0a: partition type b7, sector 2, size 102400 kbytes
sd0b: partition type b8, sector 204802, size 2048 kbytes
sd0c: partition type b7, sector 208898, size 102400 kbytes
phys mem  = 256 kbytes
user mem  = 96 kbytes
root dev  = (0,1)
swap dev  = (0,2)
root size = 102400 kbytes
swap size = 2048 kbytes
Automatic boot in progress: starting file system checks.
/dev/sd0a: 1448 files, 11897 used, 90102 free
/dev/sd0c: 3 files, 3 used, 101996 free
Updating motd... done
Starting daemons: update cron 
Sat Dec 31 18:23:07 PST 2022


2.11 BSD UNIX (name.my.domain) (console)

login: root
Password:
2.11 BSD UNIX for STM32, rev G279 #1: Sat Dec 31 18:17:10 PST 2022

Welcome to DiscoBSD.

erase ^?, kill ^U, intr ^C
# 
```

DiscoBSD/pic32 dmesg
--------------------

```
2.11 BSD Unix for PIC32, revision G279 build 1:
     Compiled 2022-12-31 by chris@pic32.discobsd.org:
     /discobsd/sys/pic32/max32
cpu: 795F512L 80 MHz, bus 80 MHz
oscillator: HS crystal, PLL div 1:2 mult x20
spi2: pins sdi=RG7/sdo=RG8/sck=RG6
uart1: pins rx=RF2/tx=RF8, interrupts 26/27/28, console
uart2: pins rx=RF4/tx=RF5, interrupts 40/41/42
uart4: pins rx=RD14/tx=RD15, interrupts 67/68/69
sd0: port spi2, pin cs=RC14
gpio0: portA, pins ii---ii-iiiioiii
gpio1: portB, pins iiiiiiiiiiiiiiii
gpio2: portC, pins i-ii-------iiii-
gpio3: portD, pins --iiiiiiiiiiiiii
gpio4: portE, pins ------iiiiiiiiii
gpio5: portF, pins --ii--------i-ii
gpio6: portG, pins iiii--i-----iiii
adc: 15 channels
pwm: 5 channels
sd0: type I, size 348160 kbytes, speed 10 Mbit/sec
sd0a: partition type b7, sector 2, size 102400 kbytes
sd0b: partition type b8, sector 204802, size 2048 kbytes
sd0c: partition type b7, sector 208898, size 102400 kbytes
phys mem  = 128 kbytes
user mem  = 96 kbytes
root dev  = (0,1)
swap dev  = (0,2)
root size = 102400 kbytes
swap size = 2048 kbytes
Automatic boot in progress: starting file system checks.
/dev/sd0a: 1462 files, 12370 used, 89629 free
/dev/sd0c: 3 files, 3 used, 101996 free
Updating motd... done
Starting daemons: update cron 
Sat Dec 31 18:36:57 PST 2022


2.11 BSD UNIX (name.my.domain) (console)

login: root
Password:
2.11 BSD Unix for PIC32, revision G279 build 1:

Welcome to RetroBSD!

erase ^?, kill ^U, intr ^C
# 
```

# How does a Linux machine start?

A typical bootstrapping process consists of six distinct phases:

1. Reading of the boot loader from the master boot record (MBR)

    BIOS code which is located on ROM chip on motherboard starts first and does the power-on self test (POST) to check the machine and then if all is fine and then it identifies where it shall boot the system from (flashcard, hard drive etc).
    When the boot drive is confirmed it reads the first block of the device (512-byte segment) known as MBR.
    The MBR contains code that tell the computer from which partition to load boot loader (e.g. GRUB(old), GRUB-2, LILO(LInux LOader - old)). This boot loader then loads kernel of the OS.
    Note that any drive has an MBR, even a floppy contains code in its MBR, even if the code is only enough to put out a message such as "Non-bootable disk in drive A:"

2. Loading and initialization of the kernel:

    Kernel is a program. It runs and first thing it does it checks RAM and reserves some space for itself, this bit of memory will not be available for user now. Then kernel finds init in sbin and executes it, so init - is the first process to run after the system boots. init starts configuring the system for you and sets the default run level. init can run at 7 different levels (0-6).
    A run level is a configuration of processes (Normally 1 - single user mode, 2/3 - multiuser mode, 4 - unused, 6 - reboot).
    As the machine boots, init goes its way up from run level 0 to the default run (lvl 2 or 3) level, which is set in /etc/inittab. In /etc/inittab it is also written what to do at each run level. Then it switched to devices detection/configuration

3. Hardware configuration

    Kernel checks what hardware is present. And loads devices' drivers as independent kernel modules

4. Creation of kernel processes

5. Administrator intervention (single-user mode only) - OPTIONAL

    Often only the root partition is mounted so you must mount other filesystems by hand to use programs that don’t live in /bin, /sbin, or /etc. Also often root directory starts as read-mode only, so you will need to remount in r/w.

6. Execution of system startup scripts

    The startup scripts are just normal shell scripts. The master copies of the startup scripts live in the /etc/init.d directory

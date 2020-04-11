# Linux hard drive partitions

Linux machine normally has the following hard drive partitions:

1. Data partition - normal Linux system data, including the root partition containing all the data to start up and run the system
2. Swap partition - expansion of the computer's physical memory, extra memory on hard disk. Only accessible for the system itself. Computer might use swap area if RAM is overloaded but as hard drive is much slower than RAM it is almost useless
3. Extended partition - special type of partition that can hold multiple logical partitions (solves the drive limitation issue). Extended partition was introduced as traditional partitioning allows to have maximum of four primary partitions, because the original method was developed in the early 1980's when 10-20 MB was considered a "very large" disk and by the time they realized how foolish the original scheme was, they were locked into it.

At some point in the future, disks will no longer use MBR partitioning. Instead they will use a GUID Partition Table (GPT).

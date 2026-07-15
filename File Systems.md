# File Systems

## Logical Structure
- Sector --> smallest addressable unit of data storage within a disk, which can store 512 bytes of data, data can be read and written in the form of sectors within a disk
- Cluster --> known as a block, formed by successive sector groups, used to allocate the smallest unit for the supported file system
- File allocation table (FAT) --> known as an inode table, a data structure based on the file system that is used to manage and track the file allocations, locations, and clusters within a disk
- Master boot record (MBR) --> partition tables that are used to create or manage partitions on a disk
- GUID partition table (GPT) --> partition tables that are used to create or manage partitions on a disk
- Logical block addressing (LBA) --> used to address the disk sectors sequentially and linearly, replacing the traditional cylinder–head–sector (CHS) addressing scheme
- Slack space --> wasted area of a disk cluster lying between the end of a file and the end of the cluster
- BIOS Parameter Block (BPB) --> data structure situated at sector 1 in the volume boot record (VBR) of a hard disk and explains the physical layout of a disk volume

## Change the cluster size of a hard disk
- `diskpart`
- `list disk`
- `select disk #` --> Write the number of the disk replacing the hashtag
- `list partition`
- `select partition #` --> Write the number of the disk replacing the hashtag
- `format fs=ntfs unit=<ClusterSize>` --> for example, unit=64k

## Lost Clusters
- `chkdsk` --> detect errors in file system

## Master Boot Record (MBR)
- is the first sector ("sector zero") of a data storage device
- MBR almost always refers to the 512-byte boot sector (or partition sector) of a disk
- Holding a partition table --> 64-byte data structure that stores information about the types of partitions present on the hard disk and their locations
- Bootstrapping an OS --> Master Boot Code --> executes to initiate the system’s boot process
- Distinctively recognizing individual hard disk media with a 32-bit disk signature
- `dd if=/dev/xxx of=mbr.backup bs=512 count=1` --> Backing up MBR --> /dev/xxx = /dev/sda
- `dd if=mbr.backup of=/dev/xxx bs=512 count=1` --> Restoring MBR

## GUID Partition Table (GPT)
- standard partitioning scheme for HDDs and SSDs and part of the unified extensible firmware interface (UEFI)
- Protective MBR occupies the first position of the GPT at LBA 0
- LBA 1 contains the GPT header, and the GPT header comprises a pointer to the partition table or partition entry array at LBA 2
- LBA 34 is the first usable sector
- `Get-ForensicMasterBootRecord -Path \\.\PHYSICALDRIVE0 | select -ExpandProperty PartitionTable` --> displays the MBR partition table of a GPT-formatted disk

## Booting Process
- Power on self-test (POST) --> ensures that all the hardware components are working fine
- Warm booting --> it does not perform POST

## Windows Boot Process: BIOS-MBR Method
1. When the user switches the system ON, the CPU sends a Power Good signal to the motherboard and checks for the computer’s BIOS firmware
2. BIOS starts POST, which checks if all the hardware required for system boot is available and loads all the firmware settings from non-volatile memory onto the motherboard
3. If POST is successful, add-on adapters perform a self-test for integration with the system
4. The pre-boot process is completed with POST, detecting a valid system boot disk
5. After POST, the computer’s firmware scans the boot disk and loads the MBR, which searches for basic boot information in boot configuration data (BCD)
6. MBR triggers Bootmgr.exe, which locates the Windows loader (Winload.exe) on the Windows boot partition and triggers Winload.exe
7. The Windows loader loads the OS kernel ntoskrnl.exe
8. Once the Kernel starts running, the Windows loader loads hal.dll, boot-class device drivers marked as BOOT_START, and the SYSTEM registry hive into memory
9. The kernel passes control of the boot process to the Session Manager Process (SMSS.exe), which loads all other registry hives and drivers required to configure the Win32 subsystem run environment
10. The Session Manager Process triggers Winlogon.exe, which presents the user login screen for user authorization
11. The Session Manager Process initiates the Service Control Manager, which starts all the services, the rest of the non-essential device drivers, the security subsystem LSASS.EXE, and Group Policy scripts
12. Once the user logs in, Windows creates a session for the user
13. The Service Control Manager starts explorer.exe and initiates the Desktop Window Manager (DMW) process, which initializes the desktop for the user

- Identifying the MBR Partition --> Computer Management --> Storage --> Disk Management --> Select Partition Properties --> Volumes --> Partition Style

## Windows Boot Process: UEFI-GPT
1. Security phase --> SEC phase of EFI consists of an initialization code that the system executes after powering on the EFI system. It manages platform reset events and sets the system so that it can find, validate, install, and run the pre-EFI initialization (PEI)
2. Pre-EFI initialization phase --> The PEI phase initializes the CPU, main memory, and boot firmware volume (BFV). It locates and executes the pre-initialization modules (PEIMs) present in the BFV so as to initialize all the hardware found in the system. Finally, it creates a handoff block list (HOBL) with all the found resources and interface descriptors and passes it to the next phase, i.e., the DXE phase
3. Driver execution environment phase --> Most of the initialization occurs in this phase. By using the HOBL, the driver execution environment (DXE) initializes the entire physical memory of the system, I/O, and memory-mapped I/O (MIMO) resources and finally begins dispatching DXE drivers present in the system firmware volumes (given in the HOBL). The DXE core produces a set of EFI boot services and EFI runtime services. The EFI boot services allocate memory and load executable images. The EFI runtime services convert memory addresses from physical to virtual, hand them over to the kernel, and reset the CPU for code running within the EFI environment or the OS kernel, once the CPU takes control of the system
4. Boot device selection phase --> the boot device selection (BDS) interprets the boot configuration data and selects the boot policy for later implementation. This phase works with the DXE to check if the device drivers require signature verification. In this phase, the system loads the MBR boot code into memory for a legacy BIOS boot or loads the bootloader program from the EFI partition for a UEFI boot. It also provides an option for the user to choose the EFI shell or a UEFI application as the boot device from the setup
5. Runtime phase --> At this point, the system clears the UEFI program from memory and transfers it to the OS. During the UEFI BIOS update, the OS calls the runtime service using a small part of the memory

## Windows File Systems
- File Allocation Table (FAT)
- FAT12
- FAT16
- FAT32
- extended file allocation table (exFAT)
- Resilient File System (ReFS)

`Get-ForensicGuidPartitionTable -Path \\.\PHYSICALDRIVE0` --> get GUID Partition Table

`Get-MBR`

`Get-ForensicBootSector` --> analyzes the hard drive's first sector and determines if the disk is formatted using the MBR or GPT partitioning scheme then parses the GPT

`Get-ForensicPartitionTable` --> determines the type of boot sector (MBR or GPT) and returns the correct partition object (PartitionEntry or GuidPartitionTableEntry)

### diskpart
```
diskpart
select disk <disk number>
detail disk
select partition=1
detail partition
```

## New Technology File System (NTFS)
### Alternate Data Streams (ADS)
`ECHO [data] > [filename]:[streamname]` --> write contents into a file’s data stream

`MORE < [filename]:[streamname]` --> displays the content of the data stream

`fsutil` --> check USN Journal

`notepad test.txt:hidden.txt`

`dir /r`

## Linux File Systems
Filesystem Hierarchy Standard (FHS)

- Second Extended File System (ext2)
- Third Extended File System (ext3)
- Fourth Extended File System (ext4)

`fsck`

`/sbin/tune2fs -j /dev/hda5` --> convert an ext2 file system located on the partition /dev/hda5 to an ext3 file system

`dumpe2fs <path-to-partition> | grep –i superblock` --> view superblock information of a file system

`ls -il` --> view the assigned inode numbers of files or directories

## macOS File Systems
- Hierarchical File System Plus (HFS+)
- Apple File System (APFS)

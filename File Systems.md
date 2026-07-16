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

### Windows Boot Process: BIOS-MBR Method
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
- `Get-MBR -Path \\.\PHYSICALDRIVE0`

### Windows Boot Process: UEFI-GPT
1. Security phase
  - SEC phase of EFI consists of an initialization code that the system executes after powering on the EFI system
  - It manages platform reset events and sets the system so that it can find, validate, install, and run the pre-EFI initialization (PEI)
2. Pre-EFI initialization phase
  - The PEI phase initializes the CPU, main memory, and boot firmware volume (BFV)
  - It locates and executes the pre-initialization modules (PEIMs) present in the BFV so as to initialize all the hardware found in the system
  - Finally, it creates a handoff block list (HOBL) with all the found resources and interface descriptors and passes it to the next phase
3. Driver execution environment phase
  - Most of the initialization occurs in this phase
  - By using the HOBL, the driver execution environment (DXE) initializes the entire physical memory of the system, I/O, and memory-mapped I/O (MIMO) resources
  - and finally begins dispatching DXE drivers present in the system firmware volumes (given in the HOBL)
  - The DXE core produces a set of EFI boot services and EFI runtime services
  - The EFI boot services allocate memory and load executable images
  - The EFI runtime services convert memory addresses from physical to virtual, hand them over to the kernel
  - and reset the CPU for code running within the EFI environment or the OS kernel, once the CPU takes control of the system
4. Boot device selection phase
  - the boot device selection (BDS) interprets the boot configuration data and selects the boot policy for later implementation
  - This phase works with the DXE to check if the device drivers require signature verification
  - In this phase, the system loads the MBR boot code into memory for a legacy BIOS boot or loads the bootloader program from the EFI partition for a UEFI boot
  - It also provides an option for the user to choose the EFI shell or a UEFI application as the boot device from the setup
5. Runtime phase
  - At this point, the system clears the UEFI program from memory and transfers it to the OS. During the UEFI BIOS update, the OS calls the runtime service using a small part of the memory

- `Get-ForensicGuidPartitionTable -Path \\.\PHYSICALDRIVE0` --> get GUID Partition Table
- `Get-ForensicBootSector` --> analyzes the hard drive's first sector and determines if the disk is formatted using the MBR or GPT partitioning scheme then parses the GPT
- `Get-ForensicBootSector -Path \\.\PHYSICALDRIVE0 | select *` --> run against a disk formatted using the MBR partitioning scheme
- `Get-ForensicPartitionTable` --> determines the type of boot sector (MBR or GPT) and returns the correct partition object (PartitionEntry or GuidPartitionTableEntry)

### Linux Boot Process
1. BIOS stage
  - It initializes the system hardware during the booting process
  - The BIOS retrieves the information stored in the complementary metal–oxide semiconductor (CMOS) chip, which is a battery-operated memory chip on the motherboard that contains information about the system’s hardware configuration
  - During the boot process, the BIOS performs a POST to ensure that all the hardware components of the system are operational
  - After a successful POST, the BIOS starts searching for the drive or disk that contains the OS in a standard sequence
  - If the first listed device is not available or not working, then it checks for the next one, and so on
  - A drive is bootable only if it has the MBR in its first sector known as the boot sector
  - The system’s hard disk acts as the primary boot disk, and the optical drive works as the secondary boot disk for booting the OS in case the primary boot disk fails
2. Bootloader stage
  - The bootloader stage includes the task of loading the Linux kernel and optional initial RAM disk
  - The kernel enables the CPU to access RAM and the disk
  - The second pre-cursor software is an image of a temporary virtual file system called the initrd image or initial RAMdisk
  - Now, the system prepares to deploy the actual root file system
  - It then detects the device that contains the file system and loads the necessary modules
  - The last step of the bootloader stage is to load the kernel into memory
3. Kernel Stage
  - Once the control shifts from the bootloader stage to the kernel stage, the virtual root file system created by the initrd image executes the Linuxrc program
  - This program generates the real file system for the kernel and later removes the initrd image
  - The kernel then searches for new hardware and loads any suitable device drivers found
  - Subsequently, it mounts the actual root file system and performs the init process
  - The init reads the file “/etc/inittab” and uses this file to load the rest of the system daemons
  - This prepares the system, and the user can log in and start using it
  - Typical bootloaders for Linux are Linux Loader (LILO) and Grand Unified Bootloader (GRUB)
  - These bootloaders allow the user to select which OS kernel to load during boot time

## Analyzing the GPT Header

### Windows - DiskPart
```
diskpart
select disk <disk number>
detail disk
select partition=1
detail partition
```

## Windows File Systems
- File Allocation Table (FAT)
- FAT12
- FAT16
- FAT32
- extended file allocation table (exFAT)
- Resilient File System (ReFS)

### New Technology File System (NTFS)
- Ntldlr.dll --> boot loader, it accesses the NTFS filesystem and loads the contents of the boot.ini file
- Ntfs.sys --> computer system file driver for NTFS

#### NTFS Master File Table (MFT)
- Each file on an NTFS volume is represented by a record in a special file called the master file table (MFT)
- The MFT size increases with an increase in the number of files added to the NTFS volume
- The first record of this table describes the MFT itself, followed by an MFT mirror file, a log file, and system files
- When the user deletes a file from an NTFS volume, the file system marks the values in the MFT as free and makes that location reusable
- NTFS saves space for the MFT to maintain it as compactly as possible as it expands
- For the MFT in each volume, NTFS reserves some space called the MFT zone

#### Alternate Data Streams (ADS)
- `ECHO [data] > [filename]:[streamname]` --> write contents into a file’s data stream
- `MORE < [filename]:[streamname]` --> displays the content of the data stream
- `notepad test.txt:hidden.txt`
- `dir /r`

#### NTFS Journals
- changes are written into a data structure before they are implemented in the file system
- The journaling feature in NTFS enables faster recovery without any data loss in case of a system crash or an unexpected shutdown
- It has a system management feature such as USN (Update Sequence Number Journal) to record the changes to the files and directories in the volume in `$Extend\$UsnJrnl`
- The `$UsnJrnl` consists of two data streams:
  - `$UsnJrnl/$J` --> records the actual journal entries of file and folder changes that occurred on the volume
  - `$UsnJrnl/$MAX` --> records the metadata of $UsnJrnl
- If the size of the USN Journal/Change file exceeds the predefined limit, the journal rotates, and the previous data will be overwritten
- `fsutil` --> check USN Journal
- NTFS Journal Viewer
- FTK Imager

## Linux File Systems
- Filesystem Hierarchy Standard (FHS)

- Second Extended File System (ext2)
  - `/sbin/tune2fs -j <partition-name> ` --> to convert ext2 to ext3
  - `/sbin/tune2fs -j /dev/hda5` --> convert an ext2 file system located on the partition /dev/hda5 to an ext3 file system
- Third Extended File System (ext3)
- Fourth Extended File System (ext4)

- `fsck` --> file system maintenance utilities

### Superblocks, Inodes, and Data Blocks
- Superblocks --> stores information regarding the characteristics of a file system such as, the type and size of file, empty or filled file system blocks, locations of inode tables and their sizes, block size of the file system, etc
- `dumpe2fs <path-to-partition> | grep –i superblock` --> view superblock information of a file system
- Inodes --> stores metadata pertaining to a file or directory on the Linux filesystem and it is identified by a unique inode number or index number
- `ls -il` --> view the assigned inode numbers of files or directories
- Data Blocks --> stores the actual contents of a file
- a data block can also store the contents of an entire directory

## macOS File Systems
- Hierarchical File System Plus (HFS+)
- Apple File System (APFS)

## MACB Timestamps
- Modified
- Accessed
- Changed ($MFT modified)
- Birth (file creation time)

## Host-Protected Areas (HPA)
- reserved area on an HDD or SSD that is not accessible to the OS
- stores data that users, BIOS, or the OS of a system cannot modify, change, or access
- store information about HDD utilities, diagnostic tools, system restore, encryption keys, boot sector code, etc
- three ATA commands that can help users create and use an HPA
  - IDENTIFY DEVICE
  - SET MAX ADDRESS
  - READ NATIVE MAX ADDRESS

## Device Configuration Overlays (DCO)
- additional hidden area available on modern hard disks
- enables system vendors to buy HDDs of varying sizes from different manufactures and configure all of them to have an equal number of sectors
- help users enable/disable features on an HDD
- DEVICE_CONFIGURATION_IDENTIFY --> command to determine the actual size and features of a disk
- EnCase
- TAFT

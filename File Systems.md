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

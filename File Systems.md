# File Systems

## Windows File Systems
- File Allocation Table (FAT)
- FAT12
- FAT16
- FAT32
- extended file allocation table (exFAT)
- Resilient File System (ReFS)

`chkdsk` --> detect errors in file system

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

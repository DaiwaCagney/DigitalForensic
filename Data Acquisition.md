# Data Acquisition

## Windows

### Volatile Data
- Belkasoft Live RAM Capturer

```
wmic diskdrive list brief /format:list #DeviceID=\\.\PHYSICALDRIVE0
net use Z: "\\server2022\chfi-tools"
.\dd.exe if=\\.\physicaldrive0 of=z:\evidence\Windows_001.dd bs=512k --size --progress
Get-Filehash 'z:\evidence\Windows_001.dd' -Algorithm md5 | format-list
```

### Non-Volatile Data
- FTK® Imager

### Identifying RAID Drives in Windows System
- Device Manager
- Expand the Disk drives, Storage controllers, and Other devices options, and check for listed RAID device drivers
- `Diskpart` --> open the command line disk partitioning utility
- `lis dis` --> list all disks connected to the Windows system
- `sel dis <disk number>` --> select the disk for operating
- `det dis` --> display details of the selected disk

### Examining Images on Windows Forensic Workstation
- Autopsy
- FTK Imager
- Volatility

## Linux

### Acquire RAM
- `dd if=/dev/fmem of=<file_name.dd> bs=1MB` --> In older versions of Linux, RAM contents were captured from the /dev/mem device
- `dd if=/dev/fmem of=/home/james/ubuntu_local_ram.dd bs=1MB` --> To acquire RAM locally
- `insmod lime-6.2.0-35-generic.ko "path=../../ubuntu_local_ram.mem format=lime"` --> To acquire RAM locally
- The kernel module version varies depending on the Ubuntu OS version installed on the suspect machine. In this case, it is 6.2.0-35-generic

### Remote acquisition of RAM using dd and netcat
```
nc -l <port> > filename.dd
dd if=/dev/fmem bs=1024 | nc <IP Address of the Suspect Machine> <port>
nc -l 1234 > ubuntu_remote_ram.dd
dd if=/dev/fmem bs=1024 | nc 10.10.1.9 1234
```

### dd
```
dd if=/dev/sda of=./diskimage.img # Create an image of a disk
dd if=/dev/sdb of=forensic.img
dd if=<source> of=<target> bs=<byte size> skip= seek= conv =<conversion>
skip: number of blocks to skip at start of input
seek: number of blocks to skip at start of output
conv: conversion options
byte size: used to set the block size for input and output in bytes (default is 512, "USUALLY" some power of 2, not less than 512 bytes i.e, 512, 1024, 2048, 4096, 8192, 16384, but can be ANY reasonable number)

dir /dev
md5sum /dev/sdb
dd if=/dev/sdb of=/dev/sdc
dd if=/dev/sda of=/image_sda.dd
dd if=/dev/hda of=/dev/case5img1 --> Create a completephysical backup of thehard disk
dd if=/dev/sda2 of=/dev/sdb2 bs=4096 conv=notrunc,noerror --> Copy one hard disk partition to another hard disk
dd if=/dev/hdc of=/home/sam/mycd.iso bs=2048 conv=notrunc --> create am ISO image of a CD
dd if=/home/sam/partition.image of=/dev/sdb2 bs=4096 conv=notrunc,noerror --> restore a disk partition from an image file
dd if=/dev/fmem of=/home /sam /mem.bin bs=1024 --> Copy RAMmemory to a file
```

### dcfldd
- `dcfldd if=/dev/sda of=/media/image.dd`
- `dcfldd if=/dev/sda split=10M of=/media/image.dd` --> split the output image into multiple segments
- `dcfldd if=/dev/sda split=100M of=/media/image.dd hash=sha256 hashlog=/media/sha256.txt`
- `dcfldd if=/dev/sda hash=sha256,sha512 hashwindow=3G sha256log=sha256_hashes.txt sha512log=sha512_hashes.txt \ hashconv=after bs=8k conv=noerror,sync split=3G splitformat=aa of=sda_image.dd`
- `dcfldd if=/dev/sda vf=image.dd`

### LiME
```
insmod lime-<kernel_module>.ko "path=<directory> format=lime" --> Local
insmod lime-<kernel_module>.ko "path=tcp:<port> format=lime"
nc <IP Address of the suspect machine>:<port> > filename.mem
```

### Mounting image files on a Linux forensic workstation
- Mount a dd image file using mount command
- Mount a dd image file using a loop device

```
mkdir /mnt/dd
mount -o ro /home/jason/Documents/Windows_Evidence_001.dd /mnt/dd/ # in read-only mode
ls -la /mnt/dd/
```

```
`losetup -f` --> identify the unused loopback device
`losetup /dev/loop14 MAC_Evidence_001.dd`
images may contain hidden files and folders. To view them, press `Ctrl+H` on the keyboard
```

### Converting E01 Image File to dd Image File
- `xmount --in [input_image_format] [file_name.E01] [mount_directory]`
- `xmount --in ewf Windows_Evidence_001.E01 /home/jason/Documents`

### Converting E01 Image File to Raw Image File
- `ewfmount [file_name.E01] [mount_directory]`
- `ewfmount Windows Evidence 001.E01 /mnt/ewfmount/`
- `mount [raw_image_filename] [mount_directory] –o ro, loop,show_sys_files,streams_interface=windows`
- `mount ewf1 /mount/ -o ro,loop,show_sys_files,streams_interface=windows`

### Converting dd Image File to VHDX File
- `qemu-img convert –f <file format> <Source_Image_filename> –O vhdx <destination_filename.vhdx>`
- `qemu-img convert -f raw Evidence.dd -O vhdx Evidence.vhdx`

### Examining a dd Image File
- `fdisk -l <file_name>` --> list information such as sector size, start sector, and type of evidence file
- `mount –t ntfs –o ro,offset=[value_in_bytes] [dd_image_file_name] [mount_directory]`
- `umount [mount_directory]`

### Examining Physical Hard Disk
- `lshw` --> view the attached hard disk and file system
- `lsblk` -->  lists information about all blocked devices
- `mount –t ntfs-3g –o ro [partition_number] [mount_directory]` -->  Mount the Windows file system on Linux
- `mount -t ntfs-3g -o ro /dev/sdb2 /media/windows/`

### Examining Mac APFS Image File
- `losetup –f` --> identify the unused loopback device
- `losetup –r /dev/loop[number] [evidence.dd]` --> mount the APFS image file to the unused loopback device
- `mount /dev/loop[number] /mnt/apfs` or `apfs-fuse /dev/loop[number] /mnt/apfs`

### Enable Write Protection on the Evidence Media
- `mount -o ro <Path of the device>` --> The “ro” flag allows read-only access to the storage media

### Identifying RAID Drives in Linux System
- `lspci | grep RAID` --> check whether RAID is configured
- `cat /etc/mdadm.conf` --> obtain essential information about active RAID devices
- `cat /proc/mdstat` --> check the current status of RAID devices
- `mdadm --detail /dev/md125` --> examine the details of the RAID device
- `mdadm --examine /dev/sdd3` --> obtain information regarding a specific device component

## Access the drive at the BIOS level to copy data in the Host Protected Area (HPA)
- UFED Ultimate
- IM Solo-4 PLUS IT Enterprise

## Digital Forensic Imaging Tools 
- OSFClone

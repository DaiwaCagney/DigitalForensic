# Data Acquisition

## E01 image file works perfectly only when the forensics workstation is Windows-based
`xmount --in ewf Windows_Evidence_001.E01 /home/jason/Documents`

## Mounting image files on a Linux forensic workstation
Mount a dd image file using mount command

Mount a dd image file using a loop device

```
mkdir /mnt/dd
mount -o ro /home/jason/Documents/Windows_Evidence_001.dd /mnt/dd/ # in read-only mode
ls -la /mnt/dd/
```

`losetup -f` --> identify the first unused loop device

`losetup /dev/loop14 MAC_Evidence_001.dd`

images may contain hidden files and folders. To view them, press `Ctrl+H` on the keyboard

## Acquire RAM
`dd if=/dev/fmem of=/home/james/ubuntu_local_ram.dd bs=1MB` --> To acquire RAM locally

`insmod lime-6.2.0-35-generic.ko "path=../../ubuntu_local_ram.mem format=lime"` --> To acquire RAM locally

The kernel module version varies depending on the Ubuntu OS version installed on the suspect machine. In this case, it is 6.2.0-35-generic

### Remote acquisition of RAM using dd and netcat
```
nc -l 1234 > ubuntu_remote_ram.dd
dd if=/dev/fmem bs=1024 | nc 10.10.1.9 1234
```

## Linux
### dd
```
dd if=/dev/sda of=./diskimage.img # Create an image of a disk
dd if=/dev/sdb of=forensic.img
```

```
dir /dev
md5sum /dev/sdb
dd if=/dev/sdb of=/dev/sdc
dd if=/dev/sda of=/image_sda.dd
dd if=/dev/hdc of=/home/sam/mycd.iso bs=2048 conv=notrunc --> create am ISO image of a CD
dd if=/home/sam/partition.image of=/dev/sdb2 bs=4096 conv=notrunc,noerror --> restore a disk partition from an image file
```

### dcfldd
`dcfldd if=/dev/sda split=100M of=/media/image.dd hash=sha256 hashlog=/media/sha256.txt`

### Acquire Volatile Data
```
dd if=/dev/fmem of=<file_name.dd> bs=1MB --> Local
nc -l <port> > filename.dd
dd if=/dev/fmem bs=1024 | nc <IP Address> <port>
```

### LiME
```
insmod lime-<kernel_module>.ko "path=<directory> format=lime" --> Local
insmod lime-<kernel_module>.ko "path=tcp:<port> format=lime"
nc <IP Address of the suspect machine>:<port> > filename.mem
```

## Windows
```
wmic diskdrive list brief /format:list #DeviceID=\\.\PHYSICALDRIVE0
net use Z: "\\server2022\chfi-tools"
.\dd.exe if=\\.\physicaldrive0 of=z:\evidence\Windows_001.dd bs=512k --size --progress
Get-Filehash 'z:\evidence\Windows_001.dd' -Algorithm md5 | format-list
```

### Check if VM exist in a host:
Check HKEY_CLASSES_ROOT for file extension

Check network adapters

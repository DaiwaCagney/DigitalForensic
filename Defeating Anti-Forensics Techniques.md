# Defeating Anti-Forensics

## Windows

### Recycle Bin Storage on the NTFS File System
- `Drive:\$Recycle.Bin\%SID%`
- `C:\$Recycle.Bin\S-1-5-21-1234567890-123456789-123456789-1001`
- When a user deletes a file or folder, Windows creates two new files in the Recycle Bin for every deleted file
- The first file is ‘$R’ that contains the main contents that was deleted
- The second file created is ‘$I’, which contains the metadata of that deleted file

### Recycle Bin Forensics
- `copy $R* <Destination Directory>` --> recover the deleted files ($R files)

## File Carving
- File carving in SSDs is different from that in HDDs because files deleted from TRIM (enabled by default)-enabled SSDs cannot be recovered
- `cp /proc/$PID/exe /tmp/<file name>` --> if an executable erases itself, its contents can be retrieved from a /proc memory image

## Recovering Deleted Partitions
- R-Studio
- EaseUS Data Recovery Wizard
- Stellar Data Recovery Professional
- Hetman Partition Recovery
- Remo Recover
- MiniTool® Data Recovery Software

## Recovering ReFS Volumes Using ReFSUtil
- `refsutil salvage -QA <source volume> <working directory> <target directory> <options>` --> quick scan
- `refsutil salvage -FA <source volume> <working directory> <target directory> <options>` --> fully automatic mode scan
- `refsutil salvage -D <source volume> <working directory> <options>` --> verify the source volume
- `refsutil salvage -QS <source volume> <working directory> <options>` --> Quick scan phase
- `refsutil salvage -FS <source volume> <working directory> <options>` --> Full-scan mode
- `foundfiles.<volume signature>.txt` --> The identified files will be logged into this file
- `refsutil salvage -C <source volume> <working directory> <target directory> <options>` --> copy all the logged files
- `refsutil salvage -SL <source volume> <working directory> <target directory> <file list> <options>` --> copy all the files in the file list from the source to the target directory
- `refsutil salvage -IC <source volume> <working directory> <options>` --> copy files using an interactive console

## Bypassing BIOS Passwords by Resetting CMOS Using Jumpers
- Check the computer or motherboard manufacturer’s documentation to locate the jumpers/DIP switches
- If the document is not available, by default, the jumper position is across pins 1 and 2
- Shut down the system and unplug the power cord
- Move the jumper from its default position so that it is across pins 2 and 3; this clears the BIOS/CMOS settings
- Now, place the jumper in the previous position. Start the machine, and the system loads the OS

## Bypassing BIOS Passwords by Removing CMOS Battery
- Shut down the system and disconnect the power plug
- Open the CPU cabinet and locate the CMOS battery (silver circular battery) on the motherboard
- The CMOS battery was removed from the socket and left for 20–30 minutes. This flushes out the CMOS memory that stores BIOS passwords and other configurations
- Replace the battery and start the system normally
- Sometimes, manufacturers use capacitors to provide backup power to the CMOS battery. Thus, if the first attempt fails, keep the battery out for 24 hours

## Tool to Reset Admin and Local User Password: PassFab 4WinKey
- Create a Windows password reset bootable USB
- Boot the Windows system with that bootable USB
- Reset the password of the selected account

## Bypassing Windows User Password by Booting Live USB
- Boot the machine from the Live USB using tools such as CAINE to get access to the hard disk and its contents
- After accessing the hard drive, investigators can capture and examine a forensic image of the drive using Guymager

## Detecting Data Hiding in File System Structures
- OSForensics
- Host Protected Areas (HPA) and Device Configuration Overlay (DCO) are hidden areas on the hard drives
- NTFS-based hard disks contain bad clusters in a metadata file such as $BadClus and the MFT entry 8 represents these bad clusters
- $BadClus is a sparse file that allows attackers to hide unlimited data because they can allocate more clusters to $BadClus to hide more data
- attackers use DPAs, DCOs, and slack spaces to hide data that are not visible to either the BIOS or OS

## Alternate Data Streams (ADS)
- Windows NTFS
- hide any number of streams into one single file without modifying the file size, functionality, etc., except the file date
- `gci -recurse | % { gi $_.FullName -stream * } | where stream -ne ':$Data'` --> search for ADS in the hard drive
- `notepad filename.extension:streamname.extension`
- `notepad simple_file1.text:secret_message1.txt`
- Stream Detector

## Detecting Overwritten Data/Metadata
- NTFS file system
- STANDARD_INFORMATION: Contain timestamps modified at user-level
- FILE_NAME: Contain timestamps modified at system kernel level
- analyzeMFT
- When the $FILE NAME creation date and $STANDARD INFORMATION creation date (for any file) do not match, the file has been timestomped

## Detecting and Unpacking Program Packers
- Detect it Easy (DiE)
- Exeinfo PE
- `upx.exe –d –o [unpacked_file_name] [packer_file_name]`

## Detecting USB Devices
- `Get-ItemProperty –Path HKLM:\SYSTEM\CurrentControlSet\Enum\USBSTOR\*\*\ | Select FriendlyName`
- `HKEY_LOCAL_MACHINE\SYSTEM\CurrentCon trolSet\Enum\USBSTOR\Disk&Ven_[VendorN ame]&Prod_[ProductName]&Rev_1.00\[Seri alNo]`

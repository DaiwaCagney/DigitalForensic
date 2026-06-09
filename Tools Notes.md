## Volatility:
```
sudo snap install volatility-phocean --> install
volatility -f {image.vmem} imageinfo
volatility -f {image.vmem} --profile={profile} pslist
volatility -f {image.vmem} --profile={profile} pstree --> parent & child
volatility -f {image.vmem} --profile={profile} psxview --> process trying to hide
volatility -f {image.vmem} --profile={profile} connscan
volatility -f {image.vmem} --profile={profile} sockets
volatility -f {image.vmem} --profile={profile} netscan
volatility -f {image.vmem} --profile={profile} cmdline
volatility -f {image.vmem} --profile={profile} procdump -p {PID} --dump-dir . --> dump the executable
volatility -f {image.vmem} --profile={profile} memdump -p {PID} --dump-dir . --> dump the dmp file
strings {PID.dmp} | grep -Fi "IP" -C 5
strings {PID.dmp} | less
volatility -f {image.vmem} --profile={profile} hivelist
volatility -f {image.vmem} --profile={profile} printkey -K "Software\Microsoft\Windows|CurrentVersion\Run" --> check what process run at start-up
strings {PID.dmp} | grep -Fi "executable.exe"
```

```
volatility -f {image.vmem} kdbgscan --> look for processes & modules
volatility -f {image.vmem} --profile={profile} consoles --> any one using cmd when memory dump
volatility -f {image.vmem} --profile={profile} timeliner --> all events by time
volatility -f {image.vmem} --profile={profile} filescan --> all files in used when memory dump
volatility -f {image.vmem} --profile={profile} psscan --> dead process
volatility -f {image.vmem} --profile={profile} dlllist
volatility -f {image.vmem} --profile={profile} dumpfiles
volatility -f {image.vmem} --profile={profile} vboxinfo
```

```
volatility -f {image.vmem} --profile={profile} handles -p {PID} -t file
volatility -f {image.vmem} --profile={profile} handles -p {PID} -t key
```

```
volatility -f {image.vmem} --profile={profile} malfind -p {PID} --> identify hidden processes in the user mode memory
```

## The Sleuth Kit (TSK):
```
mmls "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_002.dd" --> view partition tables
fsstat "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" --> view the type of file system and the OS related to the image
img_stat "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" --> view the details of the selected image
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 0 --> display an overview of the MFT file (Master File Table (MFT) has an entry for every file and directory)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 1 --> display the MFTMirr File Overview (non-resident attribute that contains a backup copy of the first MFT entry)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 7 --> display an overview of the Boot File (contains the boot sector of the file system)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 3 --> display an overview of the volume filesystem metadata file (contains the volume label and other version-related information)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 4 --> display an overview of the AttrDef file (defines the names and type identifiers for each type of attribute)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 6 --> display an overview of the Bitmap file (determines the status of the cluster)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 8 --> display an overview of the BadClus file (NTFS keeps track of the damaged clusters by allocating them to a $DATA attribute of the BadClus file system metadata file)
istat -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 9 --> display an overview of the Secure file (stores the security descriptors that define the access control policy for a file or directory)
fls -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" --> list the files and directory name
ils -r "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" --> view the list of inode information
ffind -f ntfs "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" 96 --> find the name of the file or directory in inode number 96
tsk_recover -i raw -e "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" "C:\Users\Administrator\Desktop\Retrieved Files by SleuthKit" --> recover files
fls -m 63 "E:\CHFI-Tools\Evidence Files\Forensic Images\Windows_Evidence_001.dd" --> gather the temporal data (-m argument displays files in a time-machine format so that a timeline can be created with mactime(1). 63 represents that the the file system starts in sector 63)
```

```
img_stat disk.flag.img
mmls disk.flag.img
fsstat -o <offset> disk.flag.img
fsstat -o 2048 disk.flag.img
fls -o <offset> disk.flag.img
fls -o 360448 disk.flag.img
fls -o <offset> disk.flag.img <inode>
fls -o 360448 disk.flag.img 1995
tsk_recover [-e] -o <offset> [-d <inode>] disk.flag.img <output_dir>
tsk_recover -e -o 360448 -d 3981 disk.flag.img ./my_folder_recovery
```

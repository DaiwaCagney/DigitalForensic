# Defeating Anti-Forensics

## Windows

### Recycle Bin Storage on the NTFS File System
- `Drive:\$Recycle.Bin\%SID%`
- `C:\$Recycle.Bin\S-1-5-21-1234567890-123456789-123456789-1001`
- When a user deletes a file or folder, Windows creates two new files in the Recycle Bin for every deleted file
- The first file is ‘$R’ that contains the main contents that was deleted
- The second file created is ‘$I’, which contains the metadata of that deleted file

### Recycle Bin Forensics
`copy $R* <Destination Directory>` --> recover the deleted files ($R files)

`%TEMP%`

`shell:startup`

`shell:common startup`

`HKLM\Software\WOW6432Node\Microsoft\Windows\CurrrentVersion\Uninstall` --> enumerate this key to detect applications installed on the system

## Linux

`cp /proc/$PID/exe /tmp/<file name>`

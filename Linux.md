# Linux

## Manual
- `man man`
- `man netstat`

## Hostname, Date and Time
- `hostname`
- `date`
- `cat /etc/timezone`
- `date +%s` --> epoch timestamp

## Uptime
- `uptime`
- `top`
- `w`

## Network
- `ip addr show`
- `netstat`
- `netstat -i` --> list of network interfaces
- `ifconfig`
- `ifconfig <interface> -promisc` --> disable promiscuous mode

## Routing Table
- `netstat -rn`
- `ip r`

## Open Port
- `nmap -sT localhost` --> TCP
- `nmap -sU localhost` --> UDP

## Open Files and Mounted File System
- `lsof | more`
- `lsof -u <user_name>` --> list the open files for the user currently logged into the system
- `mount` --> retrieve detailed information on all file systems that are mounted
- `df` --> determine the amount of disk space used and free disk space on the mounted file systems on a Linux system

## Reading ELF Files
- `readelf`
- `readelf --file-header /sbin/mount.fuse`

## Loaded Kernel Modules
- `lsmod` --> reads the contents of /proc/modules
- `modinfo <kernel_module>` --> If a particular kernel module is not specified, the command will search in the directory /lib/modules/kernel-version

## Running Processes
- `ps -aux`
- `ps -auxww` --> obtain information on running processes for all user accounts

## Process associated with Port
- `netstat -tulpn`
- `lsof -i -P -n | grep LISTEN`
- `ss -l -p -n | grep <PID>`
- `/proc`

## Services
- `systemctl list-units --type=service -all`
- `systemctl --type=service -state=<state>` --> dead, failed, restart, enabled, disabled, running
- `systemctl --type=service -state=<state1>,<state2>`
- `systemctl --type=service -state=<state> | grep <service_type>`

## Disk Partition and Swap Areas
- `cat /proc/swaps`
- `cat /proc/partitions`

## Kernel Message
- `dmesg`

## System Information
- `cat /proc/cpuinfo` --> details about the CPU on a machine
- `cat /proc/self/mounts` --> mount points and mounted external devices

## Kernel Version
- `uname -r`
- `cat /proc/version`
- `hostnamectl | grep Kernel`

## User Account
- `cat /etc/passwd`
- `cut -d: -f1 /etc/passwd`
- `id <user>`
- `ausearch -ui <user id> --interpret` --> track all the user events

## Logged-in Users and Login History
- `w` --> logged-in users
- `last`
- `last -a`
- `last -f /var/log/wtmp` --> login history, system reboot time, system status
- `cat /var/log/auth.log`
- `grep sudo /var/log/auth.log` --> execution of sudo command

## System Log
- `cat /var/log/syslog`
- `/var/log/messages`
- `cat /var/log/kern.log`

## Command History
- `history`
- `history -c` -->  clear history
- `gedit .bash_history`

## Hidden Files
- `ls -al`

## Rootkit
- `rkhunter --check --rwo`
- `chkrootkit`

## File Signature
- `xxd image.png | head`
- `xxd document.pdf | head`
- `file document.pdf`
- `strings document.pdf`
- `strings -t d <image_filename> | grep -iE “search_string”`
- `strings -t d Evidence.dd | grep -iE "kitty"`

## Find Writable Files
- `find / -writable –type f 2>/dev/null | grep “<search_directory>”`
- `find / -writable -type f 2>/dev/null | grep "/var/log"`

## Cron Jobs
- `sudo systemctl status cron` --> check Cron service running
- `sudo service cron start` --> start the Cron service
- `grep CRON /var/log/syslog` --> view Cron logs
- `crontab -l` --> list all Cron jobs
- `cat /etc/crontab` --> display the root user’s Cron jobs
- `sudo crontab -u [username] -l`
- `/etc/cron.d/`
- `/var/spool/cron/`
- `/etc/cron.hourly/`
- `/etc/cron.daily`
- `/etc/cron.weekly/`
- `/etc/cron.monthly/`
- `<Minute> <Hour> <Day> <Month> <Week> <script or command for schedule>`

## Hash
- `md5sum filename`

## Volatility
- `python3 vol.py -f Linux.mem linux.lsof`
- `python3 vol.py -f Linux.mem linux.mount`
- `python3 vol.py -f Linux.mem linux.bash`
- `python3 vol.py -f Linux.mem linux.lsmod`
- `python3 vol.py -f Linux.mem linux.pslist`
- `python3 vol.py -f Linux.mem linux.psscan`
- `python3 vol.py -f Linux.mem linux.malfind`

## Audit Log
- `apt install -y auditd`
- `aureport`
- `ausearch -ui <user id> --interpret` --> track all the user events

## arp
- `arp`

## Clipboard
- `xclip -o`

## SSH
- `grep "Failed password" /var/log/auth.log`
- `egrep "Failed|Failure" /var/log/auth.log`
- `journalctl -u ssh` --> SSH Key
- `lastlog` --> recent logged-in users
- `grep "Failed password" /var/log/auth.log | awk '{print $11}' | uniq -c | sort -nr`

## tcpdump
- `tcpdump -i eth0`

## PhotoRec
- `apt install testdisk`
- `photorec Linux.mem`

## Mount Evidence Volume
- `lsblk` --> check successfully attached volume
- `file -s /dev/xvdf1` --> check file system format

# Defeating Anti-Forensics

## Windows
`copy $R* <Destination Directory>`

`%TEMP%`

`shell:startup`

`shell:common startup`

`HKLM\Software\WOW6432Node\Microsoft\Windows\CurrrentVersion\Uninstall` --> enumerate this key to detect applications installed on the system

## Linux

`cp /proc/$PID/exe /tmp/<file name>`

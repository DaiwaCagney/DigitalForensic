# Database

## MSSQL

### Collecting Volatile Database Data
- ApexSQL Audit

### Collecting Primary Data File and Active Transaction Logs
- Collect the database files (.mdf) and log files (.ldf) from `C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\DATA`

#### SQLCMD
- `sqlcmd -S WIN-1BKS09O92OO -e -s"," -E` --> WIN-1BKS09O92OO is the server, connect to a server
- `:out E:\ForensicTest.txt` --> create a text file named “ForensicTest” and log the output of the collected data to E:\
- `sp_helpdb moviescope` --> determine the locations of the transaction log files associated with moviescope database
- `go`
- `dbcc loginfo` --> obtain the VLF (virtual log files) allocations for the moviescope database
- `go`

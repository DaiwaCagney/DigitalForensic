# Database

## MSSQL

### Collecting Volatile Database Data
- ApexSQL Audit

### Collecting Primary Data File and Active Transaction Logs
- Collect the database files (.mdf) and log files (.ldf) from `C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\DATA`

#### Collecting Primary Data File and Active Transaction Logs using SQLCMD
- `sqlcmd -S WIN-1BKS09O92OO -e -s"," -E` --> WIN-1BKS09O92OO is the server, connect to a server
- `:out E:\ForensicTest.txt` --> create a text file named “ForensicTest” and log the output of the collected data to E:\
- `sp_helpdb moviescope` --> determine the locations of the transaction log files associated with moviescope database
- `go`
- `dbcc loginfo` --> obtain the VLF (virtual log files) allocations for the moviescope database
- `go`

#### Collecting Active Transaction Logs using SQL Server Management Studio
- `Select * from ::fn_dblog(NULL, NULL)` --> displays the active portion of the transaction log file
- Database Consistency Checker (DBCC) commands
- `DBCC LOG(<databasename>, <output>)` --> retrieve the active transaction log files
- `DBCC LOG(moviescope, 3)` --> view the transaction log file for the moviescope database, with the detailed information for each operation

### Collecting Database Plan Cache
- `select * from sys.dm_exec_cached_plans cross apply sys.dm_exec_sql_text (plan_handle)` --> retrieve the SQL text of all cached entries
- `select * from sys.dm_exec_query_stats` --> view the aggregate performance statistics for the cached query plans
- `select * from sys.dm_exec_cached_plans cross apply sys.dm_exec_plan_attributes(plan_handle)` --> view one row per plan attribute for the plan specified by the plan handle

### Collecting SQL Server Trace Files and Error Log
- trace files (.trc)
- `C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER\MSSQL\LOG`
- SQL Server error logs also at the same path

## MSSQL Forensics
1. Examine the Windows Logs
2. Examine the Error Logs
3. Examine the Trace Files
4. Examine the Active Transaction logs --> page ID & row location of record & row data offset
5. Examine the Data Page --> `dbcc traceon(3604) dbcc page(moviescope,1,154,1)` --> object ID
6. View the Object --> `Select * from sysobjects where id = 21575115` --> find the name of the object/table
7. Gather the Object Schema --> `SELECT sc.colorder , sc.name, st.name as 'datatype', sc.length FROM syscolumns sc, systypes st WHERE sc.xusertype = st.xusertype and sc.id = 21575115 ORDER BY colorder`
8. View the Modified Record --> `dbcc trace (3604) dbcc page(moviescope,1,154,1)` --> view the data page 154, scroll down to slot no. 4 (data row no. 4)
9. Identify the Data Type
10. Compare the Row Logs --> `dbcc log(moviescope, 3)` --> Note down the hex values of RowLog Contents 0 and RowLog Contents 1

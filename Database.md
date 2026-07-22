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

## MySQL
- Information_Schema table --> database metadata
- `SELECT CONCAT(table_schema, table_name) as 'table name_table rows' FROM information_schema.tables WHERE table_rows > 10 AND table_schema not in ('information_schema','mysql','performance_schema');` --> tables more than 10 rows
- Mysqldump --> To dump single or multiple databases for backup purpose
- Mysqlaccess --> To check the access privileges defined for a hostname or username
- myisamlog --> To process the MyISAM log file and perform recovery operation, display version information, etc
- Myisamchk --> To obtain the status of the MyISAM table, identify the corrupted tables, repair the corrupted tables, etc
- Mysqlbinlog --> To display the content of bin logs (mysql-bin.nnnnnn) in text format 
- mysqldbexport --> To export metadata, data, or both from one or more databases

## MySQL Forensics
1. Examine the error log files
2. Examine the General Query log file
3. Create a backup of the database --> `mysqldump -u root -p wordpress > wordpress_evidence.sql`
4. create a database in the forensic examiner’s machine and dump the contents of the previously taken backup
  - `mysql -u root -p` --> Log in to mysql server
  - `create database wordpress;` --> Create a database with the same name
  - `\q` --> Exit the mysql terminal
  - `mysql -u root -p wordpress < C:/wordpress_evidence.sql` --> Copy all contents of the dump file to the newly created database
5. Select Database
  - `show databases;`
  - `use wordpress;`
6. View Tables in the Database
  - `show tables;`
7. View Users in the Database
  - `select * from wp_users;`
  - Make a note of the user ID
8. View Columns in the Table
  - `show columns in wp_posts;`
9. Dump all data related
  - `select * from wp_posts where post_author = '123' into outfile 'E:\evidence.txt';`

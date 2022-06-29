# Detach and Attach

Detaching and attaching a database is useful when you want to move a database.

Detaching a database will remove it from the instance of SQL Server but still keep the database intact within its data and transaction log files.

A database can be attached using the data and transaction log files from the detached database. It's important that all the files are available.

Detaching and attaching is only done in exceptional circumstances whereas backup and restore is used under normal circumstances. In most cases backup and restore will also be the safer and quicker choice.

To detach a database, e.g. `myDb`, the following system stored procedure can be used:

```sql
EXECUTE sp_detach_db @dbname = N'myDb';
```

The detached database can be reattached using the following command:

```sql
CREATE DATABASE myDb ON
(FILENAME = N'location\myDb_1.mdf'),
(FILENAME = N'location\myDb_2.ndf'),
(FILENAME = N'location\myDb_log.ldf')
FOR ATTACH;
```

See [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-detach-and-attach-sql-server?view=sql-server-ver16) and this [DBA Stack Exchange post](https://dba.stackexchange.com/questions/11177/attach-detach-vs-backup-restore) for more information.

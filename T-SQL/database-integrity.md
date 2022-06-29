# Database Integrity

SQL Server has a Database Console Command (DBCC) that checks the physical and logical integrity of all objects in a database. This can be used to detect *storage corruption*.

Use the query below to see when the last integrity check was run on e.g. the database `AdventureWorks`:

```sql
DBCC DBINFO(N'AdventureWorks') WITH TABLERESULTS;
```

The value `dbi_dbccLastKnownGood` will reveal the date and time.

To check the integrity of a database, run the following query:

```sql
DBCC CHECKDB(N'AdventureWorks');
```

The DBCC command comes with a set of repair options but these should be used as a last resort. Instead, the last good backup should be used.

Generally, the integrity of a database should be checked regularly to make sure that corruption is detected while there is still a good backup around.

See [MSSQLTip's article](https://www.mssqltips.com/sqlservertip/4381/sql-server-dbcc-checkdb-overview/) or [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql?view=sql-server-ver16) for more information.

For a ready-made, good and free maintenance solution, see [Ola Hallengren's repository](https://github.com/olahallengren/sql-server-maintenance-solution).

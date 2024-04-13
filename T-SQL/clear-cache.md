# Clear Cache

## Plan Cache

SQL Server stores query plans in the query plan cache. This way SQL Server can reuse a cached query plan and save CPU resources when the same query is run again.

Sometimes it can be beneficial to clear the plan cache, e.g. in a development environment. SQL Server has several Database Console Commands (DBCC) to do this.

The following command can be used to clear the entire plan cache:

```sql
DBCC FREEPROCCACHE;
```

The `WITH NO_INFOMSGS` option can be used to suppress output messages. The command can also be used to clear a specific plan or a resource pool.

To clear the plan cache for a specific database, e.g. `AdventureWorks`, use the following command:

```sql
DECLARE @db int = DB_ID(N'AdventureWorks');
DBCC FLUSHPROCINDB(@db);
```

See [MSSQLTip's article](https://www.mssqltips.com/sqlservertip/4714/different-ways-to-flush-or-clear-sql-server-cache/) or [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-freeproccache-transact-sql?view=sql-server-ver16) for more information.

## Buffer Cache

SQL Server copies 8KB data pages to the buffer cache (or buffer pool) whenever it is writing to or reading from a database. This way SQL Server can quickly access often used data and only read from disk whenever new data is needed. When the buffer cache is full, older or less used pages will be flushed from memory in order to make space for new pages.

Sometimes it can be beneficial to clear the buffer cache, e.g. in a development environment when performance testing. SQL Server has a DBCC command to do this.

The following command can be used to clear the buffer cache:

```sql
DBCC DROPCLEANBUFFERS;
```

Again, the `WITH NO_INFOMSGS` option can be used to suppress output messages.

See [SQLShack's article](https://www.sqlshack.com/insight-into-the-sql-server-buffer-cache/) or [Microsoft's documentation](https://learn.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql?view=sql-server-ver16) for more information.

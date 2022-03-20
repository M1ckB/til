# Clear Cache

SQL Server stores query plans in the query plan cache. This way SQL Server can reuse a cached query plan and save CPU resources when the same query is run again.

Sometimes it can be beneficial to clear the cache, e.g. in a development environment. SQL Server has several Database Console Commands (DBCC) to do this.

The following command can be used to clear the entire plan cache (but also to clear a specific plan or a resource pool):

``` sql
DBCC FREEPROCCACHE;
```

The `WITH NO_INFOMSGS` option can be used to suppress output messages.

To clear the plan cache for a specific database, e.g. `AdventureWorks`, use the following command:

``` sql
DECLARE @db int = DB_ID(N'AdventureWorks');
DBCC FLUSHPROCINDB(@db);
```

See [MSSQLTip's article](https://www.mssqltips.com/sqlservertip/4714/different-ways-to-flush-or-clear-sql-server-cache/) or [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-freeproccache-transact-sql?view=sql-server-ver15) for more information.

# Reorganize or Rebuild Index

Index fragmentation on SQL Server can be fixed either by reorganizing or rebuilding an index.

Reorganizing fixes the physical ordering of leaf level pages and compacts them. It is a lightweight operation and is always online. Rebuilding drops the index and creates a new, defragmented structure. Index rebuilds are an offline operation in Standard Edition but can be done as an online operation in Enterprise Edition.

It is recommended to *reorganize* an index if the fragmentation exceeds 5 percent but is less than 30 percent and *rebuild* an index when fragmentation exceeds 30 percent.

To reorganize an index, e.g. `IX_dbo_test` on table `dbo.test`, the following statement can be used:

```sql
ALTER INDEX IX_dbo_test 
ON dbo.test
REORGANIZE;
```

To rebuild an index, the following statement can be used:

```sql
ALTER INDEX IX_dbo_test 
ON dbo.test
REBUILD;
```

Instead of specifying a specific index, the `ALL` option can be used to reorganize or rebuild all indexes on a table.

Generally, handling index fragmentation should be part of a maintenance plan.

For more information, check out [SQLShack's article](https://www.sqlshack.com/maintaining-sql-server-indexes/) or [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16).

For a ready-made, good and free maintenance solution, see [Ola Hallengren's repository](https://github.com/olahallengren/sql-server-maintenance-solution).

# Missing Index

SQL Server does, during the process of executing a query, record if it would benefit from an index. The information is gathered and can be retrieved via SQL Servervs Dynamic Management Views (DMVs): `sys.dm_db_missing_index_groups` (index handle), `sys.dm_db_missing_index_group_stats` (index statistics) and `sys.dm_db_missing_index_details` (table and column details).

This information is useful in determining which indexes are needed to secure efficient queries and satisfy production needs but it is, however, only a byproduct of SQL Server's execution plan compilation and therefore not perfect. It is up to us to judge if the suggestions are sensible.

The following query will retrieve information about missing indexes:

```sql
SELECT
    db.[name] AS [database],
    d.statement AS [table],
    d.equality_columns AS equalityColumns,
    d.inequality_columns AS inequalityColumns,
    d.included_columns AS includeColumns,
    s.user_seeks AS userSeeks, -- Number of seeks that the missing index could have been used for
    s.user_scans AS userScans, -- Number of scans that the missing index could have been used for
    s.last_user_seek AS lastUserSeek,
    s.last_user_scan AS lastUserScan,
    s.avg_total_user_cost AS avgUserCost, -- Average cost of the user queries that could be reduced by the index
    s.Avg_User_Impact as avgUserImpact -- Average percentage benefit that user queries could experience from implementing the missing index
FROM sys.dm_db_missing_index_groups AS h
INNER JOIN sys.dm_db_missing_index_group_stats AS s ON s.group_handle = h.index_group_handle
INNER JOIN sys.dm_db_missing_index_details AS d ON d.index_handle = h.index_handle
INNER JOIN sys.databases AS db ON db.database_id = d.database_id
ORDER BY (s.user_seeks + s.user_scans) DESC;
```

**Keep in mind that the statistics are flushed when the SQL Server service is restarted.** Therefore, the data about missing indexes should be gathered regularly and be persisted in a table.

When making decisions about index creations, consider the following questions:

- How often is the query executed?
- How expensive is the query?
- How long time does the query take to execute?
- Do similar indexes exist already?

For more information, see [SQLShack's article](https://www.sqlshack.com/collecting-aggregating-analyzing-missing-sql-server-index-stats/) or Microsoft's documentation on DMVs related to missing indexes:

- [sys.dm_db_missing_index_groups](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-groups-transact-sql?view=sql-server-ver16)
- [sys.dm_db_missing_index_group_stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-group-stats-transact-sql?view=sql-server-ver16)
- [sys.dm_db_missing_index_details](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql?view=sql-server-ver16)

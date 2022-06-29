# Index Fragmentation

SQL Server has a dedicated Dynamic Management View (DMV) that shows index fragmentation: `sys.dm_db_index_physical_stats`. It can uncover both *external  fragmentation* (also called logical fragmentation) and *internal fragmentation* (also called page density).

Run the following query to get an overview of indexes that has 1.000 pages or more and some degree of fragmentation:

```sql
SELECT
    s.[name] AS [schema],
    t.[name] AS  [table],
    i.[name] AS [index],
    ddips.[index_type_desc] AS indexType,
    ddips.avg_fragmentation_in_percent, -- External fragmentation
    ddips.avg_page_space_used_in_percent, -- Internal fragmentation
    ddips.page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL, NULL, 'SAMPLED') AS ddips
INNER JOIN sys.tables t ON t.object_id = ddips.object_id
INNER JOIN sys.schemas s ON s.schema_id = t.schema_id
INNER JOIN sys.indexes i ON i.object_id = ddips.object_id AND i.index_id = ddips.index_id
WHERE ddips.page_count > 999
AND ddips.avg_fragmentation_in_percent > 0
ORDER BY ddips.avg_fragmentation_in_percent DESC;
```

For more information, check out [SQLShack's article](https://www.sqlshack.com/sql-index-maintenance/) or [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes?view=sql-server-ver16).

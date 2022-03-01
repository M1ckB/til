# Index Usage

SQL Server has two Dynamic Management Views (DMVs) that track the usage of indexes. These are a great help to determine if indexes are being used efficiently or should be modified or dropped.

While there are a lot of useful statistics in the DMVs, the query below only includes the most fundamental ones:

``` sql
WITH pages AS (
	-- Get page count
	SELECT
		[OBJECT_ID],
		SUM([used_page_count]) AS used_page_count
	FROM sys.dm_db_partition_stats
	GROUP BY [OBJECT_ID]
)

SELECT
	OBJECT_NAME(ix.OBJECT_ID) AS [table],
	ix.[name] AS [index],
	ix.[type_desc] indexType,
	ps.[used_page_count] AS indexPages,
	-- Usage stats
	ixus.user_seeks AS numberOfSeeks,
	ixus.user_scans AS numberOfScans,
	ixus.user_lookups AS numberOfLookups,
	ixus.user_updates AS numberOfModificationOperations,
	ixus.last_user_seek AS lastSeek,
	ixus.last_user_scan AS lastScan,
	ixus.last_user_lookup AS lastLookup,
	ixus.last_user_update AS lastModificationOperation,
	-- Operational stats
	ixos.leaf_insert_count numberOfInserts,
	ixos.leaf_update_count numberOfUpdates,
	ixos.leaf_delete_count numberOfDeletes
FROM sys.indexes AS ix
INNER JOIN sys.dm_db_index_usage_stats AS ixus ON ixus.index_id = ix.index_id AND ixus.[OBJECT_ID] = ix.[OBJECT_ID]
INNER JOIN sys.dm_db_index_operational_stats (NULL, NULL, NULL, NULL) AS ixos ON ixos.index_id = ix.index_id AND ixos.[OBJECT_ID] = ix.[OBJECT_ID]
INNER JOIN pages AS ps on ps.[OBJECT_ID] = ix.[OBJECT_ID]
WHERE OBJECTPROPERTY(ix.[OBJECT_ID], 'IsUserTable') = 1;
```

Keep in mind that the statistics are flushed when the SQL Server service is restarted.

See [SQLShack's article](https://www.sqlshack.com/gathering-sql-server-indexes-statistics-and-usage-information/) or Microsoft's documentation on [index usage stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql?view=sql-server-ver15) or [index operational stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql?view=sql-server-ver15) for more information.

# Index Usage

SQL Server has two Dynamic Management Views (DMVs) that track the usage of indexes. These are a great help to determine if indexes are being used efficiently or should be modified or dropped.

While there are a lot of useful statistics in the DMVs, the query below only includes the most fundamental ones for the current database:

```sql
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
    ix.[type_desc] AS indexType,
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
    ixos.leaf_insert_count AS numberOfInserts,
    ixos.leaf_update_count AS numberOfUpdates,
    ixos.leaf_delete_count AS numberOfDeletes
FROM sys.indexes AS ix
INNER JOIN sys.dm_db_index_usage_stats AS ixus ON ixus.database_id = DB_ID() AND ixus.index_id = ix.index_id AND ixus.[OBJECT_ID] = ix.[OBJECT_ID]
INNER JOIN sys.dm_db_index_operational_stats (DB_ID(), NULL, NULL, NULL) AS ixos ON ixos.index_id = ix.index_id AND ixos.[OBJECT_ID] = ix.[OBJECT_ID]
INNER JOIN pages AS ps on ps.[OBJECT_ID] = ix.[OBJECT_ID]
WHERE OBJECTPROPERTY(ix.[OBJECT_ID], 'IsUserTable') = 1
ORDER BY (ixus.user_seeks + ixus.user_scans + ixus.user_lookups);
```

Be aware, the *usage stats* only tells about operations (and not e.g. number of rows) that appeared in query plans. With this in mind, the usage stats are a perfect tool to get an overview of index usage and maintenance.

Rules of thumb:

- An index with usage stats containing many zero values means that the index isn't being used (very much) and the gain of having it is small. Moreover, if the index is being modified a lot means that the cost of maintaining the index is relatively big. This makes the index a good candidate for being dropped
- An index that is *scanned* a lot but only has few *seeks* is not used properly and should be replaced
- An index with a large number of *lookups* means that the index should be altered to include the most frequently looked up columns
- A clustered index with a large number of *scans* means that it can be efficient to create a non-clustered index to cover the queries (unless the table is small)

**Keep in mind that the *usage stats* are reset when the SQL Server is restarted while the *operational stats* are reset whenever the metadata is brought in and out of the metadata cache.**

See [SQLShack's article](https://www.sqlshack.com/gathering-sql-server-indexes-statistics-and-usage-information/) or Microsoft's documentation on [index usage stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-usage-stats-transact-sql?view=sql-server-ver16) or [index operational stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-operational-stats-transact-sql?view=sql-server-ver16) for more information.

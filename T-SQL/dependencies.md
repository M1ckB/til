# Dependencies

SQL Server has some DMVs that exposes information about depencies between objects. These include:

- [sys.dm_sql_referencing_entities](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-sql-referencing-entities-transact-sql?view=sql-server-ver16)
- [sys.dm_sql_referenced_entities](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-sql-referenced-entities-transact-sql?view=sql-server-ver16)
- [sys.sql_expression_dependencies](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-sql-expression-dependencies-transact-sql?view=sql-server-ver16)

The first two DMVs work as each other's opposite. It is possible to find all objects referenced by an object, e.g. `dbo.table`:

```sql
SELECT
  *
FROM sys.dm_sql_referenced_entities ('dbo.table', 'OBJECT');
```

It is possible to find all objects referencing the same object:

```sql
SELECT
  *
FROM sys.dm_sql_referencing_entities ('dbo.table', 'OBJECT');
```

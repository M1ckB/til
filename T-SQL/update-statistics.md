# Update Statistics

SQL Server's cardinality estimator uses statistcs to estimate how many rows will be returned when doing an operation.

If statistics are inaccurate, queries will perform poorly because because too many or too few resources were allocated.

SQL Server automatically update statistics but sometimes this isn't sufficient to create efficient query plans.

The stastics SQL Server maintains, e.g. for index `IX_dbo_test` on table `dbo.test`, can be seen by using the following command:

```sql
DBCC SHOW_STATISTICS(N'dbo.test', N'IX_dbo_test'); 
```

Updating statistics for an entire table or a specific index or column on a table can be done using the following command:

```sql
-- Table:
UPDATE STATISTICS dbo.test WITH FULLSCAN;
-- Specific index on table:
UPDATE STATISTICS dbo.test IX_dbo_test WITH FULLSCAN;
-- Specific column on table:
UPDATE STATISTICS dbo.test (col1) WITH FULLSCAN;
```

Updating statistics will potentially cause SQL Server to traverse a table many times and can therefore be a resource expensive operation. See Brent Ozar's [post](https://www.brentozar.com/archive/2014/01/update-statistics-the-secret-io-explosion/) for more information. Furthermore, updating statistics will cause query plans to be recompiled. See another of Brent Ozar's [posts](https://www.brentozar.com/archive/2020/06/updating-statistics-causes-parameter-sniffing/) on this.

Generally, updating statistics should be left for SQL Server to do automatically or be part of a maintenance plan.

For more information, check out [Microsoft's documentation](https://docs.microsoft.com/en-us/sql/t-sql/statements/update-statistics-transact-sql?view=sql-server-ver16).

For a ready-made, good and free maintenance solution, see [Ola Hallengren's repository](https://github.com/olahallengren/sql-server-maintenance-solution).

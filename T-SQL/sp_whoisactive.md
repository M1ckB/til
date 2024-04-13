# sp_WhoIsActive

`sp_WhoIsActive` is a stored procedure used for real-time activity monitoring on SQL Server. It is open source and is being maintained on [GitHub](https://github.com/amachanic/sp_whoisactive).

## Introduction

It shows by default only active sessions and sleeping sessions that have an open transaction. The procedure returns only one row per session.

The `status` column indicates if a session is sleeping or actively requesting. If the status is *sleeping*, all the metrics for `sp_WhoIsActive` will return **session-level** information. Otherwise, the procedure will return **request-level** information.

The procedure is called simply by running:

```sql
EXEC sp_WhoIsActive;
```

The `@help` parameter can be used to return the list of available paramters and output columns:

```sql
EXEC sp_WhoIsActive
    @help = 1;
```

## Filtering Options

It is possible to view all sessions by using the parameters:

```sql
EXEC sp_WhoIsActive 
    @show_sleeping_spids = 2, 
    @show_system_spids = 1, 
    @show_own_spid = 1;
```

Moreover, the procedure is capable of filtering using the parameters `@filter_type` and `@filter` or `@not_filter_type` and `@not_filter`, e.g.:

```sql
EXEC sp_WhoIsActive
    @filter_type = 'login',
    @filter = 'user';
```

## General Output

### Default Columns

The default output columns will list information about:

- Time and Status
- Identifiers
- Things Slowing Down Your Query
- Things Your Session Is Doing

A few of the columns related to *Things Your Session Is Doing* is described below:

- The `tempdb_allocations` column reports how many 8 KB pages has been allocated in tempdb due to temporary tables, LOB types etc.
- The `tempdb_current` column reports the allocated 8 KB pages substracting the deallocated pages
- The `used_memory` column reports memory used based on 8 KB pages

More output can be added using the various parameters described in [[#dd]].

### Configuring the Output

The output can be configured by using the three following parameters: `@output_column_list`, `sort_order`, and `format_output`.

The `@output_column_list` parameter can be used to control which columns will be shown and their order. The list should be bracked-delimited column names (or partial names with wild cards).

The `@sort_order` parameter can be used to sort the output by specific columns. Again, the list should be bracked-delimited column names (wild cards are not accepted) and, optionally, the keywords `ASC` or `DESC`.

The `@format_output` parameter can control the formatting of the output. The value 1 will format grid results using a non-fixed width font (the SSMS default), 2 will format grid results using a fixed-width font, and 0 will disable formatting completely.

### External Output

The procedure can write the results to a table.

First, you need to create a fitting table, e.g. `tempdb.dbo.monitoring_output`, that can contain the results:

```sql
DECLARE @s VARCHAR(MAX);

EXEC sp_WhoIsActive    
    @return_schema = 1,    
    @schema = @s OUTPUT;

SET @s = REPLACE(@s, '<table_name>', 'tempdb.dbo.monitoring_output');

EXEC(@s);
```

Afterwards, the procedure can write to the destination table using the following set of parameters:

```sql
EXEC sp_WhoIsActive    
    @format_output = 0,   
    @destination_table = 'tempdb.dbo.quick_debug';
```

## Specific Output

### Current Statement Or Entire Batch

The procedure returns, by default, the SQL text of the **currently running statement** in the `sql_text` column. By using the parameter `@get_full_inner_text`, it's possbile to retrieve the SQL text of the **entire batch**:

```sql
EXEC sp_WhoIsActive
    @get_full_inner_text = 1;
```

When running nested modules, it is possible to get the outermost scope by using the parameter `get_outer_command`:

```sql
EXEC sp_WhoIsActive
    @get_outer_command = 1;
```

### Query Plan

To retrieve the **query plan**, use the `get_plans` parameter. Setting the parameter to 1 will get the plan for the currently running statement while 2 will get the plan for all queries in the batch:

```sql
EXEC sp_WhoIsActive
     @get_plans = 1;
```

### Transactions

The `open_tran_count` column reflects the number of nested transactions that have been started. To get more information about transactions, specifically transaction *writes*, the following parameter can be used:

```sql
EXEC sp_WhoIsActive
    @get_transaction_info = 1;
```

### Wait Stats

**By defualt the procedure returns, at most, information about one wait per request.** It is the most important wait according to the following logic:

- All of the waits are evaluated and prioritized
- *CXPACKET* waits - the parallelism waits - are discarded
- Blocking waits get top priority
- Propriety is given by I waits that have been outstanding for the longest time
- Remaining ties are broken by ordering by blocking session ID

In order to see information about all of the waits that are pending on behalf of a request, use the following parameter:

```sql
EXEC sp_WhoIsActive
    @get_task_info = 2;
```

### Is This Normal?

If you're in doubt whether a request takes too long time, it's possible to get the average run time (if any cached statistics for prior runs exist, of course) to help determine this:

```sql
EXEC sp_WhoIsActive
    @get_avg_time = 1;
```

### Additional information

To get additional information, e.g. to troubleshoot edge cases, the following parameter can be used:

```sql
EXEC sp_WhoIsActive
    @get_additional_info = 1;
```

### Blocking

**When there is blocking, there is locking.**

In order to resolve why a blocking situtation happens, using the following parameters will provide a helpful output:

```sql
EXEC sp_WhoIsActive
    @get_task_info = 2,    
    @get_additional_info = 1;
```

Blocking means that your data is being protected. **When you see blocking, the correct move is not to eliminate it, but rather to *evaluate* it**.

To get lock information, use the following parameter:

```sql
EXEC sp_WhoIsActive
    @get_locks = 1;
```

(Beware, the `@get_locks` parameter can seriously slow down your query).

Sometimes, *blocking chains* build up. The blocking chain is essentially organized in to a hierarchy, and it's important to be able to locate the top-level blocker, the *block leader*. The block leader will have the highest number of downstream blockees.

The procedure can output a `blocked_session_count` column by using the following parameter: 

```sql
EXEC sp_WhoIsActive
    @find_block_leaders = 1
    @sort_order = '[blocked_session_count] DESC';
```

### Delta Mode

Most metrics in the procedure are *cumulative* but you can request *delta* metrics by using the following parameter:

```sql
EXEC sp_WhoIsActive
	@delta_interval = 5
```

This mode will take two snapshots, one at the start of the specified interval (seconds) and one at the end, and compare the metrics.

The delta numbers will be outputted in columns postfixed with `_delta`.

Leveraged together with the `@sort_order` column, this is a powerful parameter.

## Read More

For more information, read the [documentation](http://whoisactive.com/docs/).

# Backup and Restore

## Backup

To create a full backup of a database (e.g. `myDb`) on SQL Server, the following command can be used:

```sql
BACKUP DATABASE myDb
TO DISK = N'location\myDb.bak';
```

The log can be backed up with the following command:

```sql
BACKUP LOG myDb
TO DISK = N'location\myDb.log';
```

Options can be added by adding `WITH` at the end of the statement and separating options using comma as delimiter. Some of the especially useful options are:

- `DIFFERENTIAL`: Creates a partial backup of a database
- `CHECKSUM`: Verifies that no corruption has happened to any of the pages during read/write
- `INIT`: Creates a new backup (by default, backups will be appended to a file)
- `COPY_ONLY`: Creates a backup that is independent of the sequence of conventional backups. This is useful if a backup is needed for a special purpose, e.g. a test environment

## Restore

The restore process:

1. Full backup
2. Differential backup, e.g. the latest
3. Log backups from after the differential backup (restored in order)

Get an overview of a stacked file using the following command:

```sql
RESTORE FILELISTONLY
FROM DISK = N'location\myDb.bak';
```

Further details can be found using:

```sql
RESTORE HEADERONLY
FROM DISK = N'location\myDb.bak';
```

When restoring a full backup (with file names matching the `.mdf` and `.ldf` files of the destination database), use:

```sql
RESTORE DATABASE myDb
FROM DISK = N'location\myDb.bak'
WITH REPLACE;
````

The `REPLACE` option is only used when restoring a full backup and not e.g. a partial backup or a log backup.

**The `NORECOVERY` option should be used until the database has been rolled forward using the differential database backup and log backups.**

Restore transaction log backups using:

```sql
RESTORE LOG myDb
FROM DISK = N'location\myDb.log';
```

**If a file has been stacked, individual backups can be selected using the `FILE` option entering the position of the desired file.**

When the database has been rolled forward to the desired point in time, the restore process can be completed:

```sql
RESTORE DATABASE myDb
WITH RECOVERY;
```

## Source

See [Grant Fritchey's video](https://www.youtube.com/watch?v=ITXPTviK2dQ) or read Microsoft's documentation on [backup](https://docs.microsoft.com/en-us/sql/t-sql/statements/backup-transact-sql?view=sql-server-ver16) and [restore](https://docs.microsoft.com/en-us/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver16) statements for more information.

For a ready-made, good and free maintenance solution, see [Ola Hallengren's repository](https://github.com/olahallengren/sql-server-maintenance-solution).

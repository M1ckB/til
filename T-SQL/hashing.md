# Hashing

Hashing is any function that maps an arbitrary sized input to fixed-size values.

Ideally, a hash function should have the following properties:

- Be sensitive to changes in input, i.e. different inputs should produce different outputs
- Be irreversible, i.e. hashing should be a one-way function
- Be efficient

Different hash functions are designed and optimized for different purposes and will therefore balance the properties mentioned above differently.

SQL Server has two built-in functions for hashing, `CHECKSUM` (or `BINARY_CHECKSUM`) and `HASHBYTES`.

The `CHECKSUM` function computes a checksum value over a table row or expression, e.g.:

```sql
SELECT
    CHECKSUM(*) AS chksum
FROM dbo.table
```

The function accepts most datatypes as its input and returns an integer.

The function is fast but there is a small chance that the checksum will not change even though one of the values in the expression list changes. Therefore, the function should be used with care, and Microsoft recommends not using it to detect changes unless occasional missed changes can be tolerated.

The `HASHBYTES` function computes a hash value over an expression using a speicified algorithm, e.g.

```sql
SELECT
    HASHBYTES('SHA2_256', col) AS hsh
FROM dbo.table
```

Either *SHA2_256* or *SHA2_512* can be used as algorithm. The function is not as fast as `CHECKSUM` but will be more sensitive to changes and therefore better to use if precision is important.

The function will accept only varchar, nvarchar or varbinary as its input. Therefore, all columns must be converted to character strings and be concatenated in order to hash a table row. The output of the function is varbinary and will take up either 32 or 64 bytes depending on the algorithm.

See Microsoft's documentation on the [CHECKSUM](https://docs.microsoft.com/en-us/sql/t-sql/functions/checksum-transact-sql?view=sql-server-ver16) and [HASHBYTES](https://docs.microsoft.com/en-us/sql/t-sql/functions/hashbytes-transact-sql?view=sql-server-ver16) functions for more information.

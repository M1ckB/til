# Error Handling

Error handling is necessary in order to manage *unanticipated* errors. We need to make sure that execution is aborted and that the user is alerted about the error.

In database systems error handling is also *transaction handling* since we want one or more database modifications to be atomic, i.e. either all or none of the modifications should be comitted. In SQL Server the start and end of a transaction is marked using `BEGIN TRANSACTION` and `COMMIT TRANSACTION`.

In SQL Server error handling in a stored procedure is done by using a TRY-CATCH block. If an error occurs in the TRY block, the execution will be transferred to the CATCH block:

```sql
BEGIN TRY
    <database modification(s)>
END TRY
BEGIN CATCH
    <error handling>
END CATCH
```

Error handling should always perform three actions:

 1. Roll back open transactions
 2. Reraise an error
 3. Make sure that a non-zero value is returned

Summing up the aforementioned points, a simple pattern for error handling in a stored procedure is shown below:

```sql
CREATE PROCEDURE errorHandlingPattern AS 
SET XACT_ABORT, NOCOUNT ON
BEGIN TRY
    BEGIN TRANSACTION
    <database modification(s)>
    COMMIT TRANSACTION 
END TRY
BEGIN CATCH
    IF @@trancount > 0 ROLLBACK TRANSACTION
    DECLARE @errorMessage nvarchar(2048) = error_message()  
    RAISERROR (@errorMessage, 16, 1)
    RETURN 9
END CATCH
GO
```

I learned about all this by reading [Erland Sommarskog's excellent article series](https://www.sommarskog.se/error_handling/Part1.html). In these articles more patterns and details are provided.

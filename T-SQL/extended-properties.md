# Extended Properties

In SQL Server it is possible to document database objects using a feature called *extended properties*. This features is used by multiple third party tools.

Extended properties are metadata associated with an object. An object can have multiple extended properties. They will all be backed up and restored along with the database holding the objects.

Generally, extended properties have a name, a value and some level parameters pointing to a specific object. While the user can choose the name of the property, commonly used names exists for describing an object ("MS_Description") and versioning ("Version" and "VersionDate").

All extended properties can be accessed via the catalog view `sys.extended_properties`. They can also be accessed via the built-in table-valued function `sys.fn_listextendedproperty` which will list extended properties based on filter criteria.

Too add, update or drop an extended property, the following stored procedures can be used:

- `sys.sp_addextendedproperty`
- `sys.sp_updateextendedproperty`
- `sys.sp_dropextendedproperty`

E.g. adding a property describing a table, `dbo.myTable`, could be done using the following command:

```sql
EXECUTE sp_addextendedproperty  
    @name = N'MS_Description',
    @value = N'This is an extended property describing the table',
    @level0type = N'Schema', @level0name = 'dbo',
    @level1type = N'Table',  @level1name = 'myTable';
```

This articles was written with inspiration from [SQLShack's article](https://www.sqlshack.com/how-to-document-sql-server-database-objects/).

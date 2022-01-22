---
title: How to use bulk copy program (bcp) utility in SQL Server?
date: 2017-09-14
author: nhvu1988
category: DevOps
tags: [sql-server]
---

The bulk copy program utility (bcp) bulk copies data between an instance of Microsoft SQL Server and a data file in a user-specified format[...](https://docs.microsoft.com/en-us/sql/tools/bcp-utility)

Of course, I don't rewrite the whole article about bcp Utility but I just want to record all my understandings about it as very simple examples after one week working on.

All commands run on [powershell](https://msdn.microsoft.com/powershell).

## Generate CSV data file

Using `bcp` to generate a CSV file of any SQL data table is very simple like that

```powershell
bcp "TableName" out "CSVFilePath.csv" -c -T
```

- `-T` argument is using when we connect to SQL Server with a trusted connection using integrated security.

Otherwise, `-U` and `-P` are required.

```powershell
bcp "TableName" out "CSVFilePath.csv" -c -U "username" -P "password"
```

- `-S` is specify the server name when we connect to a remote SQL server
- `-d` is the database name in case the SQL user only has permission on specific database

```powershell
bcp "TableName" out "CSVFilePath.csv" -c -S "server" -U "username" -P "password" -d "database"
```

We can use `-t` argument to specific field terminator (here is ; charactor, and the default is `\t` -tab- character)

```powershell
bcp "TableName" out "CSVFilePath.csv" -c -T -t ";"
```

If we want to use the query to customize data return, just replace `out` by `queryout`

```powershell
bcp "SELECT * FROM TableName" queryout "CSVFilePath.csv" -c -T -t ";"
```

## Generate Native data-type file for exporting and importing

Basically the ``-n`` or ``-N`` argument is using for exporting data from this table and import to another table on another database. It will generate data file with the same data type and native value of data.

First, we should generate the format file using ``format nul`` and ``-f`` argument

```powershell
bcp "TableName" format nul -f "TableName.fmt" -n -T
```

And generate to data file

```powershell
bcp "TableName" out "TableName.bcp" -n -T
```

And use this command for importing

```powershell
bcp "ImportTableName" in "TableName.bcp" -f "TableName.fmt" -T
```

## Other arguments

`-w` uses to be replaced for `-c` if table has any field contains Unicode character.

`-x` only use for generating format file, and bcp will generate format file in XML format (default is non-XML format).

```powershell
bcp "TableName" format nul -f "TableName.xml" -n -x -T
```

This is example of generated `.fmt` file

```powershell
13.0
3
1       SQLINT        0       4       ""   1     CodeType             ""
2       SQLNCHAR      2       510     ""   2     Description          Danish_Norwegian_CI_AI
3       SQLCHAR       2       255     ""   3     ValidationPattern    Danish_Norwegian_CI_AI
```

And this is example of generated `.xml` file

```xml
<?xml version="1.0"?>
<BCPFORMAT xmlns="http://schemas.microsoft.com/sqlserver/2004/bulkload/format" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <RECORD>
        <FIELD ID="1" xsi:type="NativeFixed" LENGTH="4"/>
        <FIELD ID="2" xsi:type="NCharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="510" COLLATION="Danish_Norwegian_CI_AI"/>
        <FIELD ID="3" xsi:type="CharPrefix" PREFIX_LENGTH="2" MAX_LENGTH="255" COLLATION="Danish_Norwegian_CI_AI"/>
    </RECORD>
    <ROW>
        <COLUMN SOURCE="1" NAME="CodeType" xsi:type="SQLINT"/>
        <COLUMN SOURCE="2" NAME="Description" xsi:type="SQLNVARCHAR"/>
        <COLUMN SOURCE="3" NAME="ValidationPattern" xsi:type="SQLVARYCHAR"/>
    </ROW>
</BCPFORMAT>
```

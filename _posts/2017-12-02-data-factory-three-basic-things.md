---
title: Azure Data Factory - Three basic things
date: 2017-12-02
author: nhvu1988
category: DevOps
tags: [azure,data-factory]
---

> (Updated) 2020-05-03 - Data Factory V2 was released, see changes [here](https://docs.microsoft.com/en-us/azure/data-factory/compare-versions).

[Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-introduction) (DF) is an Azure service helps to copy and transform data from data source to another data source, and some use cases that we can use Azure Data Factory Azure are.

1. Restore data from a production database to development database every day.
2. Transfer data from on-premises SQL server database to Azure SQL server database.
3. Backup data from production SQL server database to CSV files every night at 1 AM.

There are many use cases that we can use Data Factory, and I will introduce how to work with DF for the first use case that I had been working on it last week.

## Three basic things

To use Data Factory, we have to prepare 3 basic things that Data Factory needs to work.

* Linked services
* Datasets
* Pipelines

All the configuration's information wiil be stored in `json` format.

### 1. Linked services

`Linked services` are much like connection strings, which define the information that's needed for Data Factory to connect to external resources. For example, the `connectionString` in below JSON string describes the connection of a SQL server database.

```json
{
    "name": "ProdSQLLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString":
                "Data Source=production.database.windows.net;Initial Catalog=ProdDB;Integrated Security=False;User ID=username;Password=password;"
        }
    }
}
```

So in this use case, we will have 2 `linked services`, one is for the production database, and one is for the develop database.

1. ProdSQLLinkedService.json
2. DevSQLLinkedService.json

### 2. Datasets

`Datasets` represent data structures within the data stores, such as the table structure we want to restore from.

```json
{
    "name": "Input_ProdUsersTable",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "ProdSQLLinkedService",
        "structure": [
            {
                "name": "UserId",
                "type": "Guid"
            },
            {
                "name": "Username",
                "type": "String"
            },
            {
                "name": "DateOfBirth",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "tableName": "Users"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

So in this use case, we also will have 2 `datasets`, one is for the table in production database, and one is for the table in development database.

* Input_ProdUsersTable.json
* Output_DevUsersTable.json

### 3. Pipelines

A pipeline contains one or more `activities` that together perform a task. An activity could be a copy data action, a data transform action or a custom action that could do anything we want to process on our data.

So in my use case, I will put a copy activity to the pipeline.

```json
{
    "name": "CopyPipeline",
    "properties":
    {
        "description": "Copy data in table User from Prod database to Dev database for checking",
        "activities": [
            {
                "name": "CopyFromSQLToSQL",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "Input_ProdUsersTable"
                    }
                ],
                "outputs": [
                    {
                        "name": "Output_DevUsersTable"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "SqlSink",
                        "writeBatchSize": 10000,
                        "writeBatchTimeout": "60:00:00"
                    }
                },
                "policy": {
                    "concurrency": 1,
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2099-12-31T00:00:00Z"
    }
}
```

Finally, we will have 2 linked services, 2 datasets and 1 pipeline with a copy activity inside.

![Data Factory - Three basic things](/assets/img/posts/20171202-datafactory-3-basic-things.png)

On the [next blog](/posts/datafactory-how-to-deploy-on-azure-portal/), I will continue with how to deploy them to Data Factory through [Azure portal](https://portal.azure.com).

## Summary

The Azure Data Factory supports many ways to do with the data, but three things above always need to understand at the first step when you walk into this world 😊

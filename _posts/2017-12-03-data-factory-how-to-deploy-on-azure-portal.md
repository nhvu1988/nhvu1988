---
title: Azure Data Factory - How to deploy on Azure portal
date: 2017-12-03
author: nhvu1988
category: DevOps
tags: [azure,data-factory]
---

In the previous blog, we have prepared [3 basic things](/posts/data-factory-three-basic-things/) that enough for Data Factory can work on. Now I will continue with deploying them to Data Factory through Azure portal.

![](/assets/img/posts/20171202-datafactory-3-basic-things.png)

## Create Data Factory

After logged in to Azure portal, just following steps to create new Data Factory

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-1.png)

In the create popup, fill in the information and click to `Create`

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-2.png)

And we have it created.

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-3.png)

Click to the action `Author and deploy`` to go to deployment editor with nothing here yet.

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-4.png)

## Deploy Data Factory Resources

To create a Linked service, click to `New data store` and select a type of service. In this case, I will choose `Azure SQL Database`

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-5.png)

Now, the editor will show the template for `AzureSqlLinkedService`, but we have it already, just copy the prepared json in `ProdSQLLinkedService.json` file to editor and click `Deploy`

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-6.png)

When it's deployed successfully, we will see it in `Linked services` list

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-7.png)

Just following 3 steps to deploy other resources

1. Select correct type of resource, click to `... More` to select dataset or pipeline
2. Copy the prepared json to editor
3. Click to `Deploy` and check the resource on resources list

After deployed everything, we will see something like this

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-8.png)

## Data Factory Resource Explorer

Click to `Monitor & Manage` at Data factory information screen

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-9.png)

Now we are in the Data Factory's resource explorer to verify our task is set up correctly, and check the running activity status as well.

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-10.png)

To rerun an failed activity or just for debug if you have a change on the resources, use `Rerun` button on an activity.

![](/assets/img/posts/20171203-datafactory-how-to-deploy-on-azure-portal-11.png)

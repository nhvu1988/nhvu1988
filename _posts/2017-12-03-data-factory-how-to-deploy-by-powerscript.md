---
title: Azure Data Factory - How to deploy by powershell
date: 2017-12-03
author: nhvu1988
category: DevOps
tags: [azure,data-factory]
---

In this blog, I will introduce how to create an automation script that can deploy the Data Factory's resources to Azure with one keypress 🆒. So, I will reuse the resources on [Data Factory - 3 basic things](/posts/data-factory-three-basic-things/) post for demonstration.

All steps that script will run are

1. Read config.json file to gather configuration information.
2. Login user with Azure account.
3. Setup datafactory.
4. Install Linked services.
5. Install Datasets.
6. Install Pipelines.

## Setup Config File

Since powershell script can load a json file quickly so we create a config.json file like this

```json
{
    "location": "EastUS",
    "resourceGroup": "InDark",
    "subscriptionName": "Developer Program Benefit", 
    "dataFactoryName": "indarkdatafactory",
    "dataFactoryLinkedServices": [
        "./ProdSQLLinkedService.json",
        "./DevSQLLinkedService.json",
    ],
    "dataFactoryDatasets": [
        "./Input_ProdUsersTable.json",
        "./Output_DevUsersTable.json",
    ],
    "dataPipelines": [
        "./CopyPipeline.json"
    ],
}
```

The `resourceGroup` and `subscriptionName` both are information requires to fill while datafactory is installing.

Script to read config file on powershell

```powershell
$config = get-content config.json | ConvertFrom-Json;
$location = $config.location;
$subscriptionName = $config.subscriptionName;
$resourceGroupName = $config.resourceGroup;
$dataFactoryName = $config.dataFactoryName;
```

## Authentication

In the first step, script will ask user to login

```powershell
### Authenticate ###
$user = Login-AzureRmAccount;
if ($user -eq $null) { Return; }
```

## Create Data Factory

Script to setup Data Factory

```powershell
### Create data factory ###
$dataFactory = Get-AzureRmDataFactory -ResourceGroupName $resourceGroupName -Name $dataFactoryName -ErrorAction SilentlyContinue;
if ($dataFactory -eq $null) {
    "creating data factory $dataFactoryName...";
    $dataFactory = New-AzureRmDataFactory -ResourceGroupName $resourceGroupName -Name $dataFactoryName -Location $location -Force;
    "data factory $dataFactoryName has been created successfully.";
    if ($dataFactory -eq $null)
    {
        Write-Error "Cannot create data factory $dataFactoryName";
        Stop-Transcript;
        return;
    }
}
else {
    "data factory $dataFactoryName found";
}
```

## Install Linked Services

Script to install Linked services

```powershell
$linkedServiceFiles = $config.dataFactoryLinkedServices;
foreach($fileName in $linkedServiceFiles) {
    "installing linked services $fileName...";
    $linkedService = New-AzureRmDataFactoryLinkedService -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -File $fileName -Force -ErrorAction SilentlyContinue;
    if ($linkedService -eq $null)
    {
        Write-Error "Cannot create linked service $fileName";
        Stop-Transcript;
        return; 
    }
}
```

## Install Datasets

Script to install Datasets

```powershell
$datasetFiles = $config.dataFactoryDatasets;
foreach($fileName in $datasetFiles) {
    "installing dataset $fileName...";
    $dataset = New-AzureRmDataFactoryDataset -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -File $fileName -Force -ErrorAction SilentlyContinue;
    if ($dataset -eq $null)
    {
        Write-Error "Cannot create dataset $fileName";
        Stop-Transcript;
        return; 
    }
}
```

## Install Pipelines

Script to install Pipelines

```powershell
$pipelineFiles = $config.dataPipelines;
foreach($fileName in $pipelineFiles) {
    "installing pipeline $fileName...";
    $pipeline = New-AzureRmDataFactoryPipeline -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -File $fileName -Force -ErrorAction SilentlyContinue;
    if ($pipeline -eq $null)
    {
        Write-Error "Cannot create pipeline $fileName";
        Stop-Transcript;
        return; 
    }
}
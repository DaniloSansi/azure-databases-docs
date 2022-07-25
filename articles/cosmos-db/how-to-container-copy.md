---
title: Create and manage intra-account container copy jobs in Azure Cosmos DB
description: Learn how to create, monitor, and manage container copy jobs within an Azure Cosmos DB account using CLI commands.
author: nayakshweta
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/18/2022
ms.author: shwetn
---

# Create and manage intra-account container copy jobs in Azure Cosmos DB (Preview)
[!INCLUDE[appliesto-sql-cassandra-api](includes/appliesto-sql-cassandra-api.md)]

[Container copy jobs](intra-account-container-copy.md) help create offline copies of containers within an Azure Cosmos DB account.

This article describes how to create, monitor, and manage intra-account container copy jobs using Azure CLI commands.

## Pre-requisites

* Make sure you have [Azure CLI](/cli/azure/install-azure-cli) downloaded and installed on your machine before trying out container copy.
* Currently, container copy is only supported in [these regions](intra-account-container-copy.md#supported-regions). Make sure your account belongs to one of these regions.


## Install the Cosmos DB preview CLI  extension

This extension contains the container copy commands.

```azurecli-interactive
az extension add --name cosmosdb-preview
```

## Set shell variables

First, set all of the variables that each individual script will use.

```azurecli-interactive
$resourceGroup = "<resource-group-name>"
$accountName = "<cosmos-account-name>"
$jobName = ""
$sourceDatabase = ""
$sourceContainer = ""
$destinationDatabase = ""
$destinationContainer = ""
```

## Create an intra-account container copy job for SQL API account

Create a job to copy a container within an Azure Cosmos DB SQL API account:

```azurecli-interactive
az cosmosdb dts copy `
    --resource-group $resourceGroup ` 
    --account-name $accountName `
    --job-name $jobName `
    --source-sql-container database=$sourceDatabase container=$sourceContainer `
    --dest-sql-container database=$destinationDatabase container=$destinationContainer
```

## Create intra-account container copy job for Cassandra API account

Create a job to copy a container within an Azure Cosmos DB Cassandra API account:

```azurecli-interactive
az cosmosdb dts copy `
    --resource-group $resourceGroup `
    --account-name $accountName `
    --job-name $jobName `
    --source-cassandra-table keyspace=$sourceKeySpace table=$sourceTable `
    --dest-cassandra-table keyspace=$destinationKeySpace table=$destinationTable
```

## Monitor the progress of a container copy job

View the progress and status of a copy job:

```azurecli-interactive
az cosmosdb dts show `
    --resource-group $resourceGroup `
    --account-name $accountName `
    --job-name $jobName
```

## List all the container copy jobs created in an account

To list all the container copy jobs created in an account:

```azurecli-interactive
az cosmosdb dts list `
    --resource-group $resourceGroup `
    --account-name $accountName
```

## Pause a container copy job

In order to pause an ongoing container copy job, you may use the command:

```azurecli-interactive
az cosmosdb dts pause `
    --resource-group $resourceGroup `
    --account-name $accountName `
    --job-name $jobName
```

## Resume a container copy job

In order to resume an ongoing container copy job, you may use the command:

```azurecli-interactive
az cosmosdb dts resume `
    --resource-group $resourceGroup `
    --account-name $accountName `
    --job-name $jobName
```

## Next steps

- For more information about intra-account container copy jobs, see [Container copy jobs](intra-account-container-copy.md).

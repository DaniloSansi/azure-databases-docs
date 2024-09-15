---
ms.service: azure-cosmos-db
ms.subservice: mongodb
ms.topic: include
ms.date: 08/23/2024
---
1. Find the API for MongoDB **connection string** from the list of connection strings for the account with the [``az cosmosdb keys list``](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) command.

    ```azurecli-interactive
    az cosmosdb keys list --type connection-strings \
        --resource-group $resourceGroupName \
        --name $accountName 
    ```

1. Record the *PRIMARY KEY* values. You'll use these credentials later.

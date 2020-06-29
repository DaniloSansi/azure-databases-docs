---
title: Create instance of DMS (Azure Resource Manager template)
description: Learn how to create  Database Migration Service by using Azure Resource Manager template.
services: azure-resource-manager
author: MashaMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/20
---

# Create instance of Azure Database Migration Service (Azure Resource Manager template)

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

## Prerequisites

The Azure Database Migration Service ARM template requires the following: 

- The latest version of the [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) and/or [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7). 
- An Azure subscription. If you don't have one, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Review the template

The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json" range="000-000" highlight="30-71":::

Three Azure resources are defined in the template: 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks):
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets):
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services):

More Azure Database Migration Services templates can be found in the [quickstart template gallery](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration).


## Deploy the template

1. Select the following image to sign in to Azure and open a template. The template creates an instance of the Azure Database Migration Service. 

   [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Select or enter the following values.

    * **Subscription**: select an Azure subscription.
    * **Resource group**: Enter an existing resource group for your Azure Database Migration Service, or choose to create a new resource group by selecting **Create new**. 
    * **Region**: select a region.  For example, **Central US**.
    * **Virtual Machine Name**: enter a name for SQL Server virtual machine. 
    * **Virtual Machine Size**: Choose the appropriate size for your virtual machine from the drop-down.
    * **Existing Virtual Network Name**: The primary replica region for the Azure Cosmos account.
    * **Existing Vnet Resource Group**: The secondary replica region for the Azure Cosmos account.
    * **Existing Subnet Name**: The default consistency level for the Azure Cosmos account.


3. Select **Review + create **. After the instance of Azure Database Migration Service has been deployed successfully, you get a notification. 


The Azure portal is used to deploy the template. In addition to the Azure portal, you can also use the Azure PowerShell, Azure CLI, and REST API. To learn other deployment methods, see [Deploy templates](../azure-resource-manager/templates/deploy-powershell.md).

## Review deployed resources

You can either use the Azure CLI to check deployed resources. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## Clean up resources

When no longer needed, delete the resource group by using Azure CLI or Azure PowerShell:

# [CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# [PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## Next steps

For a step-by-step tutorial that guides you through the process of creating a template, see:

> [!div class="nextstepaction"]
> [ Tutorial: Create and deploy your first Azure Resource Manager template](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

For other ways to deploy Azure Database Migration Service, see: 
- [Azure portal](quickstart-create-data-migration-service-portal.md)

To learn more, see [an overview of Azure Database Migration Service](dms-overview.md)
---
title: Create Azure DB for MySQL server with VNet using ARM template
description: In this article, learn how to create an Azure Database for MySQL server with virtual network integration, by using an Azure Resource Manager template.
services: azure-resource-manager
author: mgblythe
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 04/23/2020
zone_pivot_groups: user-interface-options
---

# Quickstart: Create an Azure Database for MySQL server with VNet by using Azure Resource Manager template

Azure Database for MySQL is a managed service that you use to run, manage, and scale highly available MySQL databases in the cloud. This quickstart shows you how to use a predefined Azure Resource Manager (ARM) template to create an Azure Database for MySQL server with virtual network integration. You can create the server using the Azure portal, Azure CLI, or Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## Prerequisites

::: zone pivot="user-interface-azure-powershell"

* An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/).
* [Azure PowerShell](/powershell/azure/). Alternatively, in a code snippet, you can select **Try it** to open Azure Cloud Shell and run PowerShell code.

::: zone-end

::: zone pivot="user-interface-azure-cli"

* An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/).
* [Azure CLI](/cli/azure/). Alternatively, in a code snippet, you can select **Try it** to open Azure Cloud Shell and run Azure CLI code.

::: zone-end

::: zone pivot="user-interface-azure-portal"

An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/).

::: zone-end

## Create an Azure Database for MySQL server

You create an Azure Database for MySQL server with a defined set of compute and storage resources. To learn more, see [Azure Database for MySQL pricing tiers](concepts-pricing-tiers.md). You create the server within an [Azure resource group](../azure-resource-manager/management/overview.md).

### Review the template

The template used in this quickstart is from [Azure quickstart templates](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json" range="001-231" highlight="147-229":::

Five Azure resources are defined in this template:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servers**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servers/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

More Azure Database for MySQL template samples can be found in [Azure quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## Deploy the template

::: zone pivot="user-interface-azure-cli"

Use the following interactive code to create a new Azure Database for MySQL server using the template. You will be prompted for the new server name, the name and location of a new resource group, and an administrator account name and password.

```azurecli-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure location (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

az group create --name $resourceGroupName --location $location #use this command when you need to create a new resource group for your deployment
az deployment group create --resource-group $resourceGroupName `
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    --parameters 'serverName=' + $serverName `
    --parameters 'administratorLogin=' + $adminUser `
    --parameters 'administratorLoginPassword=' + $adminPassword
```

::: zone-end

::: zone pivot="user-interface-azure-powershell"

Use the following interactive code to create a new Azure Database for MySQL server using the template. You will be prompted for the new server name, the name and location of a new resource group, and an administrator account name and password.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure location (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location #use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

::: zone-end

::: zone pivot="user-interface-azure-portal"

1. Select the following link to deploy the Azure Database for MySQL server template in the Azure portal:

    [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

2. In the **Deploy Azure Database for MySQL with VNet** window, take the following actions:

    1. In **Resource group**, select **Create new**. Then in the new resource group dialog box, enter a name for the new resource group, and select **OK**. Alternatively, you can just select an existing resource group.

    2. If you created a new resource group, select a **Location** for the resource group and the new server.

    3. Enter a **Server Name**, **Administrator Login**, and **Administrator Login Password**.

        ![Deploy Azure Database for MySQL with VNet window, Azure quickstart template, Azure portal](./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png)

    4. If you want, change the other settings. Those settings already have default values entered, so you don't need to do anything unless you want to.

        | Setting | Default value | Description |
        | --- | --- | --- |
        | **Sku Capacity** | *2* | The vCore capacity, which can be *2*, *4*, *8*, *16*, *32*, or *64* |
        | **Sku Name** | *GP_Gen5_2* | The SKU tier prefix, SKU family, and SKU capacity, joined by underscores (for example, *B_Gen5_1*, *GP_Gen5_16*, and *MO_Gen5_32*) |
        | **Sku Size MB** | *5120* | The storage size, in megabytes, of the Azure Database for MySQL server |
        | **Sku Tier** | *GeneralPurpose* | The deployment tier, such as *Basic*, *GeneralPurpose*, or *MemoryOptimized* |
        | **Sku Family** | *Gen5* | *Gen4* or *Gen5*, which indicates hardware generation for server deployment |
        | **Mysql Version** | *5.7* | The version of MySQL server to deploy, such as *5.6* or *5.7* |
        | **Backup Retention Days** | *7* | The desired period for geo-redundant backup retention, in days |
        | **Geo Redundant Backup** | *Disabled* | *Enabled* or *Disabled*, depending on geo-disaster recovery (Geo-DR) requirements |
        | **Virtual Network Name** | *azure_mysql_vnet* | The name of the virtual network |
        | **Subnet Name** | *azure_mysql_subnet* | The name of the subnet |
        | **Virtual Network Rule Name** | *AllowSubnet* | The name of the virtual network rule allowing the subnet |
        | **Vnet Address Prefix** | *10.0.0.0/16* | The address prefix for the virtual network |
        | **Subnet Prefix** | *10.0.0.0/16* | The address prefix for the subnet |

3. Read the terms and conditions, and then select **I agree to the terms and conditions stated above**.

4. Select **Purchase**.

::: zone-end

## Review deployed resources

::: zone pivot="user-interface-azure-cli"

Run the following interactive code to view details about your Azure Database for MySQL server. You'll have to enter the name and the resource group of the new server.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

::: zone-end

::: zone pivot="user-interface-azure-powershell"

Run the following interactive code to view details about your Azure Database for MySQL server. You'll have to enter the name of the new server.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

::: zone-end

::: zone pivot="user-interface-azure-portal"

Follow these steps to see an overview of your new Azure Database for MySQL server:

1. Go to the [Azure portal](https://portal.azure.com) to manage your MySQL databases. Search for and select **Azure Database for MySQL servers**.

2. In the database list, select your new server. The overview page for your new Azure Database for MySQL server appears.

::: zone-end

## Clean up resources

When it's no longer needed, delete the resource group, which deletes the resources in the resource group.

::: zone pivot="user-interface-azure-cli"

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

::: zone-end

::: zone pivot="user-interface-azure-powershell"

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

::: zone-end

::: zone pivot="user-interface-azure-portal"

1. Go to the [Azure portal](https://portal.azure.com) to manage your resource groups. Search for and select **Resource groups**.

2. In the resource group list, choose the name of your resource group.

3. In the overview page of your resource group, select **Delete resource group**.

4. In the confirmation dialog box, type of the name of your resource group, and then select **Delete**.

::: zone-end

## Next steps

For a step-by-step tutorial that guides you through the process of creating a template, see:

> [!div class="nextstepaction"]
> [ Tutorial: Create and deploy your first Azure Resource Manager template](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

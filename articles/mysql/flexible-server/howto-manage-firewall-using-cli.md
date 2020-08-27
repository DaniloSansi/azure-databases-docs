---
title: Manage firewall rules - Azure CLI - Azure Database for MySQL - Flexible Server
description: Create and manage firewall rules for Azure Database for MySQL - Flexible Server using Azure CLI command-line.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020 
ms.custom: devx-track-azurecli
---

# Create and manage Azure Database for MySQL - Flexible Server firewall rules by using the Azure CLI

Azure Database for MySQL Flexible Server supports two type of mutually exclusive network connectivity methods to connect to your flexible server. The two options are:

1. Public access (allowed IP addresses)
2. Private access (VNet Integration)

With *Public access (allowed IP addresses)*, the connections to the MySQL server is restricted to allowed IP addresses only. The client IP addresses needs to be allowed in firewall rules.To learn more about it, refer to [Public access (allowed IP addresses)](./concept-networking-public-access.md). The firewall rules can be defined at the time of server creation (recommended) but can be added later as well.

In this article, we will provide an overview on Azure CLI commands you can use to create firewall rule while creating a server and how you can create, update, delete, list, and show firewall rules to manage your server.

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is currently in public preview

## Launch Azure Cloud Shell

The Azure Cloud Shell is a free interactive shell that you can use to run the steps in this article. It has common Azure tools preinstalled and configured to use with your account.

To open the Cloud Shell, just select **Try it** from the upper right corner of a code block. You can also open Cloud Shell in a separate browser tab by going to [https://shell.azure.com/bash](https://shell.azure.com/bash). Select **Copy** to copy the blocks of code, paste it into the Cloud Shell, and select **Enter** to run it.

If you prefer to install and use the CLI locally, this quickstart requires Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI]( /cli/azure/install-azure-cli).

## Prerequisites

You'll need to log in to your account using the [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) command. Note the **id** property, which refers to **Subscription ID** for your Azure account.

```azurecli-interactive
az login
```

Select the specific subscription under your account using [az account set](/cli/azure/account) command. Make a note of the **id** value from the **az login** output to use as the value for **subscription** argument in the command. If you have multiple subscriptions, choose the appropriate subscription in which the resource should be billed. To get all your subscription, use [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## Create firewall rule during flexible server create using Azure CLI

You can use the `az mysql flexible-server --public access` command to create the flexible server with *Public access (allowed IP addresses)* and configure the firewall rules during creation of flexible server. You can use the **--public-access** switch to provide the allowed IP addresses that will be able to connect to the server. You can provide single or range of IP addresses to be included in the allowed list of IPs. IP address sets ranges must be dash separated and does not contain any spaces, as shown in the example below.

1. To create a flexible server with public access and add client IP address to have access to the server
```azurecli-interactive
az mysql flexible-server create --public-access <my_client_ip>
```
2. To create a flexible server with public access and add the range of IP address to have access to this server

```azurecli-interactive
az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
```
3. To create a flexible server with public access and allow applications from Azure IP addresses to connect to your flexible server
```azurecli-interactive
az mysql flexible-server create --public-access 0.0.0.0
```
> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.
>
4. To create a flexible server with public access and with no IP address
```azurecli-interactive
az mysql flexible-server create --public-access
```
>[!Note]
> we do not recommend to create a server without any firewall rules. If you do not add any firewall rules then no client will be able to connect to the server.

## Create and manage firewall rule after server create
The **az mysql flexible-server firewall-rule** command is used from the Azure CLI to create, delete, list, show, and update firewall rules.

Commands:
- **create**: Create an Azure MySQL server firewall rule.
- **delete**: Delete an Azure MySQL server firewall rule.
- **list**: List the Azure MySQL server firewall rules.
- **show**: Show the details of an Azure MySQL server firewall rule.
- **update**: Update an Azure MySQL server firewall rule.

### List firewall rules on Azure Database for MySQL Flexible Server 
Using the flexible server name list the existing server firewall rules on the server. Use the `az mysql flexible-server firewall-rule list` command. Notice that the server name attribute is specified in the **--server-name** switch and not in the **--name** switch. 
```azurecli-interactive
az mysql server firewall-rule list --server-name mydemoserver
```
The output lists the rules, if any, in JSON format (by default). You can use the **--output table** switch to output the results in a more readable table format.
```azurecli-interactive
az mysql server firewall-rule list --server-name mydemoserver --output table
```
### Create a firewall rule on Azure Database for MySQL Flexible Server
Using the flexible server name create a new firewall rule on the server. Use the `az mysql flexible-server firewall-rule create` command.
To allow access to a range of IP addresses, provide the IP address as the Start IP address and End IP address, as in this example.
```azurecli-interactive
az mysql flexible-server firewall-rule create --server-name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

To allow access for a single IP address, just provide single IP address, as in this example.
```azurecli-interactive
az mysql flexible-server firewall-rule create --server-name mydemoserver --start-ip-address 1.1.1.1
```

To allow applications from Azure IP addresses to connect to your flexible server, provide the IP address 0.0.0.0 as the Start IP, as in this example.
```azurecli-interactive
az mysql server firewall-rule create --server-name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.
> 

Upon success, each create command output lists the details of the firewall rule you have created, in JSON format (by default). If there is a failure, the output shows error message text instead.

### Update a firewall rule on Azure Database for MySQL Flexible Server 
Using the flexible server name update an existing firewall rule on the server. Use the `az mysql flexible-server firewall update` command. Provide the name of the existing firewall rule as input, as well as the start IP address and end IP address attributes to update.
```azurecli-interactive
az mysql flexible-server firewall-rule update --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Upon success, the command output lists the details of the firewall rule you have updated, in JSON format (by default). If there is a failure, the output shows error message text instead.

> [!NOTE]
> If the firewall rule does not exist, the rule is created by the update command.

### Show firewall rule details on Azure Database for MySQL Flexible Server
Using the flexible server name show the existing firewall rule details from the server. Use the `az mysql flexible-server firewall-rule show` command. Provide the name of the existing firewall rule as input.
```azurecli-interactive
az mysql server firewall-rule show --server-name mydemoserver --name FirewallRule1
```
Upon success, the command output lists the details of the firewall rule you have specified, in JSON format (by default). If there is a failure, the output shows error message text instead.

## Delete a firewall rule on Azure Database for MySQL Server
Using the flexible server name remove an existing firewall rule from the server. Use the `az mysql flexible-server firewall-rule delete` command. Provide the name of the existing firewall rule.
```azurecli-interactive
az mysql server firewall-rule delete --server-name mydemoserver --name FirewallRule1
```
Upon success, there is no output. Upon failure, error message text displays.

## Next steps
- Learn more about [Networking in Azure Database for MySQL Flexible Server](./concepts-networking-overview.md)
- Understand more about [Azure Database for MySQL Flexible Server firewall rules](./concepts-networking-public-access.md).
- [Create and manage Azure Database for MySQL Flexible Server firewall rules using the Azure portal](./howto-manage-firewall-using-portal.md).

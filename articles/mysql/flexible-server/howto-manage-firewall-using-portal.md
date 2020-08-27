---
title: Manage firewall rules - Azure portal - Azure Database for MySQL - Flexible Server
description: Create and manage firewall rules for Azure Database for MySQL - Flexible Server using the Azure portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
---

# Create and manage firewall rules for Azure Database for MySQL - Flexible Server using the Azure portal

Azure Database for MySQL Flexible Server supports two type of mutually exclusive network connectivity methods to connect to your flexible server. The two options are:

1. Public access (allowed IP addresses)
2. Private access (VNet Integration)

With *Public access (allowed IP addresses)*, the connections to the MySQL server is restricted to allowed IP addresses only. The client IP addresses needs to be allowed in firewall rules. To learn more about it, refer to [Public access (allowed IP addresses)](./concept-networking-public-access.md). The firewall rules can be defined at the time of server creation (recommended) but can be added later as well. In this article, we will provide an overview on how to create and manage firewall rules using public access (allowed IP addresses).

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server is currently in public preview

## Create a firewall rule in the Azure portal during flexible server create

1. Follow [use the Azure Portal to create an Azure Database for MySQL Flexible Server](./quickstart-create-flexible-server-portal.md) to create the server.
2. Go to the **Networking** tab to configure how you want to connect to your server.

   On the Networking tab, you can choose how your server will be reachable. Azure Database for MySQL Flexible Server creates a firewall at the server level. It prevents external applications and tools from connecting to the server and any databases on the server, unless you create a rule to open the firewall for specific IP addresses.
3. In the **Connectivity-method**, select *Public access (allowed IP addresses)*. To create the firewall rules, specify the Firewall rule name and single IP address, or a range of addresses. If you want to limit the rule to a single IP address, type the same address in the field for Start IP address and End IP address. Opening the firewall enables administrators, users, and applications to access any database on the MySQL server to which they have valid credentials.
4. Select Review + create to review your flexible server configuration.
5. Select Create to provision the server. Provisioning can take a few minutes.
6. Please note that firewall rules can also be added after server create. Please refer to below section to learn how to configure firewall rules after creating the server.

## Create a firewall rule in the Azure portal after flexible server is created

1. In the [Azure Portal](https://portal.azure.com/), select the Azure Database for MySQL Flexible Server on which you want to add firewall rules.
2. On the flexible server page, under Settings heading, click **Networking** to open the Networking page for flexible server.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-using-portal/1-connection-security.png)-->

3. Click **Add current client IP address** in the firewall rules. This automatically creates a firewall rule with the public IP address of your computer, as perceived by the Azure system.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)-->

4. Verify your IP address before saving the configuration. In some situations, the IP address observed by Azure portal differs from the IP address used when accessing the internet and Azure servers. Therefore, you may need to change the Start IP address and End IP address to make the rule function as expected.

   Use a search engine or other online tool to check your own IP address. For example, search for "what is my IP."

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)-->

5. Add additional address ranges. In the firewall rules for the Azure Database for MySQL Flexible Server, you can specify a single IP address, or a range of addresses. If you want to limit the rule to a single IP address, type the same address in the field for Start IP address and End IP address. Opening the firewall enables administrators, users, and applications to access any database on the MySQL server to which they have valid credentials.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)-->

6. Click **Save** on the toolbar to save this firewall rule. Wait for the confirmation that the update to the firewall rules was successful.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)-->

## Connecting from Azure

You may want to enable your flexible server to connect to resources or applications deployed within Azure. This includes web applications hosted in Azure App Service, running on an Azure VM, or an Azure Data Factory data management gateway, 

When an application within Azure attempts to connect to your server, the firewall verifies that Azure connections are allowed. There are two methods to enable these types of connections:

1. A firewall setting with starting and ending address equal to 0.0.0.0 indicates these connections are allowed.
2. You can select the **Allow public access from any resources deployed within Azure to access this server** option in the portal from the **Networking** pane and hit **Save**.

The resources do not need to be in the same Virtual Network (VNet) or resource group for the firewall rule to enable those connections. If the connection attempt is not allowed, the request does not reach the Azure Database for MySQL Flexible Server.

> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.
>
> We recommend to choose the **Private access (VNet Integration)** to securely access flexible server.
>

## Manage existing firewall rules through the Azure portal

Repeat the following steps to manage the firewall rules.

- To add the current computer, click + **Add current client IP address** in the firewall rules. Click **Save** to save the changes.
- To add additional IP addresses, type in the Rule Name, Start IP Address, and End IP Address. Click **Save** to save the changes.
- To modify an existing rule, click any of the fields in the rule and modify. Click **Save** to save the changes.
- To delete an existing rule, click the ellipsis […] and click **Delete** to remove the rule. Click **Save** to save the changes.

## Next steps

- Similarly, you can script to [Create and manage Azure Database for MySQL firewall rules using Azure CLI](howto-manage-flexible-server-firewall-using-cli.md).
- [Use MySQL Workbench to connect and query data in Azure Database for MySQL Flexible Server](./connect-flexible-server-workbench.md)
- [Use PHP to connect and query data in Azure Database for MySQL Flexible Server](./connect-flexible-server-php.md)

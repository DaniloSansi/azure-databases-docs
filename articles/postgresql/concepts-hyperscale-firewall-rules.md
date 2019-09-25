---
title: Firewall rules in Azure Database for PostgreSQL - Hyperscale (Citus)
description: This article describes firewall rules for Azure Database for PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
---
# Firewall rules in Azure Database for PostgreSQL - Hyperscale (Citus)
Azure Database for PostgreSQL server firewall prevents all access to your database server until you specify which computers have permission. The firewall grants access to the server based on the originating IP address of each request.
To configure your firewall, you create firewall rules that specify ranges of acceptable IP addresses. You can create firewall rules at the server level.

**Firewall rules:** These rules enable clients to access your Hyperscale (Citus) coordinator node, that is, all the databases within the same logical server. Server-level firewall rules can be configured by using the Azure portal. To create server-level firewall rules, you must be the subscription owner or a subscription contributor.

## Firewall overview
All database access to your Azure Database for PostgreSQL server is blocked by the firewall by default. To begin using your server from another computer, you need to specify one or more server-level firewall rules to enable access to your server. Use the firewall rules to specify which IP address ranges from the Internet to allow. Access to the Azure portal website itself is not impacted by the firewall rules.
Connection attempts from the Internet and Azure must first pass through the firewall before they can reach your PostgreSQL Database, as shown in the following diagram:

![Example flow of how the firewall works](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## Connecting from the Internet

A Hyperscale (Citus) server group firewall controls who can connect to the group's coordinator node. The firewall determines access by consulting a configurable list of rules. Each rule is an IP address, or range of addresses, that are allowed in.

Note that when the firewall blocks connections, it can cause application errors. Using the PostgreSQL JDBC driver, for instance, raises an error like this:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException:
> org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## Connecting from Azure
To allow applications from Azure to connect to your Azure Database for PostgreSQL server, Azure connections must be enabled. For example, to host an Azure Web Apps application, or an application that runs in an Azure VM, or to connect from an Azure Data Factory data management gateway. The resources do not need to be in the same Virtual Network (VNet) or Resource Group for the firewall rule to enable those connections. When an application from Azure attempts to connect to your database server, the firewall verifies that Azure connections are allowed. There are a couple of methods to enable these types of connections. A firewall setting with starting and ending address equal to 0.0.0.0 indicates these connections are allowed. Alternatively, you can set the **Allow Azure services and resources to access this server group** option to **Yes** in the portal from the **Networking** pane and hit **save**. If the connection attempt is not allowed, the request does not reach the Azure Database for PostgreSQL server.

> [!IMPORTANT]
> This option configures the firewall to allow all connections from Azure including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.
> 

![Configure Allow access to Azure services in the portal](media/concepts-hyperscale-firewall-rules/2-allow-azure-services.png)

## Troubleshooting the database server firewall
Consider the following points when access to the Microsoft Azure Database for PostgreSQL - Hyperscale (Citus) service does not behave as you expect:

* **Changes to the allow list have not taken effect yet:** There may be as much as a five-minute delay for changes to the Hyperscale (Citus) firewall configuration to take effect.

* **The login is not authorized or an incorrect password was used:** If a login does not have permissions on the Azure Database for PostgreSQL server or the password used is incorrect, the connection to the Azure Database for PostgreSQL server is denied. Creating a firewall setting only provides clients with an opportunity to attempt connecting to your server; each client must still provide the necessary security credentials.

For example, using a JDBC client, the following error may appear.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **Dynamic IP address:** If you have an Internet connection with dynamic IP addressing and you are having trouble getting through the firewall, you could try one of the following solutions:

* Ask your Internet Service Provider (ISP) for the IP address range assigned to your client computers that access the Hyperscale (Citus) coordinator node, and then add the IP address range as a firewall rule.

* Get static IP addressing instead for your client computers, and then add the static IP address as a firewall rule.

## Next steps
For articles on creating server-level and database-level firewall rules, see:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-hyperscale-manage-firewall-using-portal.md)

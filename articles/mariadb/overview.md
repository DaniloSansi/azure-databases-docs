---
title: Overview of the Azure Database for MariaDB relational database service
description: Overview of the Azure Database for MariaDB relational database service.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: overview
ms.date: 09/24/2018
ms.custom: mvc
---

# What is Azure Database for MariaDB?

Azure Database for MariaDB is a relational database service in the Microsoft cloud that's based on the [MariaDB community edition](https://mariadb.org/download/) database engine. This service is in public preview. Azure Database for MariaDB delivers:

- Built-in high availability with no additional cost.
- Predictable performance, using inclusive pay-as-you-go pricing.
- Scale as needed within seconds.
- Secured to protect sensitive data at-rest and in-motion.
- Automatic backups and point-in-time-restore for up to 35 days.
- Enterprise-grade security and compliance.

These capabilities require almost no administration. They're provided at no additional cost. Instead of allocating precious time and resources to managing virtual machines and infrastructure, you can use MariaDB to rapidly develop your app and accelerate your time to market. You can also continue to develop your application by usinig the open-source tools and platform of your choice. Deliver with the speed and efficiency your business demands, all without learning new skills.

Learn about core concepts and features in Azure Database for MariaDB, including performance, scalability, and manageability. See these quickstarts to get started:
- [Create an Azure Database for MariaDB server using Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Create an Azure Database for MariaDB server using Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## Adjust performance and scale within seconds

In preview, the Azure Database for MariaDB service offers several service tiers: Basic, General Purpose, and Memory Optimized. Each tier offers different performance and capabilities to support lightweight to heavyweight database workloads. You can build your first app on a small database for a few dollars a month, and then adjust the scale to meet the needs of your solution. Dynamic scalability enables your database to transparently respond to rapidly changing resource requirements. You only pay for the resources you need, and only when you need them. See [Pricing tiers](concepts-pricing-tiers.md) for details.

## Monitoring and alerting

How do you decide when to dial up and down? You use the built-in performance monitoring and alerting features, combined with the performance ratings based on vCores. Using these tools, you can quickly assess the impact of scaling vCores up or down based on your current or projected performance needs. <!--See [Alerts](howto-alert-on-metric.md) for details.-->

## Keep your app and business running

Azure's industry-leading 99.99% availability SLA (not offered during public preview) is powered by a global network of Microsoft-managed datacenters. It helps keep your app running 24/7. Every Azure Database for MariaDB server helps you gain built-in security, fault tolerance, and data protection that you otherwise would buy or design, build, and manage. With Azure Database for MariaDB, you can use point-in-time restore to recover a server to an earlier state, as far back as 35 days.

## Secure your data

Azure database services have a tradition of data security that Azure Database for MariaDB upholds. Azure Database for MariaDB offers features that limit access, protect data at rest and in motion, and help you monitor activity. Visit the [Azure Trust Center](https://www.microsoft.com/en-us/trustcenter/security) for information about Azure's platform security.

The Azure Database for MariaDB service uses storage encryption for data at rest. Data, including backup data, is encrypted on disk. (The exception is temporary files that are created by the engine when it runs queries.) The service uses AES 256-bit cipher, which is included in Azure storage encryption. The keys are system managed. Storage encryption is always on and can't be disabled.

By default, the Azure Database for MariaDB service is configured to require [SSL connection security](./concepts-ssl-connection-security.md) for data in motion across the network. Enforcing SSL connections between your database server and your client applications helps protect against "man in the middle" attacks by encrypting the data stream between the server and your application. Optionally, you can disable the SSL requirement for connecting to your database service if your client application doesn't support SSL connectivity.

## Contacts

For any questions or suggestions about working with Azure Database for MariaDB, send an email to the Azure Database for MariaDB Team ([@Ask Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com)). This isn't a technical support alias.

In addition, consider the following points of contact as appropriate:
- To contact Azure Support, [file a ticket from the Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- To fix an issue with your account, file a [support request](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in the Azure portal.
- To provide feedback or to request new features, create an entry via [UserVoice](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## Next steps

Now that you've read an introduction to Azure Database for MariaDB and answered the question "What is Azure Database for MariaDB?", you're ready to:
- See the pricing page for cost comparisons and calculators. [Pricing](https://azure.microsoft.com/pricing/details/mariadb/)
- Get started by creating your first server. [Create an Azure Database for MariaDB server using Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->

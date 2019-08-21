---
title: Selecting the right deployment type for your Azure Database for MySQL
description: This article describes what are the consideration that needs to be done before going ahead with Infrastucture-as-a-service (IAAS) or Platform-as-a-service (PaaS) for your Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
---

# Choose the right MySQL Server option in Azure

Learn how each deployment option fits into the Microsoft data platform and get help matching the right option to your business requirements. Whether you prioritize cost savings or minimal administration ahead of everything else, this article can help you decide which approach delivers against the business requirements you care about most.<br/>

In Azure, you can have your MySQl server workloads running in a hosted infrastructure VM (IaaS) or running as a hosted service (PaaS). Within PaaS, you have multiple service tiers which gives you the option to select the best that suits your business needs. The key question that you need to ask when deciding between PaaS or IaaS is do you want to manage your server, apply patches, take backups, or you want to delegate these operations to Azure? Depending on the answer, you have the following options: <br/><br/>
**Azure Database for MySQL** is a fully managed MySQL database engine, based on the stable version of the community edition. This is a relational database-as-a-service (DBaaS) hosted in the Azure cloud that falls into the industry category of Platform-as-a-Service (PaaS). With a managed MySQL on Azure, you can use built-in features and functionality that require extensive configuration when using MySQL (either on-premises or in an Azure virtual machine). When using MySQL as a service you pay-as-you-go with options to scale up or out for greater power with no interruption. Azure Database for MySQL has additional features that are
not available in standalone MySQL server, such as built-in high availability, intelligence, and management. <br/><br/>
**MySQL on Azure VM** falls into the industry category *Infrastructure-as-a-Service (IaaS)* and allows you to run MySQL server inside a virtual machine (VM) in Azure cloud. All recent versions and editions of MySQL can be installed on an IaaS virtual machine. The most significant difference from **Azure Database for MySQL** is that **MySQL on Azure VMs** allow full control over the database engine. You can choose when your database server maintenance/patching will start, to change the recovery model, to pause or start engine when needed. With this additional control comes added responsibility to manage the virtual machines and numerous DBA tasks.

The main differences between these options are listed in the following table:

|            | **Azure Database for MySQL** | **MySQL on Azure VMs**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Offers SLA of 99.99% availability| **SLA (for IaaS in Azure)** <br/>Up to 99.95% availability with 2 or more instance in same Availability Set. <br/>99.9% Single-Instance VM using Premium storage <br/> 99.99% with Availability Zone with 2 or more instance in 2 or more Availability Set.<br/> *Only for [Virtual Machine](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/), not to MySQL Processes. |
| **OS Patching**        | Automatic  | Managed by customers |
|**MySQL Patching**     | Automatic  | Managed by customers |
| **High Availability** | High Availability is maintained by having a separate compute and storage layers. Compute layer runs in a secure container developed by Microsoft SQL Server. Data is stored in remote Azure Blob Storage, which has HA built in. | High Availability is architected, implemented, tested, and maintained by the customer. This may include, Always-On (Failover Clustering or Group replication), Log Shipping, Transactional Replication depending on the version of MySQL engine that is being used. |
| **Zone Redundancy** | Currently not supported. | Azure VMs can be set up to run in different availability zones. For an on-premises solution, customers are expected to create, manage, and maintain their own secondary data center.|
| **Hybrid Scenarios** | [Data-in Replication](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) allows you to synchronize data from an external MySQL server into the Azure Database for MySQL service. The external server can be on-premises, in virtual machines, or a database service hosted by other cloud providers.  <br/> <br/> The [read replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) feature allows you to replicate data from an Azure Database for MySQL server (master) to up to five read-only servers (replicas) within the same Azure region, or across regions. Read-only replicas are asynchronously updated using binlog replication technology.   <br/> <br/> Note - Cross-region read replication is currently in public preview.| Managed by customers <br/>
| **Backup and restore** | Azure Database for MySQL automatically creates [server backups](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) and stores them in user configured locally redundant or geo-redundant storage. Azure Database for MySQL takes full, differential, and transaction log backups.  | Managed by customers |
| **Monitoring database operations** | Customer can [set alerts](https://docs.microsoft.com/azure/mysql/concepts-monitoring) on the database operation and act upon reaching thresholds.   |  Managed by customers |
| **Advanced thread protection** | Intelligent capabilities [Advanced Threat Protection](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) for Azure Database for MySQL detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit server.  | Not provided for the customers.
| **Backups (Disaster Recovery)** | Automated backups that are stored in locally redundant or geo-redundant storage[locally redundant or geo-redundant storage](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). They can also be used to restore your server. <br/>The retention period can be set from 7 days to up to 35 days. Restore can be accomplished done using the Azure portal. <br/> | Fully managed by customer, including scheduling, testing, archiving, storage and retention. An additional option is to use Azure Recovery Services Vault to backup Azure VMs and Databases on VMs (Preview). |
| **Recovery options for backups** | Create a new database based on a backup with the [Point-in-time Recovery](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal#point-in-time-restore) (PITR) option. <br/>Restores can be done from the Azure Portal or by using PowerShell, the Azure CLI, or the REST API. | Managed by customers|
| **Performance Recommendation** | Customers get proactive [performance recommendation](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) based on the usage telemetry that helps in optimizing workloads  | Managed by customers |


## Business motivations for choosing Azure Database for MySQL or MySQL on Azure VMs

There are several factors that can influence your decision to choose PaaS or IaaS to host your MySQL databases:

* **Cost** - Both PaaS and IaaS option include base price that covers underlying infrastructure. However, with IaaS deployments you need to invest additional time and resources to manage your database server's redundancy which adds to the cost, while in PaaS you are getting these administrative features included in the price. IaaS deployments give you the flexibility to shut-down your resources while you are not using them to decrease the cost, while PaaS version is always running unless if you drop and re-create your resources based on needs. <br/>
* **Administration** - PaaS reduces the amount of time that you need to invest to administer the database server. However, it also reduces access to a range of custom administration tasks, scripts and functionalities that you can perform or run. For example, spinning up a read replica for Azure Database for MySQL is just few click with no administration setting on PaaS but the customers on IaaS deployments have to manually manage the entire replication setup with necessary permission and authentication settings, and also ensure its functionality post create. <br/>

* **Service-Level Agreement** - Both IaaS and PaaS provide high, industry standard SLA. PaaS option guarantees 99.99% SLA, while IaaS guarantees 99.95% SLA for infrastructure, meaning that you need to implement additional mechanisms to ensure availability of your databases. In the extreme case, if you want to implement High-availability solution that is matching PaaS, you will need to create additional MySQL server in VM and configure replication scenarios, which doubles the cost of running your database and additional management of your VMs. <br/>

* **Time to move to Azure** – MySQL on Azure VM is the exact match of your environment, so migration from on-premises to Azure VM is not different than moving the databases from one on-premises server to another. Managed(PaaS) instance also enables extremely easy migration; however, there might be some changes that you need to apply before you migrate your databases. Additionally you could use Data Migration Service (DMS) to migrate on to PaaS MySQL.

These factors will be discussed in more details in the following sections.

### Cost

Whether you’re a startup that is strapped for cash, or a team in an established company that operates under tight budget constraints, limited funding is often the primary driver when deciding how to host your databases. In this section, you learn about the billing and licensing basics in Azure with regards to these two relational database options Azure Database for MySQL and MySQL on Azure VMs:

#### Billing and licensing basics

Currently, Azure Database for MySQL is sold as a service and is available in several service tiers with different prices for resources, all of which are billed hourly at a fixed rate based on the service tier and compute size you choose. For the latest information on the current supported service tiers, compute sizes, and storage amounts, see [vCore-based purchasing model](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers).

In addition, you are billed for outgoing Internet traffic at regular [data transfer rates](https://azure.microsoft.com/pricing/details/data-transfers/). You can dynamically adjust service tiers and compute sizes to match your application’s varied throughput needs.

With Azure Database for MySQL, the database software is automatically configured, patched, and upgraded by Microsoft, which reduces your administration costs. In addition, its [built-in backup](https://docs.microsoft.com/azure/mysql/concepts-backup) capabilities help you achieve significant cost savings, especially when you have a large number of databases.

With MySQL on Azure VMs you can use any of the MySQL version and run on the VMs. Any supported MySQL version can be used on the VMs. Regardless of the MySQL version you use, you pay for the VM provisioned and the costs for the specific license type used for MySQL. For more information on bring-your-own licensing, see [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### Calculating the total application cost

When you start using a cloud platform, the cost of running your application includes the cost for new development and ongoing administration costs, plus the public cloud platform service costs.

##### When using Azure Database for MySQL

* Highly minimized administration costs
* Limited development costs for migrated applications (managed instances)
* Azure Database for MySQL service costs
* No hardware purchasing costs

##### When using MySQL on Azure VMs

* Higher administration costs
* Limited to no development costs for migrated applications
* Virtual Machine service costs
* No hardware purchasing costs

For more information on pricing, see the following resources:
* [Azure Database for MySQL pricing](https://azure.microsoft.com/en-us/pricing/details/mysql/)
* [Virtual machine pricing](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

### Administration

For many businesses, the decision to transition to a cloud service is as much about offloading complexity of administration as it is cost. With IaaS and PaaS, Microsoft administers the underlying infrastructure and automatically replicates all data to provide disaster recovery, configures and upgrades the database software, manages load balancing, and does transparent fail-over if there is a server failure within a data center.

* **With Azure Database for MySQL**, you can continue to administer your database, but you no longer need to manage the database engine, the operating system, or the hardware. Examples of items you can continue to administer include databases and logins, index and query tuning, and auditing and security. Additionally, configuring high availability to another data center requires minimal or no configuration and administration.<br/><br/>
* **With MySQL on Azure VMs**, you have full control over the operating system and MySQL server instance configuration. With a VM, it’s up to you to decide when to update/upgrade the operating system and database software and when to install any additional software such as anti-virus. Some automated features are provided to dramatically simplify patching, backup, and high availability. In addition, you can control the size of the VM, the number of disks, and their storage configurations. Azure allows you to change the size of a VM as needed. For information, see Virtual Machine and Cloud Service Sizes for Azure.

### Service Level Agreement (SLA)

For many IT departments, meeting up-time obligations of a Service Level Agreement (SLA) is a top priority. In this section, we look at what SLA applies to each database hosting option.

* For **Azure Database for MySQL**, Microsoft provides an availability SLA of 99.99%. For the latest information, see [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/mysql/v1_0/).<br/><br/>
* For **MySQL on Azure VMs** , Microsoft provides an availability SLA of 99.95% that covers just the Virtual Machine. This SLA does not cover the processes (such as MySQL server) running on the VM and requires that you host at least two VM instances in an availability set. For the latest information, see the [VM SLA](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/). For database high availability (HA) within VMs, you should go through the high availability options available on MySQL database, such as [MySQL Group Replication](https://dev.mysql.com/doc/refman/8.0/en/group-replication.html). Using a supported high availability option doesn't provide an additional SLA, but allows you to achieve >99.99% database availability.

### Time to move to Azure <br/>
* **Azure Database for MySQL** are the right solution for cloud-designed applications when developer productivity and fast time-to-market for new solutions are critical. With programmatic DBA-like functionality, it is perfect for cloud architects and developers as it lowers the need for managing the underlying operating system and database.<br/><br/>
* **MySQL on Azure VMs** is perfect if your existing or new applications require MySQL database or access to features in majority functionalities of MySQL database or Windows/Linux, and you want to avoid the time and expense of acquiring new on-premises hardware. It is also a good fit when you want to migrate existing on-premises applications and databases to Azure as-is - in cases where Azure Database for MySQL instance is not a good fit. Since you do not need to change the presentation, application, and data layers, you save time and budget on re-architecting your existing solution. Instead, you can focus on migrating all your solutions to Azure and in doing some performance optimizations that may be required by the Azure platform.

## Next Steps

* See [Azure Database for MySQL Pricing](https://azure.microsoft.com/pricing/details/mysql/)
* Get started by [creating your first server](https://review.docs.microsoft.com/en-us/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).


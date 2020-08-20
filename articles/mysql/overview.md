---
title: Overview - Azure Database for MySQL
description: Learn about the Azure Database for MySQL service, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
---

# What is Azure Database for MySQL?

Azure Database for MySQL is a relational database service in the Microsoft cloud based on the [MySQL Community Edition](https://www.mysql.com/products/community/) (available under the GPLv2 license) database engine, versions 5.6, 5.7, and 8.0. Azure Database for MySQL delivers:

- Built-in high availability with no additional cost.
- Predictable performance, using inclusive pay-as-you-go pricing.
- Scale as needed within seconds.
- Secured to protect sensitive data at-rest and in-motion.
- Automatic backups and point-in-time-restore for up to 35 days.
- Enterprise-grade security and compliance.

These capabilities require almost no administration and all are provided at no additional cost. They allow you to focus on rapid app development and accelerating your time to market rather than allocating precious time and resources to managing virtual machines and infrastructure. In addition, you can continue to develop your application with the open-source tools and platform of your choice to deliver with the speed and efficiency your business demands, all without having to learn new skills.

![Azure Database for MySQL conceptual diagram](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

## Deployment Models

Azure Database for MySQL powered by the MySQL community edition with two deployment modes :
- Single Server 
- Flexible Server (Public Preview)
  
### Azure Database for MySQL - Single Server

Azure Database for MySQL Single Server is a fully managed database service with minimal requirements for customizations of database. The single server platform is designed to handle most of the database management functions such as patching, backups, high availability, security with minimal user configuration and control. The architecture is optimized for built-in high availability with 99.99% availability on single availability zone. It supports community version of MySQL 5.6, 5.7 and 8.0. The service is generally available today in wide variety of [Azure regions](https://azure.microsoft.com/global-infrastructure/services/). 

Single servers are best suited for cloud native applications designed to handle automated patching without the need for granular control on the patching schedule and custom MySQL configuration settings. 

For detailed overview of single server deployment mode, refer [single server overview](single-server/index.yml).

### Azure Database for MySQL - Flexible Server

Azure Database for MySQL Flexible Server is a fully managed database service designed to provide more granular control and flexibility over database management functions and configuration settings. In general, the service provides more flexibility and customizations based on the user requirements. The flexible server architecture allows users to opt for high availability within single availability zone and across multiple availability zones. The service supports 99.99% availability for zone redundant high availability and 99.9% availability for single availability zone. Flexible servers provides better cost optimization controls with burstable skus for servers and ability to stop/start server, ideal for workloads that do not need full compute capacity continuously. The service currently supports community version of MySQL 5.7 with plans to add newer versions soon. The service is currently in public preview, available today in wide variety of [Azure regions](https://azure.microsoft.com/global-infrastructure/services/).

Flexible servers are best suited for 
- New application development requiring better control and customizations on database environments to optimize based on your needs. 
- Migrating existing applications to managed service platform as it provides seamless experience and control at par with running in your own MySQL environments. 

For detailed overview of single server deployment mode, refer [flexible server overview](flexible-server/index.yml).

## Next Steps

Learn more about the two deployment modes for Azure Database for MySQL and choose the right options based on your needs.

- [Single Server](single-server/index.yml)
- [Flexible Server](flexible-server/index.yml)

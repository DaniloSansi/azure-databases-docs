---
title: Versioning policy - Azure Database for PostgreSQL - Single Server and Flexible Server (Preview)
description: Describes the policy around Postgres major and minor versions in Azure Database for PostgreSQL - Single Server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: fasttrack-edit
---
# Azure Database for PostgreSQL versioning policy

This page describes the Azure Database for PostgreSQL versioning policy. This is applicable to both Single Server and Flexible Server (Preview) deployment modes.

## Supported  PostgreSQL versions

Azure Database for PostgreSQL supports the following database versions.
| Version | Single Server | Flexible Server (Preview) |
| ----- | ------ | ---- |
| PostgreSQL 12 |  | X | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9.6 | X |  |
| PostgreSQL 9.5 | X |  |

## Major version support
Each major version of PostgreSQL will be supported by Azure for PostgreSQL from the date on which Azure begins supporting the version until the version is retired by the PostgreSQL community, as provided in the PostgreSQL community versioning policy.

## Minor version support
Azure for PostgreSQL automatically performs minor version upgrades to the Azure preferred PostgreSQL version as part of periodic maintenance. 

## Major version retirement policy
The table below provides the retirement details for PostgreSQL major versions. The dates follow the PostgreSQL community versioning policy. See Retired PostgreSQL versions section for more details.

| Version	| Azure support start date	| Retirement date|
| ----- | ------ | ----- |
| PostgreSQL 9.5 | April 18, 2018	| February 11, 2021
| PostgreSQL 9.6 | April 18, 2018	| November 11, 2021
| PostgreSQL 10 |	June 4, 2018	| November 10, 2022
| PostgreSQL 11 |	July 24, 2019	| November 9, 2023
| PostgreSQL 12 |	Sept 22, 2020 	| November 14, 2024

## Retired PostgreSQL versions not supported    

After the retirement date for each PostgreSQL database version, if you continue running the retired version, note the following:
- As the community will not be releasing any further bug fixes or security fixes, Azure for PostgreSQL will not patch the retired database engine for any bugs or security issues.
- If any support issue you may experience relates to the PostgreSQL database, we will be unable to provide you with support. In such cases, you will have to upgrade your database in order for us to provide you with any support.
- You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers.
- New platform capabilities developed by Azure for PostgreSQL may only be available to supported database server versions.
- Uptime SLAs will apply solely to Azure for PostgreSQL service-related issues and not to any downtime caused by database engine related bugs.  

## Version syntax
Before PostgreSQL version 10, the [PostgreSQL versioning policy](https://www.postgresql.org/support/versioning/) considered a _major version_ upgrade to be an increase in the first _or_ second number. For example, 9.5 to 9.6 was considered a _major_ version upgrade. As of version 10, only a change in the first number is considered a major version upgrade. For example, 10.0 to 10.1 is a _minor_ release upgrade. Version 10 to 11 is a _major_ version upgrade.

## Next steps
- For more details on Azure Database for PostgreSQL - Single Server, see [supported versions](./concepts-supported-versions.md)
- For more details on Azure Database for PostgreSQL - Flexible Server (Preview), see [supported versions](flexible-server/concepts-supported-versions.md)
- For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).

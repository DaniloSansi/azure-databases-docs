---
title: Advanced Threat Detection - Azure Database for PostgreSQL | Microsoft Docs
description: Advanced Threat Detection detects anomalous database activities indicating potential security threats to the database. 
services: postgresql
author: bolzmj
manager: kfile
ms.service: postgresql
ms.topic: article
ms.date: 08/08/2018
ms.author: mbolz

---
# Azure Database for PostgreSQL Advanced Threat Detection

Azure Database for PostgreSQL Advanced Threat Detection detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

Threat Detection is part of the Advanced Threat Protection (ATP) offering, which is a unified package for advanced security capabilities. Advanced Threat Detection can be accessed and managed via the [Azure portal](https://portal.azure.com) and is currently in preview.

## What is Advanced Threat Detection?

Azure Database for PostgreSQL Advanced Threat Detection provides a new layer of security, which enables customers to detect and respond to potential threats as they occur by providing security alerts on anomalous activities. Users receive an alert upon suspicious database activities, and potential vulnerabilities, as well as anomalous database access and queries patterns. Azure Database for PostgreSQL Advanced Threat Detection integrates alerts with [Azure Security Center](https://azure.microsoft.com/services/security-center/), which includes details of suspicious activity and recommends action on how to investigate and mitigate the threat. Azure Database for PostgreSQL Advanced Threat Detection makes it simple to address potential threats to the database without the need to be a security expert or manage advanced security monitoring systems. 

![Advanced Threat Detection Concept](media/concepts-database-threat-detection/advanced-threat-detection-concept.png)

## Azure SQL Database Threat Detection alerts 
Advanced Threat Detection for Azure Database for PostgreSQL detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases and it can trigger the following alerts:
- **Access from unusual location**: This alert is triggered when there is a change in the access pattern to the Azure Database for PostgreSQL server, where someone has logged on to the Azure Database for PostgreSQL server from an unusual geographical location. In some cases, the alert detects a legitimate action (a new application or developer maintenance). In other cases, the alert detects a malicious action (former employee, external attacker).
- **Access from unusual Azure data center**: This alert is triggered when there is a change in the access pattern to the Azure Database for PostgreSQL server, where someone has logged on to the SQL server from an unusual Azure data center that was seen on this server during the recent period. In some cases, the alert detects a legitimate action (your new application in Azure, Power BI, Azure Azure Database for PostgreSQL Query Editor). In other cases, the alert detects a malicious action from an Azure resource/service (former employee, external attacker).
- **Access from unfamiliar principal**: This alert is triggered when there is a change in the access pattern to the Azure Database for PostgreSQL server, where someone has logged on to the SQL server using an unusual principal (Azure Database for PostgreSQL user). In some cases, the alert detects a legitimate action (new application, developer maintenance). In other cases, the alert detects a malicious action (former employee, external attacker).
- **Access from a potentially harmful application**: This alert is triggered when a potentially harmful application is used to access the database. In some cases, the alert detects penetration testing in action. In other cases, the alert detects an attack using common attack tools.
- **Brute force Azure Database for PostgreSQL credentials**: This alert is triggered when there is an abnormal high number of failed logins with different credentials. In some cases, the alert detects penetration testing in action. In other cases, the alert detects brute force attack.

## Next steps

* Learn more about [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* For more information on pricing, see the [Azure Database for PostgreSQL Pricing page](https://azure.microsoft.com/en-us/pricing/details/postgresql/) 
* Configure [Azure Database for PostgreSQL Advanced Threat Detection](howto-database-threat-detection-using-portal.md) using the Azure portal  

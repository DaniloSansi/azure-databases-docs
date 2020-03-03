---
title: Troubleshoot data encryption on Azure Database for PostgreSQL - single server 
description: Learn how to troubleshoot the data encryption on your Azure Database for PostgreSQL - single server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
---

# Troubleshoot data encryption with customer-managed keys in Azure Database for PostgreSQL - single server

This article describes how to identify and resolve common issues that can occur on an Azure Database for PostgreSQL - single server configured with data encryption using customer-managed key.

## Introduction

When you configure data encryption to use a customer-managed key in Azure Key Vault, servers require continuous access to the key. If the server loses access to the customer-managed key in Azure Key Vault, it will deny all connections, return the appropriate error message, and change its state to ***Inaccessible*** in the Azure portal.

If you no longer need an inaccessible Azure Database for PostgreSQL - single server, you can delete it to stop incurring costs. No other actions on the server are permitted until access to the Azure Key Vault has been restored and the server is available. It's also not possible to change the data encryption option from `Yes`(customer-managed) to `No` (service-managed) on an inaccessible server when it's encrypted with a customer-managed key. You'll have to revalidate the key manually before the server is accessible again. This action is necessary to protect the data from unauthorized access while permissions to the customer-managed key are revoked.

## Common errors causing server to become inaccessible

Most issues that occur when you use data encryption with Azure Key Vault are caused by one of the following misconfigurations:

- The key vault is unavailable or doesn't exist:
  - The key vault was accidentally deleted.
  - An intermittent network error causes the key vault to be unavailable.

- You don't have permissions to access the key vault or the key doesn't exist:
  - The key expired or was accidentally deleted or disabled.
- The Azure Database for PostgreSQL - single server instance-managed identity was accidentally deleted.
  - Permissions granted to the Azure Database for PostgreSQL managed identity for the keys aren't sufficient. For example, the permissions don't include Get, Wrap, and Unwrap.
  - Permissions for the Azure Database for PostgreSQL - single-server instance-managed identity were revoked or deleted.

## Identify and resolve common errors

### Errors on the key vault

#### Disabled key vault

- `AzureKeyVaultKeyDisabledMessage`
- **Explanation**: The operation couldn't be completed on server because the Azure Key Vault key is disabled.

#### Missing key vault permissions

- `AzureKeyVaultMissingPermissionsMessage`
- The server doesn't have the required Get, Wrap, and Unwrap permissions to the Azure Key Vault permissions. Grant any missing permissions to the service principal with ID.

### Mitigation

- Confirm that the customer-managed key is present in Key Vault.
- Identify the key vault, then go to the key vault in the Azure portal.
- Ensure that the key identified by the key URI is present.

## Next steps

[Use the Azure portal to set up data encryption with a customer-managed key on Azure Database for PostgreSQL](howto-data-encryption-portal.md).

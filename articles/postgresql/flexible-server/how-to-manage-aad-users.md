---
title: Create users - Azure Database for PostgreSQL - Flexible Server
description: This article describes how you can manage Azure AD enabled roles to interact with an Azure Database for PostgreSQL - Flexible Server.
ms.service: postgresql
ms.subservice: flexible-server
ms.topic: how-to
ms.author: anchudno
author: anchudno
ms.date: 09/26/2022
---

# Manage Azure AD roles in Azure Database for PostgreSQL - Flexible Server

[!INCLUDE [applies-to-postgresql-Flexible-server](../includes/applies-to-postgresql-Flexible-server.md)]

This article describes how you can create Azure AD enabled database roles within an Azure Database for PostgreSQL server.

> [!NOTE]
> This guide assumes you already enabled Azure Active Directory authentication on your PostgreSQL Flexible server.
> See [How to Configure Azure AD Authentication](./how-to-configure-sign-in-azure-ad-authentication.md)

> [!NOTE]
> Azure Active Directory Authentication for PostgreSQL Flexible Server is currently in preview.

If you would like to learn about how to create and manage Azure subscription users and their privileges, you can visit the [Azure role-based access control (Azure RBAC) article](../../role-based-access-control/built-in-roles.md) or review [how to customize roles](../../role-based-access-control/custom-roles.md).

## Create or Delete Azure AD administrators using Azure portal or ARM API

1. Open **Authentication** page for your Azure Database for PostgreSQL Flexible Server in Azure portal
1. To add an administrator - click **Add Azure AD Admin**  and select a user, group, application or a managed identity from the current AAD tenant.
1. To remove an administrator - click **Delete** icon for the one to remove.
1. Click **Save** and wait for provisioning operation to completed.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-aad-users/add-aad-principal-via-portal.png" alt-text="manage aad administrators via portal":::

> [!NOTE]
> Support for Azure AD Administrators management via Azure SDK, az cli and Azure Powershell is coming soon,

## Manage Azure AD roles using SQL

You can use the administrator role to manage Azure AD roles in your Azure Database for PostgreSQL Flexible server after creating the first Azure AD administrator from the Azure portal or API,

We recommend getting familiar with [Microsoft identity platform](https://learn.microsoft.com/en-us/azure/active-directory/develop/v2-overview) for best use of Azure AD integration with Azure Database for PostgreSQL Flexible Servers.

### Principal Types

Azure Database for PostgreSQL Flexible servers internally stores mapping between PostgreSQL database roles and unique identifiers of AzureAD objects.
Each PostgreSQL database roles can be mapped to one of the following Azure AD object types:

1. **User** - Including Tenant local and guest users.
2. **Service Principal**. Including [Applications and Managed identities](https://learn.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals).
3. **Group**  When a PostgreSQL Role is linked to an Azure AD group, any user or service principal member of this group can connect to the Azure Database for PostgreSQL Flexible Server instance wih the group role.

### Listing Azure AD roles using SQL

```sql
select * from pgaadauth_list_principals(isAdmin);
```

**Parameters:**
- *isAdmin* - Boolean flag if the function should only return Admin users, or all users.

## Create a role using Azure AD principal name

```sql
select * from pgaadauth_create_principal('mary@contoso.com', false, false);
```

**Parameters:**
- *roleName* - Name of the role to be created. This **must match a name of Azure AD principal**:
   - For **users** use User Principal Name from Profile. For guest users the full name would include escaped full name in their home domain and #EXT# tag.
   - For **groups** and **service principals** use display name. The name must be unique in the tenant.
- *isAdmin* - Set to **true** if when creating an admin user and **false** for a regular user. Admin user created this way has the same privileges as one created via Portal or API.
- *isMfa* - Flag if Multi Factor Authentication must be enforced for this role.

## Create a role using Azure AD object identifier

```sql
select * from pgaadauth_create_principal_with_oid('accounting_application', '00000000-0000-0000-0000-000000000000', 'service', false, false);
```

**Parameters:**
- *roleName* - Name of the role to be created.
- *objectId* - Unique object identifier of the Azure AD object:
   - For **Users**, **Groups** and **Managed Identities** the ObjectId can be found by searching for the object name in Azure AD page in Azure Portal.    [See this guide as example](https://learn.microsoft.com/en-us/partner-center/find-ids-and-domain-names)
   - For **Applications**, Objectid of the corresponding **Service Principal** must be used. In Azure Portal t=the required ObjectId can be found on **Enterprise Applications** page.
- *objectType* - Type of the Azure AD object to link to this role.
- *isAdmin* - Set to **true** if when creating an admin user and **false** for a regular user. Admin user created this way has the same privileges as one created via Portal or API.
- *isMfa* - Flag if Multi Factor Authentication must be enforced for this role.

## Enable Azure AD authentication for an existing PostgreSQL role using SQL

Azure Database for PostgreSQL Flexible Servers uses Security Labels associated with database roles to store Azure AD mapping.
During preview we do not provide a function to associate existing Azure AD role.

You can use the following SQL to assign security label:
```sql
SECURITY LABEL for "pgaadauth" on role "<roleName>" is 'aadauth,oid=<objectId>'
```

**Parameters:**
- *roleName* - Name of an existing PostgreSQL role to aenable Azure AD authentication for.
- *objectId* - Unique object identifier of the Azure AD object.


## Next steps

Connect to Azure Database for PostgreSQL Flexible Servers using Azure AD principal.

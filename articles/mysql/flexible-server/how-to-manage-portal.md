---
title: Manage server - Azure portal - Azure Database for MySQL Flexible Server
description: Learn how to manage an Azure Database for MySQL Flexible server from the Azure portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
---

# Manage an Azure Database for MySQL Flexible server (Preview) using Azure portal
This article shows you how to manage your Azure Database for MySQL Flexible servers (Preview). Management tasks include compute and storage scaling, rest server administrator password and delete your server.

## Sign in
Sign in to the [Azure portal](https://portal.azure.com). Go to your flexible server resource in the Azure portal.

### Scale compute and storage

After server creation you can scale between the various [pricing tiers](https://azure.microsoft.com/pricing/details/mysql/) as your needs change. You can also scale up or down your compute and memory by increasing or decreasing vCores.

> [!NOTE]
> Storage cannot be scaled down to lower value.

1. Select your server in the Azure portal. Select **Compute + Storage**, located in the **Settings** section.

2. You can change the **Compute Tier**, **vCore**, **Storage** to scale up the server using higher compute tier or scale up within the same tier by increasing storage or vCores to a value you want.

   ![scaling storage flexible server](./media/howto-manage-server-portal/scale-server.png)

   > [!Important]
   > - Storage cannot be scaled down.
   > - Scaling vCores causes a server restart.

3. Select **OK** to save changes.

## Reset admin password

You can change the administrator role's password using the Azure portal.

1. Select your server in the Azure portal. In the **Overview** window, select **Reset password**.

2. Enter a new password and confirm the password. The textbox will prompt you about password complexity requirements.

   ![reset your password for flexible server](./media/howto-manage-server-portal/reset-password.png)

3. Select **Save** to save the new password.

## Delete a server

You can delete your server if you no longer need it.

1. Select your server in the Azure portal. In the **Overview** window select **Delete**.

2. Type the name of the server into the input box to confirm that you want to delete the server.

    ![delete the flexible server](./media/howto-manage-server-portal/delete-server.png)

   > [!NOTE]
   > Deleting a server is irreversible.

3. Select **Delete**.

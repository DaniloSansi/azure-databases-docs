---
title: Restore - Azure portal - Azure Database for PostgreSQL - Flexible Server
description: This article describes how to perform restore operations in Azure Database for PostgreSQL through the Azure portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/21/2020
---

# Point-in-time restore of a Flexible Server (Preview)

This article provides step-by-step procedure to perform point-in-time recoveries in flexible server using backups.

## Pre-requisites

To complete this how-to guide, you need:

-   You must have an Azure Database for PostgreSQL flexible server. The same procedure is also applicable for flexible server configured with zone redundancy.

## Restoring to the earliest restore point

Follow these steps to restore your flexible server using an earliest
existing backup.

1.  In the [Azure portal](https://portal.azure.com/), choose your flexible server that you want to restore the backup from.

2.  Click **Overview** from the left panel and click **Restore**
   
 ![view restore overview](./media/how-to-restore-server-portal/restore-overview.png)

3.  Restore page will be shown with an option to choose between Earliest restore point and Custom restore point.

4.  Select **Earliest restore point** and provide a new server name in the **Restore to new server** field.
   
 ![Earliest restore time](./media/how-to-restore-server-portal/restore-earliest.png)

5.  Click **OK**.

6.  A notification will be shown that the restore operation has been initiated.

## Restoring to a custom restore point

Follow these steps to restore your flexible server using an earliest
existing backup.

1.  In the [Azure portal](https://portal.azure.com/), choose your flexible server that you want to restore the backup from.

2.  From the overview page, click **Restore**.
    ![view restore overview - earliest backup](./media/how-to-restore-server-portal/restore-overview.png)
3.  Restore page will be shown with an option to choose between Earliest restore point and Custom restore point.

4.  Choose **Custom restore point**.

5.  Select date and time and provide a new server name in the **Restore to new server** field. 
   
 ![view overview - custom restore](./media/how-to-restore-server-portal/restore-custom.png)
 
6.  Click **OK**.

7.  A notification will be shown that the restore operation has been
    initiated.

## Next steps

-   Learn about [business continuity](./concepts-business-continuity.md)
-   Learn about [zone redundant high availability](./concepts-high-availability.md)
-   Learn about [backup and recovery](./concepts-backup-restore.md)

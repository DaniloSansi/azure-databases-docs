---
title: 'Quickstart: Create an Azure DB for MySQL Flexible Server - Azure portal'
description: This article steps you through using the Azure portal to quickly create an Azure Database for MySQL Flexible Server in several minutes. 
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 8/20/2020
---

# Quickstart: Use the Azure portal to create an Azure Database for MySQL Flexible Server

Azure Database for MySQL Flexible Server is a managed service that you use to run, manage, and scale highly available MySQL servers in the cloud. This Quickstart shows you how to create a flexible server in several using the Azure portal.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server is currently in public preview

If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

## Sign in to the Azure portal
Open your web browser, and then go to the [Azure portal](https://portal.azure.com/). Enter your credentials to sign in to the portal. The default view is your service dashboard.

## Create an Azure Database for MySQL Flexible Server

You create a flexible server with a defined set of compute and storage resources <!-- [compute and storage resources](./concepts-compute-and-storage.md)-->. You create the server within an [Azure resource group](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Follow these steps to create a flexible server:

1. Select **Create a resource** (+) in the upper-left corner of the  portal.

1. Select **Databases** > **Azure Database for MySQL**. You can also enter **MySQL** in the search box to find the service.

    <!--
    >[!div class="mx-imgBorder"]
    > ![Azure Database for MySQL option](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png) -->

1. Select **Flexible server** as the deployment option.
    <!--    
    >[!div class="mx-imgBorder"]
    > ![Create server form](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)
    -->

1. Fill out the **Basics** form with the following information:

    **Setting**|**Suggested Value**|**Description**
    ---|---|---
    Subscription|Your subscription name|The Azure subscription that you want to use for your server. If you have multiple subscriptions, choose the subscription in which you'd like to be billed for the resource.
    Resource group|*myresourcegroup*| A new resource group name or an existing one from your subscription.
    Server name |*mydemoserver*|A unique name that identifies your flexible server. The domain name *mysql.database.azure.com* is appended to the server name you provide. The server can contain only lowercase letters, numbers, and the hyphen (-) character. It must contain at least 3 through 63 characters.
    Admin username |*myadmin*| Your own login account to use when you connect to the server. The admin login name can't be **azure_superuser**, **admin**, **administrator**, **root**, **guest**, or **public**.
    Password |Your password| A new password for the server admin account. It must contain between 8 and 128 characters. Your password must contain characters from three of the following categories: English uppercase letters, English lowercase letters, numbers (0 through 9), and non-alphanumeric characters (!, $, #, %, etc.).
    Region|The region closest to your users| The location that is closest to your users.
    Version|5.7| MySQL major version.
    Compute + storage | **Burstable**, **Standard_B1ms**, **10 GiB**, **7 days** | The compute, storage, and backup configurations for your new server. Select **Configure server**. *Burstable*, *Standard_B1ms*, *10 GiB*, and *7 days* are the default values for **Compute tier**, **vCore**, **Storage**, and **Backup Retention Period**. You can leave those sliders as is or adjust them. To save this compute and storage selection, select **OK**. The next screenshot captures these selections.

1. Configure Networking options

    On the Networking tab, you can choose how your server is reachable. Azure Database for MySQL Flexible Server creates a firewall at the server level. It prevents external applications and tools from connecting to the server and any databases on the server, unless you create a rule to open the firewall for specific IP addresses. We recommend making the server publicly accessible and then restricting it to your own client IP address.
    <!--![The "Networking" pane](./media/quickstart-create-database-portal/5-networking.png) -->

    <!--![Select "Add current client IP address"](./media/quickstart-create-database-portal/6-add-client-ip.png)-->

1. Select **Review + create** to review your flexible server configuration.

1. Select **Create** to provision the server. Provisioning can take a few minutes.

1. Select **Notifications** on the toolbar (the bell icon) to monitor the deployment process. Once the deployment is done, you can select **Pin to dashboard**, which creates a tile for this flexible server on your Azure portal dashboard as a shortcut to the server's **Overview** page. Selecting **Go to resource** opens the server's **Overview** page.

By default, the following databases are created under your server: **information_schema**, **mysql**, **performance_schema**, and **sys**.

> [!NOTE]
> Check if your network allows outbound traffic over port 3306 that is used by Azure Database for MySQL Flexible Server to avoid connectivity issues.  

## Connect to Azure Database for MySQL Flexible Server using mysql command-line client

You can choose either [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) or MySQL Workbench <!-- [MySQL Workbench](./connect-workbench.md)--> to connect to the server from your local environment. In this quickstart, we will run **mysql.exe** in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) to connect to the server.

1. Launch Azure Cloud Shell in the portal by clicking the highlighted icon on the top-left side. Make a note of your server name, server admin login name, password, and subscription for your newly created server from the **Overview** section as shown in the image below.

    > [!NOTE]
    > If you are launching Cloud Shell for the first time, you will see a prompt to create a resource group, storage account. This is a one-time step and will be automatically attached for all sessions.

    <!--
   >[!div class="mx-imgBorder"]
   > ![Portal Full View Cloud Shell](./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png) -->

1. Run this command on Azure Cloud Shell terminal. Replace values with your actual server name and admin user login name.

    ```azurecli-interactive
    mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin -p
    ```

    Here is how the experience looks like in the Cloud Shell terminal
    ```
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    
    Welcome to Azure Cloud Shell
    
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell
    
    user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 64796
    Server version: 5.7.27.0 Source distribution
    
    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    mysql>
    ```

1. In the same Azure Cloud Shell terminal, create a database **guest**
    ```
    mysql> CREATE DATABASE guest;
    Query OK, 1 row affected (0.27 sec)
    ```
1. Change to database **guest**
    ```
    mysql> USE guest;
    Database changed
    ```
1. Type ```quit```, and then select the Enter key to quit mysql.

## Clean up resources
You have successfully created an Azure Database for MySQL Flexible Server in a resource group.  If you don't expect to need these resources in the future, you can delete them by deleting the resource group or just delete the MySQL server. To delete the resource group, follow these steps:

1. In the Azure portal, search for and select **Resource groups**.
1. In the resource group list, choose the name of your resource group.
1. In the Overview page of your resource group, select **Delete resource group**.
1. In the confirmation dialog box, type the name of your resource group, and then select **Delete**.

To delete the server, you can click on **Delete** button on **Overview** page of your server.

<!-- as shown below:
> [!div class="mx-imgBorder"]
> ![Delete your resources](media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)-->

## Next steps
> [!div class="nextstepaction"]
>[Build a PHP app on Windows with MySQL](../../app-service/app-service-web-tutorial-php-mysql.md)
>[Build PHP app on Linux with MySQL](../../app-service/containers/tutorial-php-mysql-app.md)
>[Build Java based Spring App with MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
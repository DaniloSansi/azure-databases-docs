---
title: 'Tutorial: Deploy a web reference ASP.NET Core MVC application with Cosmos DB SQL API and Manged Identity on AKS cluster using Bicep'
description: Learn how to quickly build and deploy a MVC application with Cosmos DB SQL API and Manged Identity on AKS cluster using Bicep.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
author: sandnair
ms.author: sandnair
ms.topic: tutorial
ms.date: 09/15/2022
---

# Tutorial: Deploy a web reference ASP.NET Core MVC application using Cosmos DB SQL API on AKS cluster using Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In this quickstart, you deploy a web reference ASP.NET Core MVC application on Azure Kubernetes Service (AKS) cluster.

 **[Azure Cosmos DB](../introduction.md)**  is a fully managed NoSQL database for modern application development. **[AKS](..//..//aks/intro-kubernetes.md)** is a managed Kubernetes service that lets you quickly deploy and manage clusters.



> [!NOTE]
> - This article requires the latest version of Azure CLI. If using Azure Cloud Shell, the latest version is already installed.
> - If running the commands in this quickstart locally (instead of Azure Cloud Shell), ensure to run the commands as administrator.

## Pre-requisites
The  following are required to compile the ASP.NET Core MVC application and create its container image.
* [Docker Desktop](https://docs.docker.com/desktop/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Docker extension for Visual Studio Code](https://code.visualstudio.com/docs/containers/overview)
* [Azure Account extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)



## Overview

This quickstart uses [Infrastructure as Code](https://docs.microsoft.com/devops/deliver/what-is-infrastructure-as-code) approach to deploy the resources to Azure. We'll use **[Bicep](..//..//azure-resource-manager/bicep/overview.md)**, which is a new declarative language that offers the same capabilities as [ARM templates](..//..//azure-resource-manager/templates/overview.md) but with a syntax that is more concise and easier to use. 

The Bicep modules will deploy the following Azure resources under subscription scope.

1. A [Resource Group](..//..//azure-resource-manager/management/overview.md#resource-groups)
2. A [Managed Identity](..//../active-directory/managed-identities-azure-resources/overview.md)
3. An [Azure Container Registry](..//..//container-registry/container-registry-intro.md) (ACR) for storing container images
4. An [AKS](..//..//aks/intro-kubernetes.md) Cluster
5. A [VNet](..//..//virtual-network/network-overview.md) required for configuring the AKS
6. A [Cosmos DB SQL API Account](../introduction.md)) along with a Database, Container, and [SQL Role](https://docs.microsoft.com/cli/azure/cosmosdb/sql/role?view=azure-cli-latest)
7. A [Key Vault](..//../key-vault/general/overview.md) to store secure keys
8. A [Log Analytics Workspace](..//../azure-monitor/logs/log-analytics-overview.md) (optional)

This quickstart uses the following best practices to enhance security of Cosmos DB.

1. Implements access control using [RBAC](..//../role-based-access-control/overview.md) and [Managed Identity](../../active-directory/managed-identities-azure-resources/overview.md) to eliminate the need for developers to manage secrets, credentials, certificates, and keys used to secure communication between services.
2. Limits Cosmos DB access to the AKS subnet by [configuring a virtual network service endpoint](../how-to-configure-vnet-service-endpoint.md).
3. Set disableLocalAuth = true in the databaseAccount resource to [enforce RBAC as the only authentication method](../how-to-setup-rbac.md#disable-local-auth). 

> [!NOTE]
> This tutorial uses the Azure Cosmos DB **[SQL API](./sql-api-get-started.md)**. However, the same concepts can also be applied to **[API for MongoDB](..//./mongodb/mongodb-introduction.md)**.

## Download the Bicep Modules
Download or [clone](https://docs.github.com/repositories/creating-and-managing-repositories/cloning-a-repository) the Bicep modules from the [GitHub repository](https://github.com/Azure-Samples/cosmos-aks-samples/tree/main/Bicep) .


## Connect to your Azure Account

```azurecli-interactive
az login

az account set -s <Subscription ID>
```

## Initialize the Deployment Parameters

Create a param.json file by using the following JSON, replace the {Resource Group Name}, {Cosmos DB Account Name}, and {ACR Instance Name} placeholders with your own values for Resource Group Name, Cosmos DB Account Name, and Azure Container Registry instance Name. 

> [!IMPORTANT]
>All resource names used in the steps below should be compliant with **[Naming rules and restrictions for Azure resources](../../azure-resource-manager/management/resource-name-rules.md)**, also ensure that the placeholders values are replaced consistently and match with values supplied in param.json.


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "value": "{Resource Group Name}"
    },    
    "cosmosName" :{
      "value": "{Cosmos DB Account Name}"
    },
    "acrName" :{
      "value": "{ACR Instance Name}"
    }
  }
}
```

## Create a Bicep Deployment

Set the environment variables by replacing the {Deployment Name}, and {Location} placeholders with your own values. Run the below commands to create the deployment.

```azurecli-interactive
deploymentName='{Deployment Name}'  # Name of the Deployment
location='{Location}' # Location for deploying the resources

az deployment sub create --name $deploymentName --location $location --template-file main.bicep --parameters @param.json
```
:::image type="content" source="./media/tutorial-aks-bicep-sql-todo/bicep_running.png" alt-text="Deployment Started":::

The deployment could take somewhere around 20 to 30 mins. Once provisioning is completed, you should see a JSON output with Succeeded as provisioning state.

:::image type="content" source="./media/tutorial-aks-bicep-sql-todo/bicep_success.png" alt-text="Deployment Success":::


You can also see the deployment status in the Resource Group

:::image type="content" source="./media/tutorial-aks-bicep-sql-todo/rg_postdeployment.png" alt-text="Deployment Status inside RG":::

> [!NOTE]
> When creating an AKS cluster a second resource group is automatically created to store the AKS resources. See [Why are two resource groups created with AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## Link the Azure Container Registry with AKS

Replace the {ACR Instance Name}, {Resource Group Name}, and {AKS Cluster Name} placeholders with your own values. Run the below command to integrate the ACR with the AKS cluster.

```azurecli-interactive

acrName='{ACR Instance Name}'
rgName='{Resource Group Name}'
aksName=$rgName'aks'

# integrate the ACR with the AKS cluster
az aks update -n $aksName -g $rgName --attach-acr $acrName
```

## Connect to the AKS cluster

To manage a Kubernetes cluster, you use [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), the Kubernetes command-line client. If you use Azure Cloud Shell, `kubectl` is already installed. To install `kubectl` locally, use the [az aks install-cli](/cli/azure/aks#az-aks-install-cli) command:

```azurecli-interactive
az aks install-cli
```

To configure `kubectl` to connect to your Kubernetes cluster, use the [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) command. This command downloads credentials and configures the Kubernetes CLI to use them.

```azurecli-interactive
az aks get-credentials -n $aksName -g $rgName
```

## Enable the AKS Pods to connect to Key Vault

Azure Active Directory (Azure AD) pod-managed identities use AKS primitives to associate managed identities for Azure resources and identities in Azure AD with pods. We'll use these identities to grant access to the Azure Key Vault Secrets Provider for Secrets Store CSI driver.

Use the command below to find the values of the Tenant ID (homeTenantId)
```azurecli-interactive
az account show
```

Using the following YAML template create a secretproviderclass.yml file. Make sure to update your own values for {Tenant Id} and {Resource Group Name} placeholders. Ensure that the below values for {Resource Group Name} placeholder matches with values supplied in param.json.

```yml
# This is a SecretProviderClass example using aad-pod-identity to access the key vault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               
    keyvaultName: "{Resource Group Name}kv"       # Replace resource group name. Key Vault name is generated by Bicep
    tenantId: "{Tenant Id}"              # The tenant ID of your account, use 'homeTenantId' attribute value from  the 'az account show' command output
```

## Apply the SecretProviderClass to AKS Cluster

Use the below command to install the Secrets Store CSI Driver using the YAML. 

```azurecli-interactive
kubectl apply -f secretproviderclass.yml
```

## Build the MVC Web Application

Download or clone the application source code from the [GitHub](https://github.com/Azure-Samples/cosmos-aks-samples/tree/main/Application) repository.

Open the Application folder in VS code. Select Yes to the warning message to add the missing build and debug assets. Press the F5 button to run the application.

## Push the Container Image to Azure Container Registry

1. To create a container image from the Explorer tab on VS Code, right click on the Dokcerfile and select BuildImage. You'll then get a prompt asking for the name and version to tag the image. Enter the name todo:latest.

    :::image type="content" source="./media/tutorial-aks-bicep-sql-todo/build_image.png" alt-text="Build Image VS Code":::

2. To push the built image to ACR open the Docker tab. You'll find the built image under the Images node. Open the todo node, right-click on latest and select "Push...".

3. You'll then get prompts to select your Azure Subscription, ACR, and Image tag. Image tag format should be {acrname}.azurecr.io/todo:latest.

    :::image type="content" source="./media/tutorial-aks-bicep-sql-todo/image_push.png" alt-text="Push Image to ACR":::

4. Wait for VS Code  to push the  image to ACR.

## Prepare Deployment YAML

Using the following YAML template create a akstododeploy.yml file. Make sure to replace the values for {ACR Name}, {Image Name}, {Version}, and {Resource Group Name} placeholders.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo
  labels:
    aadpodidbinding: "cosmostodo-apppodidentity"
    app: todo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: todo
  template:
    metadata:
      labels:
        app: todo
        aadpodidbinding: "cosmostodo-apppodidentity"
    spec:
      containers:
      - name: mycontainer
        image: "{ACR Name}/{Image Name}:{Version}"   # update as per your environment, example myacrname.azurecr.io/todo:latest. Do NOT add https:// in ACR Name
        ports:
        - containerPort: 80
        env:
        - name: KeyVaultName
          value: "{Resource Group Name}kv"       # Replace resource group name. Key Vault name is generated by Bicep
      nodeSelector:
        kubernetes.io/os: linux
      volumes:
        - name: secrets-store01-inline
          csi:
            driver: secrets-store.csi.k8s.io
            readOnly: true
            volumeAttributes:
              secretProviderClass: "azure-kvname-podid"       
---
    
kind: Service
apiVersion: v1
metadata:
  name: todo
spec:
  selector:
    app: todo
    aadpodidbinding: "cosmostodo-apppodidentity"    
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
``` 

## Apply Deployment YAML

The following command deploys the application pods and exposes the pods via a load balancer.

```azurecli-interactive
kubectl apply -f akstododeploy.yml --namespace 'my-app'
```

## Test the application

When the application runs, a Kubernetes service exposes the application front end to the internet. This process can take a few minutes to complete

Run the following command to view the external IP exposed by the load balancer

```azurecli-interactive
kubectl get services --namespace "my-app"
```

Open the IP received as output in a browser to access the application.

```
## Clean up the resources

To avoid Azure charges, you should clean up unneeded resources.  When the cluster is no longer needed, use the below commands to delete the Resource Group and Deployment

```azurecli-interactive
az group delete -g $rgName -y
az deployment sub delete -n $deploymentName
```

## Next steps
- Learn how to [Develop an ASP.NET Core MVC web application with Azure Cosmos DB](./sql-api-dotnet-application.md)
- Learn how to [Query Azure Cosmos DB by using the SQL API](./tutorial-query-sql-api.md).
- Learn how to [upgrade your cluster](../../aks/tutorial-kubernetes-upgrade-cluster.md)
- Learn how to [scale your cluster](../../aks/tutorial-kubernetes-scale.md)
- Learn how to [enable continuous deployment](../../aks/deployment-center-launcher.md)


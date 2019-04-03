---
title: Utiliser un modèle Azure Resource Manager pour créer un espace de travail
titleSuffix: Azure Machine Learning service
description: Découvrez la manière d’utiliser un modèle Azure Resource Manager pour créer un espace de travail Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883188"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour le service Azure Machine Learning

Vous trouverez dans cet article différentes façons de créer un espace de travail Azure Machine Learning service à l’aide de modèles Azure Resource Manager. Un modèle Resource Manager permet de créer des ressources sous la forme d’une seule opération coordonnée. Un modèle est un document JSON qui définit les ressources nécessaires à un déploiement. Il peut également spécifier des paramètres de déploiement. Les paramètres permettent de fournir des valeurs d’entrée lorsque vous utilisez le modèle.

Pour plus d’informations, consultez la page [Déploiement d’une application avec un modèle Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Conditions préalables

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Pour utiliser un modèle d’une interface CLI, vous devez avoir [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modèle Resource Manager

Le modèle Resource Manager suivant peut être utilisé pour créer un espace de travail du service Azure Machine Learning et aux ressources Azure associées :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Ce modèle crée les services Azure suivants :

* Groupe de ressources Azure
* Compte Stockage Azure
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Espace de travail Azure Machine Learning

Le groupe de ressources est le conteneur des services. Les différents services sont requis par l’espace de travail Azure Machine Learning.

L’exemple de modèle possède deux paramètres :

* L’**emplacement**, où le groupe de ressources et les services vont être créés.

    Le modèle utilise l’emplacement que vous sélectionnez pour la plupart des ressources. L’exception est le service Application Insights, qui n’est pas disponible dans tous les emplacements où les autres services le sont. Si vous sélectionnez un emplacement où il n’est pas disponible, le service est créé à l’emplacement USA Centre Sud.

* Le **nom de l’espace de travail**, qui est le nom convivial de l’espace de travail Azure Machine Learning.

    Le nom des autres services est généré de façon aléatoire.

Pour plus d’informations sur les modèles, voir les articles suivants :

* [Créer des modèles Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Déployer une application avec des modèles Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Types de ressources Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

1. Suivez la procédure indiquée dans [Déployer des ressources à partir d’un modèle personnalisé](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Lorsque vous arrivez à l’écran __Modifier le modèle__, collez dans le modèle à partir de ce document.
1. Sélectionnez __Enregistrer__ pour utiliser le modèle. Fournissez les informations suivantes et acceptez les conditions répertoriées :

   * Abonnement : Sélectionnez l’abonnement Azure à utiliser pour ces ressources.
   * Groupe de ressources : Sélectionnez ou créez un groupe de ressources pour contenir les services.
   * Nom de l’espace de travail : Nom à utiliser pour l’espace de travail Azure Machine Learning qui va être créé. Le nom de l'espace de travail doit contenir entre 3 et 33 caractères. Il ne peut contenir que des caractères alphanumériques et « - ».
   * Localisation : Sélectionnez l’emplacement de création des ressources.

     ![Paramètres du modèle sur le portail Azure](media/how-to-create-workspace-template/template-parameters.png)

Pour plus d’informations, consultez [Déployer des ressources à partir d’un modèle personnalisé](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Cet exemple suppose que vous avez enregistré le modèle dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure

Cet exemple suppose que vous avez enregistré le modèle dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure CLI](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’API REST Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

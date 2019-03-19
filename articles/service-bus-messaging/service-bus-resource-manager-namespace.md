---
title: Créer un espace de noms Service Bus Messaging à l’aide d’un modèle Azure Resource Manager | Microsoft Docs
description: Utiliser un modèle Azure Resource Manager pour créer un espace de noms Service Bus Messaging
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: a79565661ae11e70364d64503b3b11bdeabeabdb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899755"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Créer un espace de noms Service Bus à l’aide d’un modèle Azure Resource Manager
Ce guide de démarrage rapide vous permet de créer un modèle Azure Resource Manager qui génère un espace de noms Service Bus de type **Messagerie** avec une référence SKU **Standard**. L'article définit également les paramètres qui sont spécifiés pour l'exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins. Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates]. Pour le modèle complet, consultez le [modèle d’espace de noms Service Bus][Service Bus namespace template] sur GitHub.

> [!NOTE]
> Les modèles Azure Resource Manager suivants sont disponibles au téléchargement et au déploiement. 
> 
> * [Créer un espace de noms Service Bus avec file d’attente](service-bus-resource-manager-namespace-queue.md)
> * [Créer un espace de noms Service Bus par rubrique et abonnement](service-bus-resource-manager-namespace-topic.md)
> * [Créer un espace de noms Service Bus avec file d'attente et règle d’autorisation](service-bus-resource-manager-namespace-auth-rule.md)
> * [Créer un modèle d’espace de noms Service Bus avec rubrique, abonnement et règle](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Pour rechercher les derniers modèles, recherchez « Service Bus » dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].

## <a name="quick-deployment"></a>Déploiement rapide
Pour exécuter l'exemple sans rédiger de code JSON et sans exécuter de commande PowerShell/CLI, sélectionnez le bouton suivant :

[![Déployer sur Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Pour créer et déployer le modèle manuellement, parcourez les sections suivantes de cet article.

## <a name="prerequisites"></a>Conditions préalables
Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous souhaitez utiliser **Azure PowerShell** pour déployer le modèle Resource Manager, [installez Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Si vous souhaitez utiliser **Azure CLI** pour déployer le modèle Resource Manager, [installez Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Créer le fichier JSON du modèle Resource Manager 
Créez un fichier JSON nommé **MyServiceBusNamespace.json** avec le contenu suivant : 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Ce modèle crée un espace de noms Service Bus standard. Pour la syntaxe et les propriétés JSON, consultez la référence sur les modèles [espaces de noms](/azure/templates/microsoft.servicebus/namespaces).

## <a name="create-the-parameters-json"></a>Créer le fichier JSON de paramètres
Le modèle que vous avez créé à l’étape précédente contient une section appelée `Parameters`. Vous définissez pour ces valeurs des paramètres qui varient en fonction du projet que vous déployez ou de l’environnement cible. Ce modèle définit les paramètres suivants : **serviceBusNamespaceName**, **serviceBusSku** et **location**. Pour en savoir plus sur les références SKU de Service Bus, consultez [Références SKU de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Créez un fichier JSON nommé **MyServiceBusNamespace-Parameters.json** avec le contenu suivant : 

> [!NOTE] 
> Spécifiez un nom pour votre espace de noms Service Bus. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Utiliser Azure PowerShell pour déployer le modèle

### <a name="sign-in-to-azure"></a>Connexion à Azure
1. Lancer Azure PowerShell

2. Exécutez la commande ci-après pour vous connecter à Azure :

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Si vous avez exécuté les commandes suivantes pour définir le contexte de l’abonnement actuel :

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Déployer des ressources
Pour déployer les ressources à l'aide d'Azure PowerShell, basculez vers le dossier dans lequel vous avez enregistré les fichiers JSON et exécutez les commandes suivantes :

> [!IMPORTANT]
> Spécifiez un nom pour le groupe de ressources Azure en tant que valeur de $resourceGroupName avant d’exécuter les commandes. 

1. Déclarez une variable pour le nom du groupe de ressources et spécifiez une valeur pour celle-ci. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Création d’un groupe de ressources Azure.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Déployez le modèle Resource Manager. Spécifiez les noms du déploiement proprement dit, du groupe de ressources, du fichier JSON du modèle et du fichier JSON des paramètres.

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Utiliser Azure CLI pour déployer le modèle

### <a name="sign-in-to-azure"></a>Connexion à Azure

1. Exécutez la commande ci-après pour vous connecter à Azure :

    ```azurecli
    az login
    ```
2. Définissez le contexte d’abonnement actuel. Remplacez `MyAzureSub` par le nom de l’abonnement Azure que vous souhaitez utiliser :

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Déployer des ressources
Pour déployer les ressources à l’aide d’Azure CLI, basculez vers le dossier contenant les fichiers JSON, puis exécutez les commandes suivantes :

> [!IMPORTANT]
> Spécifiez le nom du groupe de ressources Azure dans la commande az group create. .

1. Création d’un groupe de ressources Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Déployez le modèle Resource Manager. Spécifiez les noms du groupe de ressources, du déploiement, du fichier JSON du modèle et du fichier JSON des paramètres.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un espace de noms Service Bus. Consultez les autres guides de démarrage rapide pour apprendre à créer et utiliser des files d'attente et des rubriques/abonnements : 

- [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Prise en main des rubriques Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

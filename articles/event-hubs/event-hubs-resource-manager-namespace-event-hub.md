---
title: Créer un hub d’événements avec un groupe de consommateurs - Azure Event Hubs | Microsoft Docs
description: Créer un espace de noms Event Hubs avec un concentrateur d’événements et un groupe de consommateurs à l’aide de modèles Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: db5bb30c4049eca699f8adb45a923915033b4216
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134452"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Démarrage rapide : Créer un hub d’événements à l’aide d’un modèle Azure Resource Manager
Azure Event Hubs est une plateforme de diffusion de données volumineuses et un service d’ingestion d’événements, capable de recevoir et de traiter des millions d’événements par seconde. Les concentrateurs d’événements peuvent traiter et stocker des événements, des données ou la télémétrie produits par des logiciels et appareils distribués. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Dans ce démarrage rapide, vous créez un Event Hub à l’aide d’un modèle Azure Resource Manager. Vous allez utiliser un modèle Azure Resource Manager pour créer un espace de noms de type [Event Hubs](event-hubs-what-is-event-hubs.md) avec un hub d’événements et un groupe de consommateurs. L’article montre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins. Pour obtenir des informations sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].


> [!NOTE]
> Pour le modèle complet, consultez [Modèle d’Event Hub et de groupe de consommateurs][Event Hub and consumer group template] sur GitHub. Ce modèle créé un groupe de consommateurs en plus d’un espace de noms Event Hub ainsi qu’un Event Hub. Pour rechercher les derniers modèles, recherchez Event Hubs dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].

## <a name="prerequisites"></a>Prérequis
Pour suivre ce guide de démarrage rapide, vous devez avoir un abonnement Azure. Si vous n’en avez pas, [créez un compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous souhaitez utiliser **Azure PowerShell** pour déployer le modèle Resource Manager, [installez Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Si vous souhaitez utiliser **Azure CLI** pour déployer le modèle Resource Manager, [installez Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Créer le fichier JSON du modèle Resource Manager
Créez un fichier JSON nommé MyEventHub.json avec le contenu suivant, puis enregistrez-le dans un dossier (par exemple : C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Créer le fichier JSON de paramètres
Créez un fichier JSON nommé MyEventHub-Parameters.json contenant les paramètres du modèle Azure Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
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

### <a name="provision-resources"></a>Provisionner les ressources
Pour déployer/provisionner les ressources à l’aide d’Azure PowerShell, basculez vers le dossier C:\EventHubsQuickStart\ARM\, puis exécutez les commandes suivantes :

> [!IMPORTANT]
> Spécifiez un nom pour le groupe de ressources Azure en tant que valeur pour $resourceGroupName avant d’exécuter les commandes. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Utiliser Azure CLI pour déployer le modèle

## <a name="sign-in-to-azure"></a>Connexion à Azure

Les étapes suivantes ne sont pas obligatoires si vous exécutez des commandes dans Cloud Shell. Si vous utilisez l’interface CLI localement, effectuez les étapes suivantes pour vous connecter à Azure et définir votre abonnement actuel :

Exécutez la commande ci-après pour vous connecter à Azure :

```azurecli
az login
```

Définissez le contexte d’abonnement actuel. Remplacez `MyAzureSub` par le nom de l’abonnement Azure que vous souhaitez utiliser :

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Provisionner les ressources
Pour déployer les ressources à l’aide d’Azure CLI, basculez vers le dossier C:\EventHubsQuickStart\ARM\, puis exécutez les commandes suivantes :

> [!IMPORTANT]
> Spécifiez le nom du groupe de ressources Azure dans la commande az group create. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Félicitations ! Vous avez utilisé le modèle Azure Resource Manager pour créer un espace de noms Event Hubs, ainsi qu’un hub d’événements dans cet espace de noms.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un espace de noms Event Hubs et utilisé des exemple d’application pour envoyer et recevoir des événements depuis votre Event Hub. Pour obtenir des instructions pas à pas sur l’envoi à ou la réception d’événements à partir d’un Event Hub, consultez les didacticiels suivants : 

- **Envoyer des événements à un hub d’événements** : [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Recevoir des événements d’un hub d’événements** : [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

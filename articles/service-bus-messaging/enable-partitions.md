---
title: Activer le partitionnement dans les files d’attente et rubriques Azure Service Bus
description: Cet article explique comment activer le partitionnement dans les files d’attente et rubriques Azure Service Bus en utilisant le portail Azure, PowerShell, l’interface CLI et les langages de programmation (C#, Java, Python et JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ac77dfc2e72d1d83c4424c05da40c5adba1e8e9d
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108076686"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>Activer le partitionnement pour une file d’attente ou une rubrique Azure Service Bus
Service Bus permet également le partitionnement des files d’attente et des rubriques, ou des entités de messagerie entre plusieurs courtiers de messages et banques de messagerie. Le partitionnement signifie que le débit global d’une entité partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible. Les rubriques et les files d’attente partitionnées peuvent contenir toutes les fonctionnalités avancées de Service Bus, comme la prise en charge des transactions et des sessions. Pour plus d’informations, consultez [Rubriques et files d’attente partitionnées](service-bus-partitioning.md). Cet article vous montre différentes façons d’activer la détection des messages dupliqués pour une file d’attente ou une rubrique Service Bus. 

> [!IMPORTANT]
> - Le partitionnement est disponible au moment de la création de l’entité pour toutes les files d’attente et rubriques dans les références SKU De base et Standard. Il n’est pas disponible pour la référence SKU de messagerie Premium, mais toutes les entités partitionnées déjà existantes dans les espaces de noms Premium continuent de fonctionner comme prévu.
> - Il n’est pas possible de modifier l’option de partitionnement sur une file d’attente ou une rubrique existante. Vous ne pouvez définir l’option que lorsque vous créez une file d’attente ou une rubrique. 
> - Dans un espace de noms de niveau **Standard**, vous pouvez créer des files d’attente et des rubriques Service Bus avec des tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Si le partitionnement est activé, Service Bus crée 16 copies (16 partitions) de l’entité, chacune de la même taille spécifiée. Par conséquent, si vous créez une file d’attente de 5 Go, avec 16 partitions, la taille maximale de la file d’attente est (5 \* 16) = 80 Go. 
> - Le partitionnement des entités n’est pas pris en charge dans un espace de noms de niveau **Premium**. Toutefois, vous pouvez quand même créer des files d’attente et des rubriques Service Bus avec des tailles de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 Go (la valeur par défaut est 1 Go). Vous pouvez voir la taille maximale de votre file d’attente ou rubrique partitionnée sur la page **Vue d’ensemble** du [portail Azure](https://portal.azure.com).


## <a name="using-azure-portal"></a>En passant par le portail Azure
Lorsque vous créez une **file d’attente** dans le portail Azure, sélectionnez **Activer le partitionnement** comme indiqué dans l’image suivante. 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="Activer le partitionnement au moment de la création de la file d’attente":::

Lorsque vous créez une rubrique dans le portail Azure, sélectionnez **Activer le partitionnement** comme indiqué dans l’image suivante. 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="Activer le partitionnement au moment de la création de la rubrique":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec le partitionnement activé**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) en définissant `--enable-partitioning` sur `true`.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

Pour **créer une rubrique avec le partitionnement activé**, utilisez la commande [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) en définissant `--enable-partitioning` sur `true`.

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Pour **créer une file d’attente avec le partitionnement activé**, utilisez la commande [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) en définissant `-EnablePartitioning` sur `$True`. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

Pour **créer une rubrique avec le partitionnement activé**, utilisez la commande [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) en définissant `-EnablePartitioning` sur `true`. 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec le partitionnement activé**, définissez `enablePartitioning` sur `true` dans la section Propriétés de la file d’attente. Pour plus d’informations, consultez la documentation de [référence sur les modèles de files d’attente/espaces de noms Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
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
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

Pour **créer une rubrique avec la détection des doublons activée**, définissez `enablePartitioning` sur `true` dans la section Propriétés de la rubrique. Pour plus d’informations, consultez la documentation de [référence sur les modèles de files d’attente/espaces de noms Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
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
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}
```



## <a name="next-steps"></a>Étapes suivantes
Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
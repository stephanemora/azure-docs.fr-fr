---
title: Activer la détection des messages dupliqués – Azure Service Bus
description: Cet article explique comment activer la détection des messages dupliqués en utilisant Portail Azure, PowerShell, CLI et des langages de programmation (C#, Java, Python et JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755057"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>Activer la détection des messages dupliqués pour une file d’attente ou une rubrique Azure Service Bus
Lorsque vous activez la détection des doublons pour une file d’attente ou une rubrique, Azure Service Bus conserve un historique de tous les messages envoyés à la file d’attente ou à la rubrique pendant une durée configurable. Pendant cet intervalle, votre file d’attente ou votre rubrique ne stockera pas de messages en double. L’activation de cette propriété garantit une livraison unique sur une période de temps définie par l’utilisateur. Pour en savoir plus, consultez [Détection des doublons](duplicate-detection.md). Cet article vous montre différentes façons d’activer la détection des messages dupliqués pour une file d’attente ou une rubrique Service Bus. 


> [!NOTE]
> - Le niveau De base de Service Bus ne prend pas en charge la détection des doublons. Les niveaux Standard et Premium prennent en charge la détection des doublons. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
> - Vous ne pouvez pas activer ou désactiver la détection des doublons après la création de la file d’attente ou de la rubrique. Vous ne pouvez le faire qu’au moment de la création de la file d’attente ou de la rubrique. 

## <a name="using-azure-portal"></a>En passant par le portail Azure
Lorsque vous créez une **file d’attente** dans le portail Azure, sélectionnez **Activer la détection des doublons** comme indiqué dans l’image suivante. Vous pouvez configurer la taille de la fenêtre de détection des doublons lors de la création d’une file d’attente ou d’une rubrique. 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="Activer la détection des doublons au moment de la création de la file d’attente":::

Lorsque vous créez une rubrique dans le portail Azure, sélectionnez **Activer la détection des doublons** comme indiqué dans l’image suivante. 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="Activer la détection des doublons au moment de la création de la rubrique":::

Vous pouvez également configurer ce paramètre pour une file d’attente ou une rubrique existante si vous aviez activé la détection des doublons au moment de la création. 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>Mettre à jour la taille de la fenêtre de détection des doublons pour une file d’attente ou une rubrique existante
Pour modifier la taille de la fenêtre de détection des doublons pour une file d’attente ou une rubrique existante, dans la page **Vue d’ensemble**, sélectionnez **Modifier** pour **Fenêtre de détection des doublons**.  

#### <a name="queue"></a>File d'attente
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="Définir la taille de la fenêtre de détection des doublons pour une file d’attente":::

#### <a name="topic"></a>Rubrique
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="Définir la taille de la fenêtre de détection des doublons pour une rubrique":::


## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec la détection des doublons activée**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) avec `--enable-duplicate-detection` défini sur `true`. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Pour **créer une rubrique avec la détection des doublons activée**, utilisez la commande [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) avec `--enable-duplicate-detection` défini sur `true`.

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

Les exemples ci-dessus définissent également la taille de la fenêtre de détection des doublons à l’aide du paramètre `--duplicate-detection-history-time-window`. La taille de la fenêtre est définie sur un jour. La valeur par défaut est de dix minutes et la valeur maximale autorisée est de sept jours.

Pour **mettre à jour une file d’attente avec une nouvelle taille de fenêtre de détection**, utilisez la commande [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) avec le paramètre `--duplicate-detection-history-time-window`. Dans cet exemple, la taille de la fenêtre est mise à jour à sept jours. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

De même, pour **mettre à jour une rubrique avec une nouvelle taille de fenêtre de détection**, utilisez la commande [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) avec le paramètre `--duplicate-detection-history-time-window`. Dans cet exemple, la taille de la fenêtre est mise à jour à sept jours.

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Pour **créer une file d’attente avec la détection des doublons activée**, utilisez la commande [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) avec `-RequiresDuplicateDetection` défini sur `$True`. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

Pour **créer une rubrique avec la détection des doublons activée**, utilisez la commande [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) avec `-RequiresDuplicateDetection` défini sur `true`. 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

Les exemples ci-dessus définissent également la taille de la fenêtre de détection des doublons à l’aide du paramètre `-DuplicateDetectionHistoryTimeWindow`. La taille de la fenêtre est définie sur un jour. La valeur par défaut est de dix minutes et la valeur maximale autorisée est de sept jours.

Pour **mettre à jour une file d’attente avec une nouvelle taille de fenêtre de détection**, consultez l’exemple suivant.  Dans cet exemple, la taille de la fenêtre est mise à jour à sept jours.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

Pour **mettre à jour une rubrique avec une nouvelle taille de fenêtre de détection**, consultez l’exemple suivant. Dans cet exemple, la taille de la fenêtre est mise à jour à sept jours.

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec la détection des doublons activée**, définissez `requiresDuplicateDetection` sur `true` dans la section Propriétés de la file d’attente. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/queues](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). Spécifiez une valeur pour la propriété `duplicateDetectionHistoryTimeWindow` afin de définir la taille de la fenêtre de détection des doublons. Dans l’exemple suivant, elle est définie sur un jour.  

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

Pour **créer une rubrique avec la détection des doublons activée**, définissez `requiresDuplicateDetection` sur `true` dans la section Propriétés de la rubrique. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/topics](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json). 

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
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>Étapes suivantes
Essayez les exemples dans le langage de votre choix pour explorer les fonctionnalités d’Azure Service Bus. 

- [Exemples de bibliothèque de client Azure Service Bus pour Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Exemples Azure.Messaging.ServiceBus pour .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Recherchez des exemples pour les anciennes bibliothèques de client .NET et Java ci-dessous :
- [Exemples Microsoft.Azure.ServiceBus pour .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples azure-servicebus pour Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
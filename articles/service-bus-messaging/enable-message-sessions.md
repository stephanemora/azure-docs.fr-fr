---
title: Activer des sessions de messagerie Azure Service Bus | Microsoft Docs
description: Cet article explique comment activer des sessions de messagerie en utilisant le Portail Azure, PowerShell, l’interface CLI et des langages de programmation (C#, Java, Python et JavaScript)
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: dc2667b746a57c7f2e4ac5faec88c4540bed1180
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755056"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>Activer des sessions de messagerie pour une file d’attente ou un abonnement Azure Service Bus
Les sessions Azure Service Bus permettent un traitement conjoint et chronologique de séquences illimitées de messages associés. Vous pouvez utiliser des sessions dans des modèles **premier entré, premier sorti (FIFO**) et **requête-réponse**. Pour plus d’informations, consultez [Sessions de messagerie](message-sessions.md). Cet article vous montre différentes façons d’activer des sessions pour une file d’attente ou un abonnement Service Bus. 

> [!IMPORTANT]
> - Le niveau de base de Service Bus ne prend pas en charge les sessions. Les niveaux standard et premium prennent en charge les sessions. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
> - Vous ne pouvez pas activer ou désactiver les sessions de messagerie après la création de la file d’attente ou de l’abonnement. Vous ne pouvez le faire qu’au moment de la création de la file d’attente ou de l’abonnement. 

## <a name="using-azure-portal"></a>En passant par le portail Azure
Lorsque vous créez une **file d’attente** dans le Portail Azure, sélectionnez **Activer les sessions** comme illustré à l’image suivante. 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="Activer la session au moment de la création de la file d’attente":::

Lorsque vous créez un abonnement pour une rubrique dans le Portail Azure, sélectionnez **Activer les sessions** comme illustré à l’image suivante. 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="Activer la session au moment de la création de l’abonnement":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec les sessions de messagerie activées**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) et définissez `--enable-session` sur `true`.

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

Pour **créer un abonnement pour une rubrique avec les sessions de messagerie activées**, utilisez la commande [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) et définissez `--enable-session` sur `true`.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Pour **créer une file d’attente avec les sessions de messagerie activées**, utilisez la commande [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) et définissez `-RequiresSession` sur `$True`. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

Pour **créer un abonnement pour une rubrique avec les sessions de messagerie activées**, utilisez la commande [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) et définissez `-RequiresSession` sur `true`. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec les sessions de messagerie activées**, définissez `requiresSession` sur `true` dans la section Propriétés de la file d’attente. Pour plus d’informations, consultez la documentation de [référence sur les modèles de files d’attente/espaces de noms Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

Pour **créer un abonnement pour une rubrique avec les sessions de messagerie activées**, définissez `requiresSession` sur `true` dans la section des propriétés de l’abonnement. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/topics/subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "requiresSession": true
              }
            }
          ]
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
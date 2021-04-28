---
title: Activer le transfert automatique pour les files d’attente et les abonnements Azure Service Bus
description: Cet article explique comment activer le transfert automatique pour les files d’attente et les abonnements en utilisant Portail Azure, PowerShell, CLI et les langages de programmation (C#, Java, Python et JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: ef22ae08485dc896c94858db4e422cf89a00ec1f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755073"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Activer le transfert automatique pour les files d’attente et les abonnements Azure Service Bus
La fonctionnalité de transfert automatique de Service Bus vous permet de chaîner une file d’attente ou un abonnement à une autre file d’attente ou rubrique qui fait partie du même espace de noms. Lorsque le transfert automatique est activé, Service Bus supprime automatiquement les messages placés dans la première file d’attente ou le premier abonnement (source) pour les placer dans la deuxième file d’attente ou la rubrique (destination). Il est toujours possible d’envoyer un message directement à l’entité de destination. Pour en savoir plus, consultez l’article [Chaînage des entités Service Bus avec transfert automatique](service-bus-auto-forwarding.md). Cet article vous montre différentes façons d’activer le transfert automatique pour les files d’attente et les abonnements de Service Bus. 

> [!IMPORTANT]
> Le niveau de base de Service Bus ne prend pas en charge la fonctionnalité de transfert automatique. Les niveaux Standard et Premium prennent en charge la fonctionnalité. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>En passant par le portail Azure
Lors de la création d’une **file d’attente** ou d’un **abonnement** pour une rubrique du portail Azure, sélectionnez **Transférer les messages à la file d’attente/rubrique** comme indiqué dans les exemples suivants. Ensuite, indiquez si vous souhaitez que les messages soient transférés vers une file d’attente ou une rubrique. Dans cet exemple, l’option **File d’attente** est sélectionnée et une file d’attente (**myqueue**) du même espace de noms est sélectionnée.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Créer une file d’attente avec le transfert automatique activé
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Activer le transfert automatique au moment de la création de la file d’attente":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Créer un abonnement pour une rubrique avec le transfert automatique activé
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Activer le transfert automatique au moment de la création de l’abonnement":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec le transfert automatique activé**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) avec `--forward-to` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Pour **créer un abonnement pour une rubrique avec le transfert automatique activé**, utilisez la commande [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) avec `--forward-to` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Pour **créer une file d’attente avec le transfert automatique activé**, utilisez la commande [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) avec `-ForwardTo` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

Pour **créer un abonnement pour une rubrique avec le transfert automatique activé**, utilisez la commande [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) avec `-ForwardTo` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec le transfert automatique activé**, définissez `forwardTo` dans la section Propriétés de la file d’attente sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/queues](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

Pour **créer un abonnement pour une rubrique avec le transfert automatique activé**, définissez `forwardTo` dans la section Propriétés de la file d’attente sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/topics/subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "forwardTo": "myqueue2"
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
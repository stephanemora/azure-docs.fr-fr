---
title: Activer la mise en file d’attente de lettres mortes pour les files d’attente et les abonnements Azure Service Bus
description: Cet article explique comment activer la mise en file d’attente de lettres mortes pour les files d’attente et les abonnements en utilisant Portail Azure, PowerShell, CLI et les langages de programmation (C#, Java, Python et JavaScript).
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 789f9221c224a70225849d05b736276b95ebfc11
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989267"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>Activer la mise en file d’attente de lettres mortes à l’expiration du message pour les files d’attente et les abonnements Azure Service Bus
Les files d’attente et les abonnements aux rubriques Azure Service Bus fournissent une sous-file d’attente secondaire, appelée file d’attente de lettres mortes. La file d’attente de lettres mortes n’a pas besoin d’être explicitement créée et ne peut pas être supprimée ni gérée indépendamment de l’entité principale. L’objectif de la file d’attente de lettres mortes est de conserver les messages qui ne peuvent pas être remis aux destinataires ou les messages qui n’ont pas pu être traités. Pour en savoir plus, consultez [Vue d’ensemble des files d’attente de lettres mortes Service Bus](service-bus-dead-letter-queues.md). Cet article vous montre différentes façons d’activer la mise en file d’attente de lettres mortes pour les files d’attente et les abonnements de Service Bus. 

## <a name="using-azure-portal"></a>En passant par le portail Azure
Lors de la création d’une **file d’attente** ou d’un **abonnement** à une rubrique dans le portail Azure, sélectionnez **Activer la mise en file d’attente de lettres mortes à l’expiration du message** comme indiqué dans les exemples suivants. 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>Créer une file d’attente avec mise en file d’attente de lettres mortes
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="Activer la mise en file d’attente de lettres mortes au moment de la création de la file d’attente":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>Créer un abonnement avec mise en file d’attente de lettres mortes
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="Activer la mise en file d’attente de lettres mortes au moment de la création de l’abonnement":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>Mettre à jour le paramètre de mise en file d’attente de lettres mortes à l’expiration du message pour une file d’attente existante
Dans la page **Vue d’ensemble** de la file d’attente Service Bus, sélectionnez la valeur actuelle pour le paramètre de **mise en file d’attente de lettres mortes** à l’expiration du message. Dans l’exemple suivant, la valeur actuelle est **Désactivé**. Vous pouvez activer ou désactiver la mise en file d’attente de lettres mortes à l’expiration du message dans la fenêtre contextuelle. 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="Activer la mise en file d’attente de lettres mortes à l’expiration du message pour une file d’attente existante":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>Mettre à jour le paramètre de mise en file d’attente de lettres mortes à l’expiration du message pour un abonnement existant
Dans la page **Vue d’ensemble** de l’abonnement Service Bus, sélectionnez la valeur actuelle pour le paramètre de **mise en file d’attente de lettres mortes** à l’expiration du message. Dans l’exemple suivant, la valeur actuelle est **Désactivé**. Vous pouvez activer ou désactiver la mise en file d’attente de lettres mortes à l’expiration du message dans la fenêtre contextuelle. 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="Activer la mise en file d’attente de lettres mortes à l’expiration du message pour un abonnement existant":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec mise en file d’attente de lettres mortes à l’expiration du message**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) avec `--enable-dead-lettering-on-message-expiration` défini sur `true`. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

Pour **activer le paramètre de mise en file d’attente de lettres mortes à l’expiration du message dans une file d’attente existante**, utilisez la commande [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) avec `--enable-dead-lettering-on-message-expiration` défini sur `true`. 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


Pour **créer un abonnement à une rubrique avec mise en file d’attente de lettres mortes à l’expiration du message**, utilisez la commande [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) avec `--enable-dead-lettering-on-message-expiration` défini sur `true`.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

Pour **activer le paramètre de mise en file d’attente de lettres mortes à l’expiration du message dans un abonnement à une rubrique**, utilisez la commande [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) avec `--enable-dead-lettering-on-message-expiration` défini sur `true`.

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Pour **créer une file d’attente avec mise en file d’attente de lettres mortes à l’expiration du message**, utilisez la commande [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) avec `-DeadLetteringOnMessageExpiration` défini sur `$True`. 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

Pour **activer le paramètre de mise en file d’attente de lettres mortes à l’expiration du message dans une file d’attente existante**, utilisez la commande [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) comme indiqué dans l’exemple suivant.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Pour **créer un abonnement à une rubrique avec mise en file d’attente de lettres mortes à l’expiration du message**, utilisez la commande [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) avec `-DeadLetteringOnMessageExpiration` défini sur `$True`. 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

Pour **activer le paramètre de mise en file d’attente de lettres mortes à l’expiration du message dans un abonnement existant**, consultez l’exemple suivant.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec mise en file d’attente de lettres mortes à l’expiration du message**, définissez `deadLetteringOnMessageExpiration` dans la section Propriétés de la file d’attente sur `true`. Pour plus d’informations, consultez la documentation de [référence sur les modèles de files d’attente/espaces de noms Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

Pour **créer un abonnement à une rubrique avec mise en file d’attente de lettres mortes à l’expiration du message**, définissez `deadLetteringOnMessageExpiration` dans la section Propriétés de la file d’attente sur `true`. Pour plus d’informations, consultez [Modèle de référence Microsoft.ServiceBus namespaces/topics/subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json). 

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
                "deadLetteringOnMessageExpiration": true
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

- [Exemples de bibliothèque de client Azure Service Bus pour .NET (dernière version)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Exemples de bibliothèque de client Azure Service Bus pour Java (dernière version)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Exemples de bibliothèque de client Azure Service Bus pour JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Exemples de bibliothèque de client Azure Service Bus pour TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Recherchez des exemples pour les anciennes bibliothèques clientes .NET et Java ci-dessous :
- [Exemples de bibliothèque de client Azure Service Bus pour .NET (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Exemples de bibliothèque de client Azure Service Bus pour Java (version héritée)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)
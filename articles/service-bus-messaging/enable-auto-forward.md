---
title: Activer le transfert automatique pour les files d’attente et les abonnements Azure Service Bus
description: Cet article explique comment activer le transfert automatique pour les files d’attente et les abonnements en utilisant Portail Azure, PowerShell, CLI et les langages de programmation (C#, Java, Python et JavaScript).
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: da5af50941263c54a08de27df43fc85b5bd75b4a
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671492"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>Activer le transfert automatique pour les files d’attente et les abonnements Azure Service Bus
La fonctionnalité de transfert automatique de Service Bus vous permet de chaîner une file d’attente ou un abonnement à une autre file d’attente ou rubrique qui fait partie du même espace de noms. Lorsque le transfert automatique est activé, Service Bus supprime automatiquement les messages placés dans la première file d’attente ou le premier abonnement (source) pour les placer dans la deuxième file d’attente ou la rubrique (destination). Il est toujours possible d’envoyer un message directement à l’entité de destination. Pour en savoir plus, consultez l’article [Chaînage des entités Service Bus avec transfert automatique](service-bus-auto-forwarding.md). Cet article vous montre différentes façons d’activer le transfert automatique pour les files d’attente et les abonnements de Service Bus. 

> [!IMPORTANT]
> Le niveau de base de Service Bus ne prend pas en charge la fonctionnalité de transfert automatique. Les niveaux Standard et Premium prennent en charge la fonctionnalité. Pour connaître les différences entre ces niveaux, voir [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="using-azure-portal"></a>En passant par le portail Azure
Lors de la création d’une **file d’attente** ou d’un **abonnement** pour une rubrique du portail Azure, sélectionnez **Transférer les messages à la file d’attente/rubrique** comme indiqué dans les exemples suivants. Ensuite, indiquez si vous souhaitez que les messages soient transférés vers une file d’attente ou une rubrique. Dans cet exemple, l’option **File d’attente** est sélectionnée et une file d’attente du même espace de noms est sélectionnée.

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>Créer une file d’attente avec le transfert automatique activé
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="Activer le transfert automatique au moment de la création de la file d’attente":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>Créer un abonnement pour une rubrique avec le transfert automatique activé
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="Activer le transfert automatique au moment de la création de l’abonnement":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>Mettre à jour le paramètre de transfert automatique pour une file d’attente existante
Dans la page **Vue d’ensemble** de la file d’attente de Service Bus, sélectionnez la valeur actuelle pour le paramètre **Transférer les messages à**. Dans l’exemple suivant, la valeur actuelle est **Désactivé**. Dans la fenêtre **Transférer les messages à la file d’attente/rubrique**, vous pouvez sélectionner la file d’attente ou la rubrique dans laquelle vous souhaitez que les messages soient transférés. 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="Activer le transfert automatique pour une file d’attente existante":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>Mettre à jour le paramètre de transfert automatique pour un abonnement existant
Dans la page **Vue d’ensemble** de l’abonnement Service Bus, sélectionnez la valeur actuelle pour le paramètre **Transférer les messages à**. Dans l’exemple suivant, la valeur actuelle est **Désactivé**. Dans la fenêtre **Transférer les messages à la file d’attente/rubrique**, vous pouvez sélectionner la file d’attente ou la rubrique dans laquelle vous souhaitez que les messages soient transférés. 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="Activer le transfert automatique pour un abonnement existant":::

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Pour **créer une file d’attente avec le transfert automatique activé**, utilisez la commande [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) avec `--forward-to` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

Pour **mettre à jour le paramètre de transfert automatique pour une file d’attente existante**, utilisez la commande [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) avec la valeur `--forward-to` définie sur le nom de la file d’attente ou de la rubrique à laquelle vous souhaitez transférer les messages. 

```azurecli-interactive
az servicebus queue update \
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

Pour **mettre à jour le paramètre de transfert automatique pour un abonnement à une rubrique**, utilisez la commande [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) avec la valeur `--forward-to` définie sur le nom de la file d’attente ou de la rubrique à laquelle vous souhaitez transférer les messages.

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

Pour **mettre à jour le paramètre de transfert automatique d’une file d’attente existante**, utilisez la commande [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) comme indiqué dans l’exemple suivant.

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

Pour **créer un abonnement pour une rubrique avec le transfert automatique activé**, utilisez la commande [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) avec `-ForwardTo` défini sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés.

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

Pour **mettre à jour le paramètre de transfert automatique pour un abonnement existant**, consultez l’exemple suivant.

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager
Pour **créer une file d’attente avec le transfert automatique activé**, définissez `forwardTo` dans la section Propriétés de la file d’attente sur le nom de la file d’attente ou de la rubrique vers laquelle vous souhaitez que les messages soient transférés. Pour plus d’informations, consultez la documentation de [référence sur les modèles de files d’attente/espaces de noms Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json). 

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

## <a name="net"></a>.NET 

### <a name="azuremessagingservicebus-latest"></a>Azure.Messaging.ServiceBus (dernière version)
Vous pouvez activer la fonctionnalité de transfert automatique en définissant [CreateQueueOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createqueueoptions.forwardto) ou [CreateSubscriptionOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createsubscriptionoptions.forwardto), puis en utilisant les méthodes [CreateQueueAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createqueueasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateQueueAsync_Azure_Messaging_ServiceBus_Administration_CreateQueueOptions_System_Threading_CancellationToken_) ou [CreateSubscriptionAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscriptionasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateSubscriptionAsync_Azure_Messaging_ServiceBus_Administration_CreateSubscriptionOptions_System_Threading_CancellationToken_) qui acceptent les paramètres `CreateQueueOptions` ou `CreateSubscriptionOptions`. 

### <a name="microsoftazureservicebus-legacy"></a>Microsoft.Azure.ServiceBus (hérité)
Vous pouvez activer le transfert automatique en définissant les propriétés [QueueDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriptionDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) pour la source, comme dans l’exemple suivant :

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

## <a name="java"></a>Java

### <a name="azure-messaging-servicebus-latest"></a>azure-messaging-servicebus (dernière version)
Vous pouvez activer la fonctionnalité de transfert automatique à l’aide de la méthode [CreateQueueOptions.setForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createqueueoptions.setforwardto) ou [CreateSubscriptionOptions.SetForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createsubscriptionoptions.setforwardto), puis à l’aide de la méthode [createQueue](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createqueue#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createQueue_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateQueueOptions_) ou de la méthode [createSubscription](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscription#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createSubscription_java_lang_String_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateSubscriptionOptions_), qui prennent les paramètres `CreateQueueOptions` ou `CreateSubscriptionOptions`. 

### <a name="azure-servicebus-legacy"></a>azure-servicebus (hérité)
Vous pouvez activer le transfert à l’aide de [QueueDescription.setForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.queuedescription.setforwardto#com_microsoft_azure_servicebus_management_QueueDescription_setForwardTo_java_lang_String_) ou [SubscriptionDescription.SetForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.subscriptiondescription.setforwardto) pour la source. 


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

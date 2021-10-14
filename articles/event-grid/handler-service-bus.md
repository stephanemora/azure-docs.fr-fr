---
title: Files d’attente et rubriques Service Bus en tant que gestionnaires d’événements pour des événements Azure Event Grid
description: Décrit comment vous pouvez utiliser des files d’attente et rubriques Service Bus en tant que gestionnaires d’événements pour des événements Azure Event Grid.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 676a9fcc6e260f2226e103ede26398b384e046ca
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354386"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus des files d’attente et des rubriques comme gestionnaires d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements et **Azure Service Bus** est l’un d’eux. 

Vous pouvez utiliser une file d’attente ou une rubrique du service en tant que gestionnaire pour des événements Event Grid. 

## <a name="service-bus-queues"></a>Files d’attente Service Bus

> [!NOTE]
> Les files d’attente activées pour la session ne sont pas prises en charge en tant que gestionnaires d’événements pour les événements Azure Event Grid
 
Vous pouvez router les événements Event Grid directement vers les files d’attente Service Bus. Cette fonctionnalité est utilisée pour les scénarios de mise en mémoire tampon ou de commande et de contrôle dans les applications d’entreprise.

Dans le portail Azure, lors de la création d’un abonnement aux événements, sélectionnez **File d’attente Service Bus** comme type de point de terminaison, puis cliquez sur **Sélectionner un point de terminaison** pour choisir une file d’attente Service Bus.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Utilisation de l’interface CLI pour ajouter un gestionnaire de files d’attente Service Bus

Dans Azure CLI, l’exemple suivant abonne et connecte une rubrique Event Grid à une file d’attente Service Bus :

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Rubriques Service Bus

Vous pouvez acheminer les événements dans Event Grid directement dans les rubriques Service Bus afin de gérer des événements système Azure à l’aide de ces dernières ou pour les scénarios de messagerie de commande et de contrôle.

Dans le portail Azure, lors de la création d’un abonnement aux événements, sélectionnez **Rubrique Service Bus** comme type de point de terminaison, puis cliquez sur **Sélectionner un point de terminaison** pour choisir une rubrique Service Bus.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Utilisation de l’interface CLI pour ajouter un gestionnaire de rubriques Service Bus

Dans Azure CLI, l’exemple suivant abonne et connecte une rubrique Event Grid à une rubrique Service Bus :

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]

Lors de l’envoi d’un événement à une file d’attente ou une rubrique Service Bus en tant que message réparti, le `messageid` du message réparti est un ID système interne.

L’ID système interne du message est conservé lors de la nouvelle remise de l’événement afin que vous puissiez éviter les remises en double en activant la **détection des doublons** sur l’entité Service Bus. Nous vous recommandons d’activer la durée de la détection des doublons sur l’entité Service Bus comme étant la durée de vie (TTL, Time-to-Live) de l’événement ou la durée maximale de nouvelle tentative, la valeur la plus longue étant retenue.

## <a name="rest-examples-for-put"></a>Exemples REST (pour PUT)

### <a name="service-bus-queue"></a>File d’attente Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>File d’attente Service Bus : remise avec une identité managée

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Rubrique Service Bus

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Rubrique Service Bus : remise avec une identité managée

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="delivery-properties"></a>Propriétés de remise
Les abonnements aux événements vous permettent de définir des en-têtes HTTP qui sont inclus dans les événements remis. Cette fonctionnalité vous permet de définir des en-têtes personnalisés requis par une destination. vous pouvez définir des en-têtes personnalisés sur les événements qui sont remis aux files d’attente et rubriques Azure Service Bus.

Azure Service Bus prend en charge l’utilisation des propriétés de message suivantes lors de l’envoi de messages uniques. 

| Nom de l’en-tête | Type d’en-tête |
| :-- | :-- |
| `MessageId` | dynamique |  
| `PartitionKey` | Statique ou dynamique |
| `SessionId` | Statique ou dynamique |
| `CorrelationId` | Statique ou dynamique |
| `Label` | Statique ou dynamique |
| `ReplyTo` | Statique ou dynamique | 
| `ReplyToSessionId` | Statique ou dynamique |
| `To` |Statique ou dynamique |
| `ViaPartitionKey` | Statique ou dynamique |

> [!NOTE]
> - La valeur par défaut de `MessageId` est l’ID interne de l’événement Event Grid. Vous pouvez la remplacer. Par exemple : `data.field`.
> - Vous pouvez définir uniquement `SessionId` ou `MessageId`. 

Pour plus d'informations, consultez [Propriétés de remise personnalisées](delivery-properties.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 

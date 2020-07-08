---
title: Files d’attente et rubriques Service Bus en tant que gestionnaires d’événements pour des événements Azure Event Grid
description: Décrit comment vous pouvez utiliser des files d’attente et rubriques Service Bus en tant que gestionnaires d’événements pour des événements Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c573f7ee088fe1d88f832623891377d4fd50bd4b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105691"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Service Bus des files d’attente et des rubriques comme gestionnaires d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements et **Azure Service Bus** est l’un d’eux. 

Vous pouvez utiliser une file d’attente ou une rubrique du service en tant que gestionnaire pour des événements Event Grid. 

## <a name="service-bus-queues"></a>Files d’attente Service Bus
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

Dans Azure CLI, l’exemple suivant abonne et connecte une rubrique Event Grid à une file d’attente Service Bus :

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>Propriétés du message
Si vous utilisez **une rubrique ou une file d’attente Service Bus** en tant que gestionnaire d’événements pour des événements d’Event Grid, définissez les en-têtes de message suivants : 

| Nom de la propriété | Description |
| ------------- | ----------- | 
| aeg-subscription-name | Nom de l’abonnement aux événements. |
| aeg-delivery-count | <p>Nombre de tentatives effectuées pour l’événement.</p> <p>Exemple : "1"</p> |
| aeg-event-type | <p>Type de l’événement.</p><p> Exemple : « Microsoft.Storage.blobCreated »</p> | 
| aeg-metadata-version | <p>Version de métadonnées de l’événement.</p> <p>Exemple : "1".</p><p> Pour un **schéma d’événement Event Grid**, cette propriété représente la version des métadonnées et, pour un **schéma d’événements cloud**, elle représente la **version des spécifications**. </p>|
| aeg-data-version | <p>Version de données de l’événement.</p><p>Exemple : "1".</p><p>Pour un **schéma d’événement Event Grid**, cette propriété représente la version des données et, pour un **schéma d’événements cloud**, elle ne s’applique pas.</p> |

## <a name="message-headers"></a>En-têtes de message
Lors de l’envoi d’un événement à une file d’attente ou une rubrique Service Bus en tant que message réparti, l’ID `messageid` du message réparti est l’**ID d’événement**.

L’ID d’événement est conservé lors de la nouvelle remise de l’événement afin que vous puissiez éviter les remises en double en activant la **détection des doublons** sur l’entité Service Bus. Nous vous recommandons d’activer la durée de la détection des doublons sur l’entité Service Bus comme étant la durée de vie (TTL, Time-to-Live) de l’événement ou la durée maximale de nouvelle tentative, la valeur la plus longue étant retenue.

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

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 

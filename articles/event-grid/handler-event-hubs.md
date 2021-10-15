---
title: Event Hub en tant que gestionnaire d’événements pour les événements Azure Event Grid
description: Décrit comment utiliser des concentrateurs d’événements en tant que gestionnaires d’événements pour des événements Azure Event Grid.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 590364d759fe6f628cfaa306d162d00f575c0622
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354415"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event Hub en tant que gestionnaire d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue une action pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements, et **Azure Event Hubs** est l’un d’eux. 

Utilisez **Event Hubs** si votre solution reçoit des événements d’Event Grid plus vite qu’elle ne peut les traiter. Une fois que les événements se trouvent dans un concentrateur d’événements, votre application peut traiter ces événements selon sa propre planification. Vous pouvez mettre à l’échelle votre système de traitement des événements de façon à gérer les événements entrants.

## <a name="tutorials"></a>Tutoriels
Regardez les exemples suivants : 

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements personnalisés vers Azure Event Hubs avec Azure CLI](custom-event-to-eventhub.md) | Envoie un événement personnalisé à un hub d’événements afin qu’il soit traité par une application. |
| [Modèle Resource Manager : Créer une rubrique personnalisée Event Grid et envoyer des événements à un concentrateur d’événements](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventgrid/event-grid-event-hubs-handler)| Modèle Resource Manager qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers Azure Event Hubs. |

[!INCLUDE [event-grid-message-headers](./includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>Exemples REST (pour PUT)


### <a name="event-hub"></a>Event Hub

```json
{
  "properties": 
  {
    "destination": 
    {
      "endpointType": "EventHub",
      "properties": 
      {
        "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Event Hub : remise avec une identité managée

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
        "endpointType": "EventHub",
        "properties": 
        {
          "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
        }
      }
    },
    "eventDeliverySchema": "EventGridSchema"
  }
}
```

## <a name="delivery-properties"></a>Propriétés de remise
Les abonnements aux événements vous permettent de définir des en-têtes HTTP qui sont inclus dans les événements remis. Cette fonctionnalité vous permet de définir des en-têtes personnalisés requis par une destination. Vous pouvez définir des en-têtes personnalisés sur les événements remis à Azure Event Hubs.

Si vous devez publier des événements dans une partition spécifique au sein d’un Event Hub, définissez une propriété `ParitionKey` sur votre abonnement aux événements pour spécifier la clé de partition qui identifie la partition Event Hub cible.

| Nom de l’en-tête | Type d’en-tête |
| :-- | :-- |
|`PartitionKey` | statique |

Pour plus d'informations, consultez [Propriétés de remise personnalisées](delivery-properties.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 

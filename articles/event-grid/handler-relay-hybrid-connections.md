---
title: Connexion hybride Relay en tant que gestionnaire d’événements pour des événements Azure Event Grid
description: Décrit comment utiliser des connexions hybrides Azure Relay en tant que gestionnaires d’événements pour des événements Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800881"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Connexion hybride Relay en tant que gestionnaire d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements, et **Azure Relay** est l’un d’eux. 

Utilisez des **connexions hybrides Azure Relay** pour envoyer des événements aux applications se trouvant dans un réseau d’entreprise et qui ne disposent pas d’un point de terminaison accessible publiquement.

## <a name="tutorials"></a>Tutoriels
Consultez le tutoriel suivant pour obtenir un exemple d’utilisation d’une connexion hybride Azure Relay en tant que gestionnaire d’événements. 

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Envoyer des événements à une connexion hybride](custom-event-to-hybrid-connection.md) | Envoie un événement personnalisé vers une connexion hybride existante pour son traitement par une application d’écouteur. |

## <a name="rest-example-for-put"></a>Exemple REST (pour PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 
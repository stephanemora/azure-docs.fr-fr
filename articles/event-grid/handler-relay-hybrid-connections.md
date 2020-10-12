---
title: Connexion hybride Relay en tant que gestionnaire d’événements pour des événements Azure Event Grid
description: Décrit comment utiliser des connexions hybrides Azure Relay en tant que gestionnaires d’événements pour des événements Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3807e2d125d652b8f5ed7c9dec1b972d69f699f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270200"
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

> [!NOTE]
> La transmission d’événements à une connexion hybride Azure Relay dans **un autre locataire** n’est pas prise en charge. 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 
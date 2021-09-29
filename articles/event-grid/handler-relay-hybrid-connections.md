---
title: Connexion hybride Relay en tant que gestionnaire d’événements pour des événements Azure Event Grid
description: Décrit comment utiliser des connexions hybrides Azure Relay en tant que gestionnaires d’événements pour des événements Azure Event Grid.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: fb36e391663ee53729175a2768d765521b03f4da
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214937"
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
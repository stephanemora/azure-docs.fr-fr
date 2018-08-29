---
title: Schéma d’événements de hubs d’événements Azure Event Grid
description: Décrit les propriétés qui sont fournies pour les événements de hubs d’événements avec Azure Event Grid.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: e301f3895126ed52b8d4c1f046f69dfcedb3563c
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141290"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schéma des événements Azure Event Grid des hubs d’événements

Cet article fournit les propriétés et le schéma des événements de hubs d’événements. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [Source d’événement Event Hubs](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Types d’événement disponibles

Event Hubs émet le type d’événement **Microsoft.EventHub.CaptureFileCreated** quand un fichier de capture est créé.

## <a name="example-event"></a>Exemple d’événement

Cet exemple d’événement montre le schéma d’un événement de hubs d’événements déclenché quand la fonctionnalité Capture stocke un fichier : 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | chaîne | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement de hub d’événement. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| fileUrl | chaîne | Chemin du fichier de capture. |
| fileType | chaîne | Type du fichier de capture. |
| partitionId | chaîne | ID de partition. |
| sizeInBytes | integer | Taille du fichier. |
| eventCount | integer | Nombre d’événements dans le fichier. |
| firstSequenceNumber | integer | Plus petit numéro de séquence de la file d’attente. |
| lastSequenceNumber | integer | Dernier numéro de séquence de la file d’attente. |
| firstEnqueueTime | chaîne | Première heure de la file d’attente. |
| lastEnqueueTime | chaîne | Dernière heure de la file d’attente. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur la gestion des événements de hubs d’événements, consultez [Diffuser des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md).
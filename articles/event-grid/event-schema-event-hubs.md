---
title: Schéma d’événements de hubs d’événements Azure Event Grid
description: Décrit les propriétés qui sont fournies pour les événements de hubs d’événements avec Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561826"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schéma des événements Azure Event Grid des hubs d’événements

Cet article fournit les propriétés et le schéma des événements de hubs d’événements. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

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

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| topic | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| data | object | Données d’événement de hub d’événement. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| fileUrl | string | Chemin du fichier de capture. |
| fileType | string | Type du fichier de capture. |
| partitionId | string | ID de partition. |
| sizeInBytes | entier | Taille du fichier. |
| eventCount | entier | Nombre d’événements dans le fichier. |
| firstSequenceNumber | entier | Plus petit numéro de séquence de la file d’attente. |
| lastSequenceNumber | entier | Dernier numéro de séquence de la file d’attente. |
| firstEnqueueTime | string | Première heure de la file d’attente. |
| lastEnqueueTime | string | Dernière heure de la file d’attente. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur la gestion des événements de hubs d’événements, consultez [Diffuser des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md).
---
title: Schéma d’événements de stockage Blob Azure Event Grid
description: Décrit les propriétés fournies pour les événements de stockage Blob avec Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614906"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schéma d’événements Azure Event Grid pour le stockage Blob

Cet article fournit les propriétés et les schémas des événements de stockage Blob. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [Source d’événement Stockage](event-sources.md#storage).

## <a name="available-event-types"></a>Types d’événement disponibles

Le stockage Blob émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Déclenché lorsqu’un objet blob est créé. |
| Microsoft.Storage.BlobDeleted | Déclenché lorsqu’un objet blob est supprimé. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement de création d’objet blob : 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Le schéma de l’événement de suppression d’un objet blob est similaire : 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
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
| data | objet | Données d’événement de stockage Blob. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| api | string | Opération qui a déclenché l’événement. |
| clientRequestId | string | Valeur opaque générée par le client, avec une limite de caractères de 1 Ko. Lorsque vous activez la journalisation Storage Analytics, cette valeur est enregistrée dans les journaux d’activité d’analytique. |
| requestId | string | Identificateur unique de la requête. À utiliser pour le dépannage de la requête. |
| eTag | string | Valeur que vous pouvez utiliser pour effectuer des opérations de manière conditionnelle. |
| contentType | string | Type de contenu spécifié pour l’objet blob. |
| contentLength | integer | Taille de l’objet blob en octets. |
| blobType | string | Type d’objet blob. Les valeurs valides sont « BlockBlob » ou « PageBlob ». |
| url | string | Chemin de l’objet blob. |
| sequencer | string | Valeur contrôlée par l’utilisateur que vous pouvez utiliser pour effectuer le suivi des requêtes. |
| storageDiagnostics | objet | Informations sur les diagnostics de stockage. |
 
## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur l’utilisation des événements de stockage Blob, consultez [Itinéraire d’événements de stockage Blob - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

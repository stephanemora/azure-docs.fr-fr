---
title: Schéma d’événements de stockage Blob Azure Event Grid
description: Décrit les propriétés fournies pour les événements de stockage Blob avec Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79231333"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schéma d’événements Azure Event Grid pour le stockage Blob

Cet article fournit les propriétés et les schémas des événements de stockage Blob. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [Source d’événement Stockage](event-sources.md#storage).

>[!NOTE]
> Seuls les comptes de stockage de type **StorageV2 (usage général v2)** et **BlobStorage** prennent en charge l’intégration d’événements. **Le stockage (usage général v1)** ne prend *pas* en charge l’intégration à Event Grid.

## <a name="list-of-events-for-blob-rest-apis"></a>Liste des événements pour des API REST d’objets blob

Ces événements sont déclenchés quand un client crée, remplace ou supprime un objet blob en appelant des API REST d’objets blob.

 |Nom d'événement |Description|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Déclenché quand un objet blob est créé ou remplacé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent les opérations `PutBlob`, `PutBlockList` ou `CopyBlob` qui sont disponibles dans l’API REST d’objet blob.   |
 |**Microsoft.Storage.BlobDeleted** |Déclenché quand un objet blob est supprimé. <br>Plus précisément, cet événement est déclenché quand des clients appellent l’opération `DeleteBlob` qui est disponible dans l’API REST d’objet blob. |

> [!NOTE]
> Si vous voulez vérifier que l’événement **Microsoft.Storage.BlobCreated** est déclenché uniquement quand un objet blob de blocs est entièrement validé, filtrez l’événement pour les appels d’API REST `CopyBlob`, `PutBlob` et `PutBlockList`. Ces appels d’API déclenchent l’événement **Microsoft.Storage.BlobCreated** uniquement quand les données sont entièrement validées dans un objet blob de blocs. Pour savoir comment créer un filtre, consultez [Filtrer des événements pour Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Liste des événements pour les API REST Azure Data Lake Storage Gen2

Ces événements sont déclenchés si vous activez un espace de noms hiérarchique sur le compte de stockage et que des clients appellent des API REST Azure Data Lake Storage Gen2.

|Nom d'événement|Description|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Déclenché quand un objet blob est créé ou remplacé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent les opérations `CreateFile` et `FlushWithClose` qui sont disponibles dans l’API REST Azure Data Lake Storage Gen2. |
|**Microsoft.Storage.BlobDeleted** |Déclenché quand un objet blob est supprimé. <br>Plus précisément, cet événement est également déclenché quand des clients appellent l’opération `DeleteFile` qui est disponible dans l’API REST Azure Data Lake Storage Gen2. |
|**Microsoft.Storage.BlobRenamed**|Déclenché quand un objet blob est renommé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent l’opération `RenameFile` qui est disponible dans l’API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryCreated**|Déclenché quand un répertoire est créé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent l’opération `CreateDirectory` qui est disponible dans l’API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryRenamed**|Déclenché quand un répertoire est renommé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent l’opération `RenameDirectory` qui est disponible dans l’API REST Azure Data Lake Storage Gen2.|
|**Microsoft.Storage.DirectoryDeleted**|Déclenché quand un répertoire est supprimé. <br>Plus précisément, cet événement est déclenché quand des clients utilisent l’opération `DeleteDirectory` qui est disponible dans l’API REST Azure Data Lake Storage Gen2.|

> [!NOTE]
> Si vous voulez vérifier que l’événement **Microsoft.Storage.BlobCreated** est déclenché uniquement quand un objet blob de blocs est entièrement validé, filtrez l’événement pour l’appel d’API REST `FlushWithClose`. Cet appel d’API déclenche l’événement **Microsoft.Storage.BlobCreated** uniquement quand les données sont entièrement validées dans un objet blob de blocs. Pour savoir comment créer un filtre, consultez [Filtrer des événements pour Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Le contenu d’une réponse à un événement

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement de stockage d’objet blob.

### <a name="microsoftstorageblobcreated-event"></a>Événement Microsoft.Storage.BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Événement Microsoft.Storage.BlobCreated (Data Lake Storage Gen2)

Si le compte de stockage d’objets blob a un espace de noms hiérarchique, les données sont similaires à l’exemple précédent à l’exception des changements suivants :

* La clé `dataVersion` est définie sur la valeur `2`.

* La clé `data.api` est définie sur la chaîne `CreateFile` ou `FlushWithClose`.

* La clé `contentOffset` est incluse dans le jeu de données.

> [!NOTE]
> Si des applications utilisent l’opération `PutBlockList` pour charger un nouvel objet blob dans le compte, les données ne contiennent pas ces changements.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Événement Microsoft.Storage.BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Événement Microsoft.Storage.BlobDeleted (Data Lake Storage Gen2)

Si le compte de stockage d’objets blob a un espace de noms hiérarchique, les données sont similaires à l’exemple précédent à l’exception des changements suivants :

* La clé `dataVersion` est définie sur la valeur `2`.

* La clé `data.api` est définie sur la chaîne `DeleteFile`.

* La clé `url` contient le chemin `dfs.core.windows.net`.

> [!NOTE]
> Si des applications utilisent l’opération `DeleteBlob` pour supprimer un objet blob du compte, les données ne contiennent pas ces changements.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Événement Microsoft.Storage.BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Événement Microsoft.Storage.DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Événement Microsoft.Storage.DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Événement Microsoft.Storage.DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
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
| data | object | Données d’événement de stockage Blob. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| api | string | Opération qui a déclenché l’événement. |
| clientRequestId | string | ID de requête fourni par le client pour l’opération d’API de stockage. Il peut être utilisé pour mettre en corrélation les journaux de diagnostic du Stockage Azure en utilisant le champ « client-request-id » dans les journaux et peut être fourni dans des demandes du client à l’aide de l’en-tête « x-ms-client-request-id ». Consultez [Format de journal](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | L’ID de requête de service généré pour l’opération de l’API de stockage. Peut être utilisé pour mettre en corrélation les journaux de diagnostic de stockage Azure en utilisant le champ « request-id-header » dans les journaux d’activité et est retourné lors de l’initialisation de l’appel d’API dans l’en-tête ’x-ms-request-id’. Consultez [Format de journal](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Valeur que vous pouvez utiliser pour effectuer des opérations de manière conditionnelle. |
| contentType | string | Type de contenu spécifié pour l’objet blob. |
| contentLength | entier | Taille de l’objet blob en octets. |
| blobType | string | Type d’objet blob. Les valeurs valides sont « BlockBlob » ou « PageBlob ». |
| contentOffset | nombre | Décalage, en octets, d’une opération d’écriture effectuée au point où l’application de déclenchement d’événement a effectué l’écriture dans le fichier. <br>Apparaît uniquement pour les événements déclenchés sur les comptes de stockage d’objets blob qui ont un espace de noms hiérarchique.|
| destinationUrl |string | URL du fichier qui existera une fois l’opération terminée. Par exemple, si un fichier est renommé, la propriété `destinationUrl` contient l’URL du nouveau nom de fichier. <br>Apparaît uniquement pour les événements déclenchés sur les comptes de stockage d’objets blob qui ont un espace de noms hiérarchique.|
| sourceUrl |string | URL du fichier qui existe avant l’opération. Par exemple, si un fichier est renommé, `sourceUrl` contient l’URL du nom de fichier d’origine avant l’opération de renommage. <br>Apparaît uniquement pour les événements déclenchés sur les comptes de stockage d’objets blob qui ont un espace de noms hiérarchique. |
| url | string | Chemin de l’objet blob. <br>Si le client utilise une API REST d’objet blob, l’URL présente la structure suivante : *\<nom-compte-stockage\>.blob.core.windows.net/\<nom-conteneur\>/\<nom-fichier\>* . <br>Si le client utilise une API REST Data Lake Storage, l’URL présente la structure suivante : *\<nom-compte-stockage\>.dfs.core.windows.net/\<nom-système-fichiers\>/\<nom-fichier\>* . |
| recursive | string | `True` pour effectuer l’opération sur tous les répertoires enfants ; sinon, `False`. <br>Apparaît uniquement pour les événements déclenchés sur les comptes de stockage d’objets blob qui ont un espace de noms hiérarchique. |
| sequencer | string | Une valeur de chaîne opaque représentant l’ordre logique des événements pour n’importe quel nom d’objet Blob particulier.  Les utilisateurs peuvent utiliser la comparaison de chaînes standard pour comprendre l’ordre relatif de deux événements sur le même nom d’objet Blob. |
| storageDiagnostics | object | Des données de diagnostic occasionnellement incluses par le service de stockage Azure. Elles doivent, le cas échéant, être ignorées par les consommateurs d’événements. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour plus d’informations sur l’utilisation des événements de stockage Blob, consultez [Itinéraire d’événements de stockage Blob - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

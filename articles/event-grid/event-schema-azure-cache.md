---
title: Azure Cache pour Redis comme source d’Event Grid
description: Décrit les propriétés qui sont fournies pour les événements Azure Cache pour Redis avec Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
author: curib
ms.author: cauribeg
ms.openlocfilehash: 1a2995bc9ef40cd4eab320ce1bb4c5faf61e0e6e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371275"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure Cache pour Redis comme source d’Event Grid

Cet article fournit les propriétés et les schémas des événements Azure Cache pour Redis.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). 

## <a name="available-event-types"></a>Types d’événement disponibles
Ces événements sont déclenchés lorsqu’un client exporte, importe ou met à l’échelle en appelant les API REST Azure Cache pour Redis. L’événement de mise à jour corrective est déclenché par la mise à jour de Redis.

 |Nom d'événement |Description|
 |----------|-----------|
 |**Microsoft.Cache.ExportRDBCompleted** |Déclenché lorsque les données du cache sont exportées. |
 |**Microsoft.Cache.ImportRDBCompleted** |Déclenché lorsque les données du cache sont importées. |
 |**Microsoft.Cache.PatchingCompleted** |Déclenché lorsque la mise à jour corrective est terminée. |
 |**Microsoft.Cache.ScalingCompleted** |Déclenché lorsque la mise à l’échelle est terminée. |

## <a name="example-event"></a>Exemple d’événement
Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement. Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement Azure Cache pour Redis.

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

### <a name="microsoftcachepatchingcompleted-event"></a>Événement Microsoft.Cache.PatchingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Événement Microsoft.Cache.ImportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Événement Microsoft.Cache.ExportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Événement Microsoft.Cache.ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)


### <a name="microsoftcachepatchingcompleted-event"></a>Événement Microsoft.Cache.PatchingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.PatchingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "PatchingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "PatchingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Événement Microsoft.Cache.ImportRDBCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ImportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ImportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ImportRDBCompleted",
    "eventTime": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Événement Microsoft.Cache.ExportRDBCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ExportRDBCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ExportRDBCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ExportRDBCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

### <a name="microsoftcachescalingcompleted"></a>Événement Microsoft.Cache.ScalingCompleted

```json
[{
    "id": "9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
    "type": "Microsoft.Cache.ScalingCompleted",
    "source": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
    "data": {
        "name": "ScalingCompleted",
        "timestamp": "2020-12-09T21:50:19.9995668+00:00",
        "status": "Succeeded"
    },
    "subject": "ScalingCompleted",
    "time": "2020-12-09T21:50:19.9995668+00:00",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Propriétés d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `topic` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement Azure Cache pour Redis. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |


# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)


Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `source` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| `type` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `time` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement Azure Cache pour Redis. |
| `specversion` | string | Version de la spécification de schéma CloudEvents. |

---


L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `timestamp` | string | Heure à laquelle l’événement s’est produit. |
| `name` | string | Nom de l’événement. |
| `status` | string | État de l’événement. Échec ou réussi. |

## <a name="quickstarts"></a>Démarrages rapides

Si vous souhaitez essayer les événements Azure Cache pour Redis, consultez l’un des articles de démarrage rapide suivants :

|Si vous souhaitez utiliser cet outil :    |Lisez l’article : |
|--|-|
|Portail Azure    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec le portail Azure](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).


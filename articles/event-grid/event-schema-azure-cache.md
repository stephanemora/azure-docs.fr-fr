---
title: Azure Cache pour Redis comme source d’Event Grid
description: Décrit les propriétés qui sont fournies pour les événements Azure Cache pour Redis avec Azure Event Grid
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056788"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure Cache pour Redis comme source d’Event Grid

Cet article fournit les propriétés et les schémas des événements Azure Cache pour Redis.  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schéma d’événement Event Grid

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Liste d’événements pour les API REST Azure Cache pour Redis

Ces événements sont déclenchés lorsqu’un client exporte, importe ou met à l’échelle en appelant les API REST Azure Cache pour Redis. L’événement de mise à jour corrective est déclenché par la mise à jour de Redis.

 |Nom d'événement |Description|
 |----------|-----------|
 |**Microsoft.Cache.ExportRDBCompleted** |Déclenché lorsque les données du cache sont exportées. |
 |**Microsoft.Cache.ImportRDBCompleted** |Déclenché lorsque les données du cache sont importées. |
 |**Microsoft.Cache.PatchingCompleted** |Déclenché lorsque la mise à jour corrective est terminée. |
 |**Microsoft.Cache.ScalingCompleted** |Déclenché lorsque la mise à l’échelle est terminée. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Le contenu d’une réponse à un événement

Quand un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison d’abonnement.

Cette section contient un exemple de ce à quoi ces données ressembleraient pour chaque événement Azure Cache pour Redis.

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

### <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| topic | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| data | object | Données d’événement Azure Cache pour Redis. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| timestamp | string | Heure à laquelle l’événement s’est produit. |
| name | string | Nom de l’événement. |
| status | string | État de l’événement. Échec ou réussi. |


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


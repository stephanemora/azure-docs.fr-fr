---
title: Schéma d’événements pour Azure App Configuration dans Azure Event Grid
description: Décrit les propriétés fournies pour les événements Azure App Configuration avec Azure Event Grid.
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735780"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schéma des événements Azure Event Grid pour Azure App Configuration

Cet article fournit les propriétés et le schéma des événements Azure App Configuration. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des tutoriels, consultez la section relative à la [source d’événements Azure App Configuration](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Types d’événement disponibles

Azure App Configuration propose les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Émis lorsqu’une valeur de clé est créée ou remplacée. |
| Microsoft.AppConfiguration.KeyValueDeleted | Émis lorsqu’une valeur de clé est supprimée. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement de valeur de clé modifié : 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Le schéma d’un événement de valeur de clé supprimé est similaire : 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
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
| data | object | Données de l’événement App Configuration. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| key | string | La clé de la paire clé-valeur qui a été modifiée ou supprimée. |
| label | string | Le cas échéant, l’étiquette de la paire clé-valeur qui a été modifiée ou supprimée. |
| etag | string | Pour le paramètre `KeyValueModified`, l’etag de la nouvelle paire clé-valeur. Pour le paramètre `KeyValueDeleted`, l’etag de la paire clé-valeur qui a été supprimée. |
 
## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour découvrir les événements Azure App Configuration et savoir comment les utiliser, voir [Démarrage rapide : Router des événements Azure App Configuration vers un point de terminaison web avec Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
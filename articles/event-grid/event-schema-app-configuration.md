---
title: Schéma d’événements Azure Event Grid Configuration de l’application Azure
description: Décrit les propriétés qui sont fournies pour les événements de Configuration de l’application Azure avec Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735780"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schéma d’événement Azure Event Grid pour la Configuration d’application Azure

Cet article fournit les propriétés et un schéma pour les événements de Configuration de l’application Azure. Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](event-schema.md).

Pour obtenir la liste des exemples de scripts et des didacticiels, consultez [source d’événements de Configuration de l’application Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Types d’événement disponibles

Configuration de l’application Azure émet des types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Déclenché lorsqu’une valeur de clé est créée ou remplacée. |
| Microsoft.AppConfiguration.KeyValueDeleted | Déclenché lorsqu’une valeur de clé est supprimée. |

## <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement de modification de clé-valeur : 

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

Le schéma pour un événement deleted clé-valeur est similaire : 

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
| data | objet | Données d’événement App Configuration. |
| dataVersion | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| key | string | La clé de la valeur de clé qui a été modifiée ou supprimée. |
| label | string | L’étiquette, le cas échéant, de la valeur de clé qui a été modifiée ou supprimée. |
| etag | string | Pour `KeyValueModified` l’etag de la nouvelle valeur de clé. Pour `KeyValueDeleted` l’etag de la valeur de clé qui a été supprimée. |
 
## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
* Pour une introduction à l’utilisation des événements de Configuration de l’application Azure, consultez [événements de Configuration de l’application Azure Route - Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
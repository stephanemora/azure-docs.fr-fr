---
title: Gestion des API comme source Event Grid
description: Cet article explique comment utiliser la Gestion des API Azure comme source d’événement Event Grid. Il fournit le schéma et des liens vers des articles de guide pratique.
ms.topic: conceptual
author: dlepow
ms.author: danlep
ms.date: 07/12/2021
ms.openlocfilehash: c14107561886a9e29c2d95c5d04847274afdf4e3
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733946"
---
# <a name="azure-api-management-as-an-event-grid-source-preview"></a>Gestion des API Azure comme source Event Grid (préversion)

Cet article fournit les propriétés et le schéma des événements de [Gestion des API Azure](../api-management/index.yml).  Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](./event-schema.md). Vous y trouverez également des liens vers des articles expliquant comment utiliser la Gestion des API comme source d’événement.

## <a name="available-event-types"></a>Types d’événement disponibles

La Gestion des API émet les types d’événements suivants :

| Type d’événement | Description |
| ---------- | ----------- |
| Microsoft.APIManagement.UserCreated | Déclenché quand un utilisateur est créé. |
| Microsoft.APIManagement.UserUpdated | Déclenché quand un utilisateur est mis à jour. |
| Microsoft.APIManagement.UserDeleted | Déclenché quand un utilisateur est supprimé. |
| Microsoft.APIManagement.APICreated | Déclenché quand une API est créée. |
| Microsoft.APIManagement.APIUpdated | Déclenché quand une API est mise à jour. |
| Microsoft.APIManagement.APIDeleted | Déclenché quand une API est supprimée. |
| Microsoft.APIManagement.ProductCreated | Déclenché quand un produit est créé. |
| Microsoft.APIManagement.ProductUpdated | Déclenché quand un produit est mis à jour. |
| Microsoft.APIManagement.ProductDeleted | Déclenché quand un produit est supprimé. |
| Microsoft.APIManagement.ReleaseCreated | Déclenché quand une version d’API est créée. |
| Microsoft.APIManagement.ReleaseUpdated | Déclenché quand une version d’API est mise à jour. |
| Microsoft.APIManagement.ReleaseDeleted | Déclenché quand une version d’API est supprimée. |
| Microsoft.APIManagement.SubscriptionCreated | Déclenché quand un abonnement est créé. |
| Microsoft.APIManagement.SubscriptionUpdated | Déclenché quand un abonnement est mis à jour. |
| Microsoft.APIManagement.SubscriptionDeleted | Déclenché quand un abonnement est supprimé. |

## <a name="example-event"></a>Exemple d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)
L’exemple suivant montre le schéma d’un événement de création d’un produit. Le schéma des autres événements de création d’une ressource de Gestion des API est similaire.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "eventType": "Microsoft.ApiManagement.ProductCreated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

L’exemple suivant montre le schéma d’un événement de création d’un produit. Le schéma des autres événements de création d’une ressource de Gestion des API est similaire. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/products/myproduct",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/products/myproduct"
  },
  "Type": "Microsoft.ApiManagement.ProductCreated",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)
L’exemple suivant montre le schéma d’un événement de suppression d’un utilisateur. Le schéma des autres événements de suppression d’une ressource de Gestion des API est similaire.

```json
[{
  "id": "92c502f2-a966-42a7-a428-d3b319844544",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "eventType": "Microsoft.ApiManagement.UserDeleted",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-02T00:47:47.8536532Z"
}]
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

L’exemple suivant montre le schéma d’un événement de suppression d’un utilisateur. Le schéma des autres événements de suppression d’une ressource de Gestion des API est similaire. 

```json
[{
  "id": "81dac958-49cf-487e-8805-d0baf0ee485a",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/users/apimuser-contoso-com",
  "data": {
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/users/apimuser-contoso-com"
  },
  "Type": "Microsoft.ApiManagement.UserDeleted",
  "Time": "2021-07-02T00:38:44.3978295Z",
  "specversion":"1.0"
}]
```
---

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

L’exemple suivant montre le schéma d’un événement de mise à jour d’une API. La propriété `data` comprend à la fois le tableau `updatedProperies` et la valeur `resourceUri`.  Le schéma des autres événements de mise à jour d’une ressource de Gestion des API est similaire. 
```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "eventType": "Microsoft.ApiManagement.APIUpdated",
  "dataVersion": "1",
  "metadataVersion": "1",
  "eventTime": "2021-07-12T23:13:44.9048323Z"
}]
```

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

L’exemple suivant montre le schéma d’un événement de mise à jour d’une API. La propriété `data` comprend à la fois le tableau `updatedProperies` et la valeur `resourceUri`.  Le schéma des autres événements de mise à jour d’une ressource de Gestion des API est similaire. 

```json
[{
  "id": "95015754-aa51-4eb6-98d9-9ee322b82ad7",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}",
  "subject": "/apis/myapi;Rev=1",
  "data": {
    "updatedProperties": [
      "path"
    ],
    "resourceUri": "/subscriptions/subscription-id}/resourceGroups/{your-rg}/providers/Microsoft.ApiManagement/service/{your-APIM-instance}/apis/myapi;Rev=1"
  },
  "Type": "Microsoft.ApiManagement.APIUpdated",
  "Time": "2021-07-12T23:13:44.9048323Z",
  "specversion":1.0
}]
```
---

## <a name="event-properties"></a>Propriétés d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `topic` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Identifiant complet de la ressource concernée par le changement de l’état de conformité, y compris le nom et le type de la ressource. Utilise le format, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement de Gestion des API. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `source` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Identifiant complet de la ressource concernée par le changement de l’état de conformité, y compris le nom et le type de la ressource. Utilise le format, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `time` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement de Gestion des API. |
| `specversion` | string | Version de la spécification de schéma CloudEvents. |

---

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `resourceUri` | string | URI de la ressource de Gestion des API qui a déclenché l’événement. |
| `updatedProperties` | string[] | Liste des propriétés mises à jour dans la ressource de Gestion des API qui ont déclenché un événement de mise à jour. |

## <a name="tutorials-and-how-tos"></a>Tutoriels et guides pratiques

|Intitulé  |Description  |
|---------|---------|
| [Envoyer les événements de la Gestion des API à Event Grid](../api-management/how-to-event-grid.md)| Comment s’abonner à des événements de Gestion des API à l’aide d’Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](./overview.md).
- Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](./subscription-creation-schema.md).



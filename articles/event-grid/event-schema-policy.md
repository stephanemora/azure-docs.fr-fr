---
title: Azure Policy en tant que source Event Grid
description: Cet article explique comment utiliser Azure Policy en tant que source d’événement Event Grid. Il fournit le schéma et des liens vers des articles de procédure et des tutoriels.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734906"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy en tant que source Event Grid

Cet article fournit les propriétés et les schémas des événements [Azure Policy](../governance/policy/index.yml). Pour une présentation des schémas d’événements, consultez [Schéma d’événements Azure Event Grid](./event-schema.md). Cet article fournit également une liste de guides de démarrage rapide et de tutoriels permettant d’utiliser Azure Policy en tant que source d’événement.

## <a name="available-event-types"></a>Types d’événement disponibles

Azure Policy émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | Déclenché lorsqu'un état de conformité à une stratégie est créé. |
| Microsoft.PolicyInsights.PolicyStateChanged | Déclenché lorsqu'un état de conformité à une stratégie est modifié. |
| Microsoft.PolicyInsights.PolicyStateDeleted | Déclenché lorsqu'un état de conformité à une stratégie est supprimé. |

## <a name="example-event"></a>Exemple d’événement

# <a name="event-grid-event-schema"></a>[Schéma d’événement Event Grid](#tab/event-grid-event-schema)
L’exemple suivant montre le schéma d’un événement de création de l’état de la stratégie : 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Le schéma d’un événement de modification de l’état de la stratégie est similaire : 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

L’exemple suivant montre le schéma d’un événement de création de l’état de la stratégie : 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Le schéma d’un événement de modification de l’état de la stratégie est similaire : 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
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
| `subject` | string | Identifiant complet de la ressource concernée par le changement de l'état de conformité, y compris le nom et le type de la ressource. Utilise le format, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement Azure Policy. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

# <a name="cloud-event-schema"></a>[Schéma d’événement cloud](#tab/cloud-event-schema)

Un événement contient les données générales suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `source` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Identifiant complet de la ressource concernée par le changement de l'état de conformité, y compris le nom et le type de la ressource. Utilise le format, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `time` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `id` | string | Identificateur unique de l’événement. |
| `data` | object | Données d’événement Azure Policy. |
| `specversion` | string | Version de la spécification de schéma CloudEvents. |

---

L’objet de données comporte les propriétés suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `timestamp` | string | Heure (UTC) à laquelle la ressource a été analysée par Azure Policy. Pour trier les événements, utilisez cette propriété à la place des propriétés `eventTime` ou `time` de niveau supérieur. |
| `policyAssignmentId` | string | ID de ressource de l’attribution de stratégie. |
| `policyDefinitionId` | string | ID de ressource de la définition de stratégie. |
| `policyDefinitionReferenceId` | string | ID de référence de la définition de stratégie dans la définition de l’initiative, si l’attribution de la stratégie concerne une initiative. Peut être vide. |
| `complianceState` | string | État de conformité de la ressource par rapport à l’attribution de stratégie. |
| `subscriptionId` | string | ID d’abonnement de la ressource. |
| `complianceReasonCode` | string | Code de raison de la conformité. Peut être vide. |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir un routage de procédure pas à pas Azure Policy des événements de changement d’état, consultez [Utiliser Event Grid pour les notifications de changement d’état de stratégie](../governance/policy/tutorials/route-state-change-events.md).
- Pour obtenir une vue d’ensemble de l’intégration d’Azure Policy avec Event Grid, consultez [Réagir à des événements Azure Policy avec Event Grid](../governance/policy/concepts/event-overview.md).
- Pour une présentation d’Azure Event Grid, consultez [Présentation d’Event Grid](./overview.md).
- Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](./subscription-creation-schema.md).
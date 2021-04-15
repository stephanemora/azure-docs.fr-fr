---
title: Réaction aux événements de changement d’état Azure Policy
description: Utilisez Azure Event Grid pour vous abonner aux événements Azure Policy, qui permettent aux applications de réagir aux changements d’état sans nécessiter de code complexe.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734911"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Réaction aux événements de changement d’état Azure Policy

Les événements Azure Policy permettent aux applications de réagir aux changements d’état. Cette intégration se fait sans avoir recours à un code complexe ni à des services d’interrogation coûteux et inefficaces. Au lieu de cela, les événements sont envoyés (push) via [Azure Event Grid](../../../event-grid/index.yml) aux abonnés, comme [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml), ou même à votre propre écouteur HTTP personnalisé.
Vous paierez uniquement pour ce que vous utiliserez.

Les événements Azure Policy sont envoyés à Azure Event Grid, qui fournit des services de livraison fiables à vos applications via des stratégies enrichies de nouvelle tentative et de livraison de lettres mortes. Pour plus d’informations, consultez [Distribution et nouvelle tentative de distribution de messages avec Azure Grid](../../../event-grid/delivery-and-retry.md).

Le scénario d’événement Azure Policy le plus courant est le suivi du changement de l’état de conformité d’une ressource pendant l’évaluation d’une stratégie. L’architecture basée sur les événements est un moyen efficace de réagir à ces changements au lieu d’analyser l’état de conformité des ressources selon une planification fixe.

> [!NOTE]
> Les événements de changement d’état d’Azure Policy sont envoyés à Event Grid après qu’un [déclencheur d’évaluation](../how-to/get-compliance-data.md#evaluation-triggers) a terminé l’évaluation des ressources.

Pour accéder au tutoriel complet, consultez [Acheminer les événements de changement d’état de stratégie à Event Grid avec Azure CLI](../tutorials/route-state-change-events.md).

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Modèle Event Grid de sources et de gestionnaires" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Événements Azure Policy disponibles

Event Grid utilise les [abonnements aux événements](../../../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Les abonnements aux événements Azure Policy peuvent inclure trois types d’événements :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | Déclenché lorsqu’un état de conformité à une stratégie est créé. |
| Microsoft.PolicyInsights.PolicyStateChanged | Déclenché lorsqu’un état de conformité à une stratégie est modifié. |
| Microsoft.PolicyInsights.PolicyStateDeleted | Déclenché lorsqu’un état de conformité à une stratégie est supprimé. |

## <a name="event-schema"></a>Schéma d’événement

Les événements Azure Policy contiennent toutes les informations dont vous avez besoin pour répondre aux changements apportés à vos données. Vous pouvez identifier un événement Azure Policy lorsque la propriété `eventType` commence par « Microsoft.PolicyInsights ».
Plus d’informations sur l’utilisation des propriétés d’événement Event Grid sont documentées dans  
[Schéma d’événement Event Grid](../../../event-grid/event-schema.md).

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| `id` | string | Identificateur unique de l’événement. |
| `topic` | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. Event Grid fournit cette valeur. |
| `subject` | string | Identifiant complet de la ressource concernée par le changement de l’état de conformité, y compris le nom et le type de la ressource. Utilise le format `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>`. |
| `data` | object | Données d’événement Azure Policy. |
| `data.timestamp` | string | Heure (UTC) à laquelle la ressource a été analysée par Azure Policy. Pour trier les événements, utilisez cette propriété à la place des propriétés `eventTime` ou `time` de niveau supérieur. |
| `data.policyAssignmentId` | string | ID de ressource de l’attribution de stratégie. |
| `data.policyDefinitionId` | string | ID de ressource de la définition de stratégie. |
| `data.policyDefinitionReferenceId` | string | ID de référence de la définition de stratégie dans la définition de l’initiative, si l’attribution de la stratégie concerne une initiative. Peut être vide. |
| `data.complianceState` | string | État de conformité de la ressource par rapport à l’attribution de stratégie. |
| `data.subscriptionId` | string | ID d’abonnement de la ressource. |
| `data.complianceReasonCode` | string | Code de raison de la conformité. Peut être vide. |
| `eventType` | string | Un des types d’événements inscrits pour cette source d’événement. |
| `eventTime` | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| `dataVersion` | string | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| `metadataVersion` | string | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

Voici un exemple d’événement de changement d’état de la stratégie :

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

Pour plus d’informations, consultez [Schéma des événements Azure Policy](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Pratiques pour la consommation d’événements

Les applications qui gèrent les événements Azure Policy doivent respecter les pratiques recommandées :

> [!div class="checklist"]
> - Plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements. Il ne faut donc pas supposer que les événements proviennent d’une source particulière. Au lieu de cela, consultez le sujet du message pour vérifier que l’attribution de stratégie, la définition de stratégie et la ressource auxquelles l’événement de changement d’état est destiné.
> - Vérifiez le `eventType` et ne supposez pas que tous les événements que vous recevez sont les types que vous attendez.
> - Utilisez `data.timestamp` pour déterminer l’ordre des événements dans Azure Policy, au lieu des propriétés `eventTime` ou `time` de niveau supérieur.
> - Utilisez le champ objet pour accéder à la ressource dont l’état de la stratégie a changé.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Event Grid et essayer les événements de changement d’état Azure Policy :

- [Acheminer les événements de changement d’état de stratégie à Event Grid avec Azure CLI](../tutorials/route-state-change-events.md)
- [Détails du schéma Azure Policy pour Event Grid](../../../event-grid/event-schema-policy.md)
- [À propos d’Event Grid](../../../event-grid/overview.md)
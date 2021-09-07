---
title: Configurer des stratégies Azure personnalisées sur des ressources Event Grid pour améliorer votre posture de sécurité
description: Cet article vous aide à définir des stratégies Azure personnalisées pour appliquer des contrôles de sécurité.
ms.topic: how-to
author: jfggdl
ms.author: jafernan
ms.date: 06/24/2021
ms.openlocfilehash: a9018150d75556f729db593ec1a76fb6aa174798
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992333"
---
# <a name="use-custom-azure-policies-to-enforce-security-controls"></a>Utiliser des stratégies Azure personnalisées pour appliquer des contrôles de sécurité

Cet article vous fournit des exemples de stratégies Azure personnalisées pour contrôler les destinations qui peuvent être configurées dans des abonnements aux événements d’Event Grid. [Azure Policy](../governance/policy/overview.md) vous permet d’appliquer les normes d’organisation et la conformité réglementaire pour différents aspects tels que la sécurité, le coût, la cohérence des ressources, la gestion, etc. Parmi ces considérations figurent les normes de conformité de la sécurité qui permettent de maintenir une posture de sécurité pour votre organisation. Pour vous aider dans vos contrôles de sécurité, les stratégies présentées dans cet article vous permettent d’empêcher l’exfiltration de données ou la remise d’événements à des points de terminaison non autorisés ou à des services Azure.

> [!NOTE]
> Azure Event Grid fournit des stratégies intégrées pour les domaines de conformité et les contrôles de sécurité liés à plusieurs normes de conformité. Vous pouvez trouver ces stratégies intégrées dans le [benchmark de sécurité Azure](security-controls-policy.md#azure-security-benchmark) d’Event Grid.

Pour empêcher l’exfiltration de données, les organisations peuvent vouloir limiter les destinations auxquelles Event Grid peut remettre des événements. Pour ce faire, vous pouvez affecter des stratégies qui permettent de créer ou de mettre à jour des [abonnements à des événements](concepts.md#event-subscriptions) ayant comme destination l’une des destinations approuvées dans la stratégie. L’effet de stratégie utilisé pour empêcher l’aboutissement d’une demande de ressource est [Deny](../governance/policy/concepts/effects.md#deny) (refus).

Les sections suivantes présentent des exemples de définitions de stratégie qui appliquent une liste de destinations autorisées. Vous voulez rechercher des [alias de propriété](../governance/policy/concepts/definition-structure.md#aliases) qui contiennent ```destination``` et l’utilisent comme valeur ```field``` (champ) pour effectuer une comparaison avec une liste de destinations autorisées lors de la définition d’une stratégie.

Vous pouvez trouver les alias de propriété définis pour Event Grid (utiliser l’espace de noms ```Microsoft.EventGrid```) en exécutant les commandes CLI ou PowerShell décrites [dans cette section de l’article](../governance/policy/concepts/definition-structure.md#aliases).

Pour plus d’informations sur la définition de stratégies, consultez l’article [Structure de définition Azure Policy](../governance/policy/concepts/definition-structure.md).

 
## <a name="define-an-azure-policy-with-a-list-of-allowed-destinations-for-a-webhook-destination"></a>Définir une stratégie Azure avec une liste de destinations autorisées pour une destination de webhook

La définition de stratégie suivante restreint les destinations de point de terminaison de webhook configurées dans un abonnement aux événements pour une rubrique système.

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/systemTopics/eventSubscriptions/destination.WebHook.endpointUrl",
        "in": "[parameters('allowedDestinationEndpointURLs')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedDestinationEndpointURLs": {
      "type": "Array",
      "metadata": {
        "description": "Allowed event destination endpoint URLs.",
        "displayName": "The list of allowed webhook endpoint destinations to which send events"
      },
        "allowedValues": [
          "https://www.your-valid-destination-goes-here-1.com",
          "https://www.your-valid-destination-goes-here-2.com",
          "https://www.your-valid-destination-goes-here-3.com"
        ]
    }
  }
}
```

## <a name="define-an-azure-policy-with-a-list-of-allowed-azure-service-resource-destinations"></a>Définir une stratégie Azure avec une liste de destinations des ressources de service Azure autorisées

La définition de stratégie suivante restreint une destination Event Hubs spécifique configurée dans un abonnement aux événements pour une rubrique personnalisée. Vous pouvez utiliser une approche similaire pour d’autres types de [destinations de service Azure prises en charge](event-handlers.md).

```json
{
  "mode": "All",
  "policyRule": {
    "if": {
      "not": {
        "field": "Microsoft.EventGrid/eventSubscriptions/destination.EventHub.resourceId",
        "in": "[parameters('allowedResourceDestinations')]"
      }
    },
    "then": {
      "effect": "deny"
    }
  },
  "parameters": {
    "allowedResourceDestinations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed event delivery destinations.",
        "displayName": "Allowed event delivery destinations"
      },
        "allowedValues": [
          "/subscriptions/<your-event-subscription>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<event-hubs-namespace-name>/eventhubs/<your-event-hub-name>"
        ]
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur Azure Policy, consultez les articles suivants : 
    - [Présentation d’Azure Policy](../governance/policy/overview.md)
    - [Structure de définition Azure Policy](../governance/policy/concepts/definition-structure.md).
    - [Présentation des effets d’Azure Policy](../governance/policy/concepts/effects.md).
    - [Comprendre l’étendue dans Azure Policy](../governance/policy/concepts/scope.md).
    - [Utiliser l’extension Azure Policy pour Visual Studio Code](../governance/policy/how-to/extension-for-vscode.md).
    - [Stratégies créées par programmation](../governance/policy/how-to/programmatically-create.md).
- Pour en savoir plus sur Azure Event Grid, consultez les articles listés sous la section Concepts, par exemple la [Terminologie Event Grid](concepts.md).
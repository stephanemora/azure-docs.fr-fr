---
title: Informations détaillées sur la structure d’attribution des stratégies
description: Décrit la définition d’attribution des stratégies qui est utilisée par Azure Policy pour associer des définitions et des paramètres de stratégie aux ressources à des fins d’évaluation.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 1b29becc3324c0d174db2102c4ae7ac15206e567
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563865"
---
# <a name="azure-policy-assignment-structure"></a>Structure d’attribution Azure Policy

Les attributions de stratégies sont utilisées par Azure Policy pour définir quelles stratégies ou initiatives sont attribuées aux ressources. L’attribution de stratégie peut déterminer les valeurs des paramètres de ce groupe de ressources au moment de l’attribution, ce qui permet de réutiliser les définitions de stratégie pour des mêmes propriétés de ressource qui auraient des exigences de conformité différentes.

Vous devez utiliser du code JSON pour créer une attribution de stratégie. L’attribution de stratégie contient des éléments pour :

- le nom d’affichage
- description
- metadata
- le mode d’application
- étendues exclues
- la définition de la stratégie
- messages de non-conformité
- parameters

Par exemple, le code JSON suivant montre une attribution de stratégie en mode _DoNotEnforce_ avec des paramètres dynamiques :

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Tous les exemples Azure Policy se trouvent dans [Exemples de stratégies](../samples/index.md).

## <a name="display-name-and-description"></a>Nom d’affichage et description

Vous utilisez **displayName** et **description** pour identifier l’attribution de stratégie et fournir un contexte pour son utilisation avec l’ensemble de ressources. **displayName** a une longueur maximale de _128_ caractères et **description** a une longueur maximale de _512_ caractères.

## <a name="metadata"></a>Métadonnées

La propriété facultative `metadata` stocke les informations sur l’attribution de stratégie. Les clients peuvent définir toutes les propriétés et valeurs utiles à leur organisation dans `metadata`. Cependant, certaines propriétés _communes_ sont utilisées par Azure Policy. Chaque `metadata` a une limite de 1024 caractères.

### <a name="common-metadata-properties"></a>Propriétés de métadonnées communes

- `assignedBy` (chaîne) : nom convivial du principal de sécurité qui a créé l’attribution.
- `createdBy` (chaîne) : GUID du principal de sécurité qui a créé l’attribution.
- `createdOn` (chaîne) : format date/heure universel ISO 8601 de l’heure de création de l’attribution.
- `parameterScopes` (objet) : collection de paires clé-valeur où la clé correspond à un nom de paramètre configuré [strongType](./definition-structure.md#strongtype) et la valeur définit l’étendue de ressource utilisée dans le portail pour fournir la liste des ressources disponibles en faisant correspondre la valeur _strongType_. Le portail définit cette valeur si l’étendue est différente de celle de l’attribution. Si cette valeur est définie, une modification de l’attribution de stratégie dans le portail l’affecte automatiquement à l’étendue du paramètre. Toutefois, l’étendue n’est pas verrouillée sur la valeur et peut être remplacée par une autre étendue.

  L’exemple suivant de `parameterScopes` s’applique à un paramètre _strongType_ nommé **backupPolicyId** qui définit une étendue pour la sélection des ressources quand l’attribution est modifiée dans le portail.

  ```json
  "metadata": {
      "parameterScopes": {
          "backupPolicyId": "/subscriptions/{SubscriptionID}/resourcegroups/{ResourceGroupName}"
      }
  }
  ```

- `updatedBy` (chaîne) : nom convivial du principal de sécurité qui a mis à jour l’attribution, le cas échéant.
- `updatedOn` (chaîne) : format date/heure universel ISO 8601 de l’heure de mise à jour de l’attribution, le cas échéant.

## <a name="enforcement-mode"></a>Mode d’application

La propriété **enforcementMode** permet aux clients de tester le résultat d’une stratégie sur des ressources existantes sans lancer l’effet de stratégie ni déclencher des entrées du [journal d’activité Azure](../../../azure-monitor/essentials/platform-logs-overview.md). Ce scénario est de type « What If » et suit des pratiques de déploiement sécurisées. **enforcementMode** diffère de l’effet [Disabled](./effects.md#disabled), car cet effet empêche l’évaluation des ressources de se produire.

Cette propriété a les valeurs suivantes :

|Mode |Valeur JSON |Type |Corriger manuellement |Entrée du journal d’activité |Description |
|-|-|-|-|-|-|
|activé |Default |string |Oui |Oui |L’effet de stratégie est appliqué pendant la création ou la mise à jour d’une ressource. |
|Désactivé |DoNotEnforce |string |Oui |Non | L’effet de stratégie n’est pas appliqué pendant la création ou la mise à jour d’une ressource. |

Si **enforcementMode** n’est pas spécifié dans la définition d’une stratégie ou d’une initiative, la valeur _Default_ est utilisée. Les [tâches de correction](../how-to/remediate-resources.md) peuvent être démarrées pour les stratégies [deployIfNotExists](./effects.md#deployifnotexists), même lorsque **enforcementMode** est défini sur _DoNotEnforce_.

## <a name="excluded-scopes"></a>Étendues exclues

L’**étendue** de l’affectation comprend tous les conteneurs de ressources enfants et les ressources enfants. Si la définition ne doit pas être appliquée à un conteneur de ressources enfant ou une ressource enfant, ces derniers peuvent être _exclus_ de l’évaluation en définissant **notScopes**. Cette propriété est un tableau qui permet d’exclure un ou plusieurs conteneurs de ressources ou ressources de l’évaluation. **notScopes** peut être ajouté ou mis à jour au terme de l’affectation initiale.

> [!NOTE]
> Une ressource _exclue_ est différente d’une ressource _exemptée_. Pour plus d’informations, consultez [Comprendre l’étendue d’Azure Policy](./scope.md).

## <a name="policy-definition-id"></a>ID de définition de stratégie

Ce champ correspond au nom du chemin complet d’une définition de stratégie ou d’une définition d’initiative.
`policyDefinitionId` est une chaîne et non un tableau. Si plusieurs stratégies sont souvent attribuées ensemble, il est recommandé d’utiliser une [initiative](./initiative-definition-structure.md).

## <a name="non-compliance-messages"></a>Messages de non-conformité

Pour définir un message personnalisé décrivant la raison pour laquelle une ressource n’est pas conforme à la définition de la stratégie ou de l’initiative, définissez `nonComplianceMessages` dans la définition de l’affectation. Ce nœud est un tableau d’entrées `message`. Ce message personnalisé s’ajoute au message d’erreur par défaut en cas de non-conformité et est facultatif.

> [!IMPORTANT]
> Les messages personnalisés en cas de non-conformité sont pris en charge uniquement sur des définitions ou des initiatives avec les des définitions de [Modes Resource Manager](./definition-structure.md#resource-manager-modes).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Si l’affectation concerne une initiative, différents messages peuvent être configurés pour chaque définition de stratégie de l’initiative. Les messages utilisent la valeur `policyDefinitionReferenceId` configurée dans la définition de l’initiative. Pour plus d’informations, consultez [Propriétés des définitions des stratégies](./initiative-definition-structure.md#policy-definition-properties).

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Paramètres

Ce segment de l’attribution de stratégie fournit les valeurs des paramètres définis dans la [définition de stratégie ou d’initiative](./definition-structure.md#parameters). Grâce à cette conception, il est possible de réutiliser une définition de stratégie ou d’initiative avec différentes ressources. Toutefois, vous devez chercher à connaître les valeurs métiers et les résultats pour chaque option.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Dans cet exemple, les paramètres précédemment définis dans la définition de stratégie sont `prefix` et `suffix`. Cette attribution de stratégie définit `prefix` sur **DeptA** et `suffix` sur **-LC**. La même définition de stratégie peut être réutilisée avec un autre ensemble de paramètres dans un autre service, ce qui réduit la duplication et la complexité des définitions de stratégie tout en offrant une certaine flexibilité.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [structure des définitions de stratégies](./definition-structure.md)
- Découvrez comment [créer des stratégies par programmation](../how-to/programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](../how-to/get-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](../how-to/remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).

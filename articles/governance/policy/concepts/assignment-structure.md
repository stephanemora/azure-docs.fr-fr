---
title: Informations détaillées sur la structure d’attribution des stratégies
description: Décrit la définition d’attribution des stratégies qui est utilisée par Azure Policy pour associer des définitions et des paramètres de stratégie aux ressources à des fins d’évaluation.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747206"
---
# <a name="azure-policy-assignment-structure"></a>Structure d’attribution Azure Policy

Les attributions de stratégies sont utilisées par Azure Policy pour définir quelles stratégies ou initiatives sont attribuées aux ressources. L’attribution de stratégie peut déterminer les valeurs des paramètres de ce groupe de ressources au moment de l’attribution, ce qui permet de réutiliser les définitions de stratégie pour des mêmes propriétés de ressource qui auraient des exigences de conformité différentes.

Vous devez utiliser du code JSON pour créer une attribution de stratégie. L’attribution de stratégie contient des éléments pour :

- le nom d’affichage
- description
- metadata
- le mode d’application
- la définition de la stratégie
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
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
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

## <a name="enforcement-mode"></a>Mode d’application

La propriété **enforcementMode** permet aux clients de tester le résultat d’une stratégie sur des ressources existantes sans lancer l’effet de stratégie ni déclencher des entrées du [journal d’activité Azure](../../../azure-monitor/platform/platform-logs-overview.md). Ce scénario est de type « What If » et suit des pratiques de déploiement sécurisées. **enforcementMode** diffère de l’effet [Disabled ](./effects.md#disabled), car cet effet empêche l’évaluation des ressources de se produire.

Cette propriété a les valeurs suivantes :

|Mode |Valeur JSON |Type |Corriger manuellement |Entrée du journal d’activité |Description |
|-|-|-|-|-|-|
|activé |Default |string |Oui |Oui |L’effet de stratégie est appliqué pendant la création ou la mise à jour d’une ressource. |
|Désactivé |DoNotEnforce |string |Oui |Non | L’effet de stratégie n’est pas appliqué pendant la création ou la mise à jour d’une ressource. |

Si **enforcementMode** n’est pas spécifié dans la définition d’une stratégie ou d’une initiative, la valeur _Default_ est utilisée. Les [tâches de correction](../how-to/remediate-resources.md) peuvent être démarrées pour les stratégies [deployIfNotExists](./effects.md#deployifnotexists), même lorsque **enforcementMode** est défini sur _DoNotEnforce_.

## <a name="policy-definition-id"></a>ID de définition de stratégie

Ce champ correspond au nom du chemin complet d’une définition de stratégie ou d’une définition d’initiative.
`policyDefinitionId` est une chaîne et non un tableau. Si plusieurs stratégies sont souvent attribuées ensemble, il est recommandé d’utiliser une [initiative](./definition-structure.md#initiatives).

## <a name="parameters"></a>Paramètres

Ce segment de l’attribution de stratégie fournit les valeurs des paramètres définis dans la [définition de stratégie ou d’initiative](./definition-structure.md#parameters).
Grâce à cette conception, il est possible de réutiliser une définition de stratégie ou d’initiative avec différentes ressources. Toutefois, vous devez chercher à connaître les valeurs métiers et les résultats pour chaque option.

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
---
title: 'Modèle : Effets d’une définition de stratégie'
description: Ce modèle Azure Policy fournit un exemple d’utilisation des différents effets d’une définition de stratégie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: b86a24bc0af6c9bdd7b29bb0a931d6c78865218b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170457"
---
# <a name="azure-policy-pattern-effects"></a>Modèle Azure Policy : Effets

Azure Policy a un certain nombre d’[effets](../concepts/effects.md) qui déterminent la façon dont le service réagit aux ressources non conformes. Certains effets sont simples et ne nécessitent aucune propriété supplémentaire dans la définition de stratégie, tandis que d’autres nécessitent plusieurs propriétés.

## <a name="sample-1-simple-effect"></a>Exemple 1 : Effet simple

Cette définition de stratégie vérifie si l’étiquette définie dans le paramètre **tagName** existe sur la ressource évaluée. Si l’étiquette n’existe pas encore, l’effet [modify](../concepts/effects.md#modify) est déclenché afin d’ajouter l’étiquette avec la valeur spécifiée dans le paramètre **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Exemple 1 : Explication

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="30-34":::

Un effet **modify** exige le bloc **policyRule.then.details** qui définit **roleDefinitionIds** et **operations**. Ces paramètres indiquent à Azure Policy quels rôles sont nécessaires pour ajouter l’étiquette et corriger la ressource, et quelle opération **modify** effectuer. Dans cet exemple, **operation** est _add_, et les paramètres servent à définir l’étiquette et sa valeur.

## <a name="sample-2-complex-effect"></a>Exemple 2 : Effet complexe

Cette définition de stratégie audite chaque machine virtuelle afin de vérifier si une extension, définie dans les paramètres **publisher** et **type**, n’existe pas. Elle utilise [auditIfNotExists](../concepts/effects.md#auditifnotexists) pour vérifier une ressource associée à la machine virtuelle afin de déterminer s’il existe une instance qui correspond aux paramètres définis. Cet exemple vérifie le type **extensions**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Exemple 2 : Explication

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Un effet **auditIfNotExists** exige le bloc **policyRule.then.details** pour définir à la fois un **type** et l’**existenceCondition** à rechercher. L’**existenceCondition** utilise des éléments de langage de stratégie, tels que des [opérateurs logiques](../concepts/definition-structure.md#logical-operators), pour déterminer s’il existe une ressource associée correspondante. Dans cet exemple, les valeurs vérifiées par rapport à chaque [alias](../concepts/definition-structure.md#aliases) sont définies dans les paramètres.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les autres [modèles et définitions intégrées](./index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
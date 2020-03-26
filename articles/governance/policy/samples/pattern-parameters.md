---
title: 'Modèle : Paramètres dans une définition de stratégie'
description: Ce modèle Azure Policy fournit un exemple d’utilisation des paramètres dans une définition de stratégie.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77170177"
---
# <a name="azure-policy-pattern-parameters"></a>Modèle Azure Policy : paramètres

Une définition de stratégie peut être rendue dynamique afin de réduire le nombre de définitions de stratégie nécessaires à l’aide des [paramètres](../concepts/definition-structure.md#parameters). Un paramètre est défini lors de l’attribution de la stratégie. Les paramètres ont un ensemble de propriétés prédéfinies qui les décrivent, ainsi que la façon dont ils sont utilisés.

## <a name="sample-1-string-parameters"></a>Exemple 1 : Paramètre de chaîne

Cette définition de stratégie utilise deux paramètres (**tagName** et **tagValue**) pour définir ce que l’attribution de stratégie doit rechercher dans les ressources. Ce format permet à la stratégie d’être utilisée avec autant de combinaisons nom-valeur d’étiquette que souhaité. Toutefois, il ne conserve qu’une seule définition de stratégie.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>Exemple 1 : Explication

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

Dans cette partie de la définition de stratégie, le paramètre **tagName** est défini en tant que _chaîne_ et une description est fournie pour son utilisation.

Le paramètre est ensuite utilisé dans le bloc **policyRule.if** pour rendre la stratégie dynamique. Ici, il est utilisé pour définir le champ évalué, qui correspond à une étiquette ayant la valeur **tagName**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>Exemple 2 : Paramètres de tableau

Cette définition de stratégie utilise un seul paramètre (**listOfBandwidthinMbps**) pour vérifier si la ressource de circuit ExpressRoute a configuré le paramètre de bande passante sur l’une des valeurs approuvées. Si ce n’est pas le cas, la création ou la mise à jour de la ressource est [refusée](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>Exemple 2 : Explication

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

Dans cette partie de la définition de stratégie, le paramètre **listOfBandwidthinMbps** est défini en tant que _tableau_ et une description est fournie pour son utilisation. En tant que _tableau_, il comprend plusieurs valeurs à rechercher.

Le paramètre est ensuite utilisé dans le bloc **policyRule.if**. En tant que paramètre de _tableau_, le **in** ou le **notIn** de la [condition](../concepts/definition-structure.md#conditions) d’un _tableau_
 doivent être utilisés.
Ici, il est utilisé sur l’alias **serviceProvider.bandwidthInMbps** comme l’une des valeurs définies.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les autres [modèles et définitions intégrées](./index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
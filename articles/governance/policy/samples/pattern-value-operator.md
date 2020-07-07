---
title: 'Modèle : Opérateur value dans une définition de stratégie'
description: Ce modèle Azure Policy fournit un exemple d’utilisation de l’opérateur value dans une définition de stratégie.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: e246e3a5e2517fa80626081227070bcb2f967784
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565665"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Modèle Azure Policy : Opérateur value

L’opérateur [value](../concepts/definition-structure.md#value) évalue des [paramètres](../concepts/definition-structure.md#parameters), des [fonctions de modèle prises en charge](../concepts/definition-structure.md#policy-functions) ou des littéraux par rapport à une valeur fournie pour une [condition](../concepts/definition-structure.md#conditions) donnée.

> [!WARNING]
> Si le résultat d’une _fonction de modèle_ est une erreur, la stratégie d’évaluation échoue. Une évaluation ayant échoué correspond à un **refus** implicite. Pour plus d’informations, consultez [Éviter les défaillances des modèles](../concepts/definition-structure.md#avoiding-template-failures).

## <a name="sample-policy-definition"></a>Exemple de définition de stratégie

Cette définition de stratégie ajoute ou remplace l’étiquette spécifiée dans le paramètre **tagName** (_string_) sur des ressources, et hérite de la valeur de **tagName** à partir du groupe de ressources dans lequel se trouve la ressource. Cette évaluation se produit quand la ressource est créée ou mise à jour. En tant qu’effet [modify](../concepts/effects.md#modify), la correction peut être exécutée sur des ressources existantes par le biais d’une [tâche de correction](../how-to/remediate-resources.md).

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Explication

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

L’opérateur **value** est utilisé dans le bloc **policyRule.if** dans **properties**. Dans cet exemple, l’[opérateur logique](../concepts/definition-structure.md#logical-operators) **allOf** est utilisé pour indiquer que les deux instructions conditionnelles doivent avoir la valeur true pour que l’effet, **modify**, ait lieu.

**value** évalue le résultat de la fonction de modèle [resourceGroup()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) à la condition **notEquals** d’une valeur vide. Si le nom d’étiquette fourni dans **tagName** sur le groupe de ressources parent existe, la condition est évaluée à true.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue les autres [modèles et définitions intégrées](./index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
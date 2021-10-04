---
title: Fonctions de modèle - Comparaison
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (modèle ARM) pour comparer des valeurs.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 0f9243cdc61ad5e7710663328eb4f85c9471fda5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812356"
---
# <a name="comparison-functions-for-arm-templates"></a>Fonctions de comparaison pour les modèles ARM

Resource Manager fournit plusieurs fonctions pour effectuer des comparaisons dans votre modèle Azure Resource Manager (modèle ARM) :

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Retourne la première valeur non null à partir des paramètres. Les chaînes vides, les tableaux vides et les objets vides ne sont pas null.

Dans Bicep, utilisez plutôt l’opérateur `??`. Voir [Fusionner ??](../bicep/operators-logical.md#coalesce-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |int, string, array ou object |La première valeur dans laquelle rechercher des valeurs null. |
| plus d’arguments |Non  |int, string, array ou object | Plus de valeurs dans lesquelles rechercher des valeurs null. |

### <a name="return-value"></a>Valeur retournée

Valeur des premiers paramètres non null. Il peut s’agir d’une chaîne, d’un entier, d’un tableau ou d’un objet. Null si tous les paramètres sont null.

### <a name="example"></a> Exemple

L’exemple de modèle suivant montre la sortie de différentes utilisations de la fonction coalesce.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/coalesce.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | Int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Vérifie si deux valeurs sont égales.

Dans Bicep, utilisez plutôt l’opérateur `==`. Consultez [Égal à ==](../bicep/operators-comparison.md#equals-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |int, string, array ou object |Première valeur dont l’égalité est à vérifier. |
| arg2 |Oui |int, string, array ou object |Deuxième valeur dont l’égalité est à vérifier. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si les valeurs sont égales ; sinon, renvoie **False**.

### <a name="remarks"></a>Remarques

La fonction equals est souvent utilisée avec l’élément `condition` pour tester si une ressource est déployée.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

### <a name="example"></a>Exemple

L’exemple suivant vérifie que les différents types de valeurs sont égaux. Toutes les valeurs par défaut retournent la valeur True.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/equals.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

L’exemple de modèle suivant utilise [not](template-functions-logical.md#not) avec **equals**.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/logical/not-equals.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Vérifie si la première valeur est supérieure à la deuxième valeur.

Dans Bicep, utilisez plutôt l’opérateur `>`. Consultez [Supérieur à >](../bicep/operators-comparison.md#greater-than-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |entier ou chaîne |Première valeur pour la comparaison « supérieur à ». |
| arg2 |Oui |entier ou chaîne |Seconde valeur pour la comparaison « supérieur à ». |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la première valeur est supérieure à la seconde ; sinon, renvoie **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si une valeur est supérieure à l’autre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greater.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Vérifie si la première valeur est supérieure ou égale à la deuxième valeur.

Dans Bicep, utilisez plutôt l’opérateur `>=`. Consultez [Supérieur ou égal à >=](../bicep/operators-comparison.md#greater-than-or-equal-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |entier ou chaîne |Première valeur pour la comparaison « supérieur ou égal à ». |
| arg2 |Oui |entier ou chaîne |Seconde valeur pour la comparaison « supérieur ou égal à ». |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la première valeur est supérieure ou égale à la seconde ; sinon, renvoie **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si une valeur est supérieure ou égale à l’autre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/greaterorequals.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Vérifie si la première valeur est inférieure à la deuxième valeur.

Dans Bicep, utilisez plutôt l’opérateur `<`. Consultez [Inférieur à <](../bicep/operators-comparison.md#less-than-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |entier ou chaîne |Première valeur pour la comparaison « inférieur à ». |
| arg2 |Oui |entier ou chaîne |Deuxième valeur pour la comparaison « inférieur à ». |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la première valeur est inférieure à la seconde ; sinon, renvoie **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si une valeur est inférieure à l’autre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/less.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Vérifie si la première valeur est inférieure ou égale à la deuxième valeur.

Dans Bicep, utilisez plutôt l’opérateur `<=`. Consultez [Inférieur ou égal à <=](../bicep/operators-comparison.md#less-than-or-equal-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |entier ou chaîne |Première valeur pour la comparaison « inférieur à ». |
| arg2 |Oui |entier ou chaîne |Première valeur pour la comparaison « inférieur ou égal à ». |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la première valeur est inférieure ou égale à la seconde ; sinon, renvoie **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si une valeur est inférieure ou égale à l’autre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/comparison/lessorequals.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).

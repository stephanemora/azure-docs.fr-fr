---
title: Opérateurs logiques Bicep
description: Décrit les opérateurs logiques bicep qui évaluent les conditions.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 1f904ed82f9d5b73d3b570017c005d88388bd068
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025796"
---
# <a name="bicep-logical-operators"></a>Opérateurs logiques Bicep

Les opérateurs logiques évaluent des valeurs booléennes, retournent des valeurs non nulles ou évaluent une expression conditionnelle. Pour exécuter les exemples, utilisez Azure CLI ou Azure PowerShell pour [déployer un fichier Bicep](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).

| Opérateur | Nom |
| ---- | ---- |
| `&&` | [And](#and-) |
| `||` | [Ou](#or-) |
| `!` | [Not](#not-) |
| `??` | [Coalesce](#coalesce-) |
| `?` `:` | [Expression conditionnelle](#conditional-expression--) |

## <a name="and-"></a>And &&

`operand1 && operand2`

Détermine si les deux valeurs sont vraies.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Première valeur à vérifier si la valeur est true. |
| `operand2` | boolean | Deuxième valeur à vérifier si la valeur est true. |
| Plus d’opérandes | boolean | D’autres opérandes peuvent être inclus. |

### <a name="return-value"></a>Valeur retournée

`True` lorsque les deux valeurs sont vraies, sinon `false` est retourné.

### <a name="example"></a>Exemple

Évalue un ensemble de valeurs de paramètres et un ensemble d’expressions.

```bicep
param operand1 bool = true
param operand2 bool = true

output andResultParm bool = operand1 && operand2
output andResultExp bool = bool(10 >= 10) && bool(5 > 2)
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `andResultParm` | boolean | true |
| `andResultExp` | boolean | true |

## <a name="or-"></a>Or ||

`operand1 || operand2`

Détermine si une des deux valeurs est vraie.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | boolean | Première valeur à vérifier si la valeur est true. |
| `operand2` | boolean | Deuxième valeur à vérifier si la valeur est true. |
| Plus d’opérandes | boolean | D’autres opérandes peuvent être inclus. |

### <a name="return-value"></a>Valeur retournée

`True` lorsque l’une ou l’autre valeur est vraie, sinon `false` est retourné.

### <a name="example"></a>Exemple

Évalue un ensemble de valeurs de paramètres et un ensemble d’expressions.

```bicep
param operand1 bool = true
param operand2 bool = false

output orResultParm bool = operand1 || operand2
output orResultExp bool = bool(10 >= 10) || bool(5 < 2)
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `orResultParm` | boolean | true |
| `orResultExp` | boolean | true |

## <a name="not-"></a>Not !

`!boolValue`

Inverse une valeur booléenne.

### <a name="operand"></a>Opérande

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `boolValue` | boolean | Valeur booléenne inversée. |

### <a name="return-value"></a>Valeur retournée

Inverse la valeur initiale et retourne une valeur booléenne. Si la valeur initiale est `true`, `false` est retourné.

### <a name="example"></a>Exemple

L’opérateur `not` inverse une valeur. Les valeurs peuvent être insérées entre parenthèses.

```bicep
param initTrue bool = true
param initFalse bool = false

output startedTrue bool = !(initTrue)
output startedFalse bool = !initFalse
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `startedTrue` | boolean | false |
| `startedFalse` | boolean | true |

## <a name="coalesce-"></a>Coalesce ??

`operand1 ?? operand2`

Retourne la première valeur non nulle à partir des paramètres.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | chaîne, entier, booléen, objet, tableau | Valeur à tester pour `null`. |
| `operand2` | chaîne, entier, booléen, objet, tableau | Valeur à tester pour `null`. |
| Plus d’opérandes | chaîne, entier, booléen, objet, tableau | Valeur à tester pour `null`. |

### <a name="return-value"></a>Valeur retournée

Retourne la première valeur non nulle. Les chaînes vides, les tableaux vides et les objets vides ne sont pas `null` et une valeur \<empty> est retournée.

### <a name="example"></a>Exemple

Les instructions de sortie retournent les valeurs non nulles. Le type de sortie doit correspondre au type dans la comparaison ou une erreur est générée.

```bicep
param myObject object = {
  'isnull1': null
  'isnull2': null
  'string': 'demoString'
  'emptystr': ''
  'integer': 10
  }

output nonNullStr string = myObject.isnull1 ?? myObject.string ?? myObject.isnull2
output nonNullInt int = myObject.isnull1 ?? myObject.integer ?? myObject.isnull2
output nonNullEmpty string = myObject.isnull1 ?? myObject.emptystr ?? myObject.string ?? myObject.isnull2
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `nonNullStr` | string | demoString |
| `nonNullInt` | int | 10 |
| `nonNullEmpty` | string | \<empty> |

## <a name="conditional-expression--"></a>Expression conditionnelle ? :

`condition ? true-value : false-value`

Évalue une condition et retourne une valeur si la condition est true ou false.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `condition` | boolean | Condition à évaluer comme vraie ou fausse. |
| `true-value` | chaîne, entier, booléen, objet, tableau | Valeur lorsque condition est vraie. |
| `false-value` | chaîne, entier, booléen, objet, tableau | Valeur lorsque condition est fausse. |

### <a name="example"></a>Exemple

Cet exemple évalue l’initiale d’un paramètre et retourne une valeur si la condition est vraie ou fausse.

```bicep
param initValue bool = true

output outValue string = initValue ? 'true value' : 'false value'
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `outValue` | string | true value |

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un fichier Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Pour plus d’informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](./bicep-functions-any.md).
- Pour comparer la syntaxe de Bicep et de JSON, consultez [Comparaison de JSON et de Bicep pour les modèles](./compare-template-syntax.md).
- Pour obtenir des exemples de fonctions Bicep, consultez [Fonctions Bicep](./bicep-functions.md).

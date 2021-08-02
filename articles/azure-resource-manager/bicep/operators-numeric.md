---
title: Opérateurs numériques Bicep
description: Décrit les opérateurs numériques Bicep qui calculent des valeurs.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 8d863d303ef0ed72b82bd65238f43a2d902c6003
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026452"
---
# <a name="bicep-numeric-operators"></a>Opérateurs numériques Bicep

Les opérateurs numériques utilisent des entiers pour effectuer des calculs et retourner des valeurs entières. Pour exécuter les exemples, utilisez Azure CLI ou Azure PowerShell afin de [déployer un fichier Bicep](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).

| Opérateur | Nom |
| ---- | ---- |
| `*` | [Multiplier](#multiply-) |
| `/` | [Divide](#divide-) |
| `%` | [Modulo](#modulo-) |
| `+` | [Ajouter](#add-) |
| `-` | [Soustraire](#subtract--) |
| `-` | [Moins](#minus--) |

> [!NOTE]
> Soustraire et Moins utilisent le même opérateur. La fonctionnalité est différente, car Soustraire utilise deux opérandes et Moins en utilise un seul.

## <a name="multiply-"></a>Multiplier *

`operand1 * operand2`

Multiplie deux entiers.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | entier | Nombre à multiplier. |
| `operand2`  | entier  | Multiplicateur du nombre. |

### <a name="return-value"></a>Valeur retournée

La multiplication retourne le produit sous forme d’entier.

### <a name="example"></a>Exemple

Deux entiers sont multipliés et retournent le produit.

```bicep
param firstInt int = 5
param secondInt int = 2

output product int = firstInt * secondInt
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `product` | entier | 10 |

## <a name="divide-"></a>Diviser /

`operand1 / operand2`

Divise un entier par un entier.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier | L’entier divisé. |
| `operand2` | entier | L’entier utilisé pour la division. Ne peut pas être zéro. |

### <a name="return-value"></a>Valeur retournée

La division retourne le quotient sous la forme d’un entier.

### <a name="example"></a>Exemple

Deux entiers sont divisés et retournent le quotient.

```bicep
param firstInt int = 10
param secondInt int = 2

output quotient int = firstInt / secondInt
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `quotient` | entier | 5 |

## <a name="modulo-"></a>Modulo %

`operand1 % operand2`

Divise un entier par un entier et retourne le reste.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | entier  | L’entier divisé. |
| `operand2`  | entier  | L’entier utilisé pour la division. Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour

Le reste est renvoyé sous forme d’entier. Si la division ne produit pas de reste, la valeur 0 est retournée.

### <a name="example"></a>Exemple

Deux paires d’entiers sont divisées et retournent les restes.

```bicep
param firstInt int = 10
param secondInt int = 3

param thirdInt int = 8
param fourthInt int = 4

output remainder int = firstInt % secondInt
output zeroRemainder int = thirdInt % fourthInt
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `remainder` | integer | 1 |
| `zeroRemainder` | entier | 0 |

## <a name="add-"></a>Ajouter +

`operand1 + operand2`

Ajoute deux entiers.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier | Nombre à ajouter. |
| `operand2` | entier | Nombre ajouté à un nombre. |

### <a name="return-value"></a>Valeur retournée

L’addition retourne la somme sous la forme d’un entier.

### <a name="example"></a>Exemple

Deux entiers sont ajoutés et retournent la somme.

```bicep
param firstInt int = 10
param secondInt int = 2

output sum int = firstInt + secondInt
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `sum` | entier | 12 |

## <a name="subtract--"></a>Soustraire -

`operand1 - operand2`

Soustrait un entier d’un entier.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier | Le plus grand nombre duquel une valeur est soustraite. |
| `operand2` | entier | Nombre qui est soustrait au plus grand nombre. |

### <a name="return-value"></a>Valeur retournée

La soustraction retourne la différence sous la forme d’un entier.

### <a name="example"></a>Exemple

Un entier est soustrait et retourne la différence.

```bicep
param firstInt int = 10
param secondInt int = 4

output difference int = firstInt - secondInt
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `difference` | entier | 6 |

## <a name="minus--"></a>Moins -

`-integerValue`

Multiplie un entier par `-1`.

### <a name="operand"></a>Opérande

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `integerValue` | entier | Entier multiplié par `-1`. |

### <a name="return-value"></a>Valeur retournée

Un entier est multiplié par `-1`. Un entier positif retourne un entier négatif, et un entier négatif retourne un entier positif. Les valeurs peuvent être insérées entre parenthèses.

### <a name="example"></a>Exemple

```bicep
param posInt int = 10
param negInt int = -20

output startedPositive int = -posInt
output startedNegative int = -(negInt)
```

Résultat de l’exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `startedPositive` | entier | -10 |
| `startedNegative` | entier | 20 |

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un fichier Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Pour plus d’informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](./bicep-functions-any.md).
- Pour comparer la syntaxe de Bicep et de JSON, consultez [Comparaison de JSON et de Bicep pour les modèles](./compare-template-syntax.md).
- Pour obtenir des exemples de fonctions Bicep, consultez [fonctions Bicep](./bicep-functions.md).

---
title: Opérateurs de comparaison Bicep
description: Décrit les opérateurs Bicep qui permettent de comparer des valeurs.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 1a28da26a3c97982bb0e06deebae6ae68b1eb7d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744406"
---
# <a name="bicep-comparison-operators"></a>Opérateurs de comparaison Bicep

Les opérateurs de comparaison comparent les valeurs et renvoient `true` ou `false`. Pour exécuter les exemples, utilisez Azure CLI ou Azure PowerShell afin de [déployer un fichier Bicep](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).

| Opérateur | Nom |
| ---- | ---- |
| `>=` | [Supérieur ou égal à](#greater-than-or-equal-) |
| `>`  | [Supérieur à](#greater-than-) |
| `<=` | [Inférieur ou égal à](#less-than-or-equal-) |
| `<`  | [Inférieur à](#less-than-) |
| `==` | [Égal à](#equals-) |
| `!=` | [Différent de](#not-equal-) |
| `=~` | [Égal à - insensible à la casse](#equal-case-insensitive-) |
| `!~` | [Différent de - insensible à la casse](#not-equal-case-insensitive-) |

## <a name="greater-than-or-equal-"></a>Supérieur ou égal à >=

`operand1 >= operand2`

Détermine si la première valeur est supérieure ou égale à la seconde.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier, chaîne | Première valeur de la comparaison. |
| `operand2` | entier, chaîne | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si la première valeur est supérieure ou égale à la seconde. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Une paire d'entiers et une paire de chaînes sont comparées.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'A'
param secondString string = 'A'

output intGtE bool = firstInt >= secondInt
output stringGtE bool = firstString >= secondString
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intGtE` | boolean | true |
| `stringGtE` | boolean | true |

## <a name="greater-than-"></a>Supérieur à >

`operand1 > operand2`

Détermine si la première valeur est supérieure à la seconde.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier, chaîne | Première valeur de la comparaison. |
| `operand2` | entier, chaîne | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si la première valeur est supérieure à la seconde. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Une paire d'entiers et une paire de chaînes sont comparées.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'bend'
param secondString string = 'band'

output intGt bool = firstInt > secondInt
output stringGt bool = firstString > secondString
```

Résultat de l'exemple :

Le **e** de **bend** rend la première chaîne plus grande.

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intGt` | boolean | true |
| `stringGt` | boolean | true |

## <a name="less-than-or-equal-"></a>Inférieur ou égal à <=

`operand1 <= operand2`

Détermine si la première valeur est inférieure ou égale à la seconde.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier, chaîne | Première valeur de la comparaison. |
| `operand2` | entier, chaîne | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si la première valeur est inférieure ou égale à la seconde. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Une paire d'entiers et une paire de chaînes sont comparées.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'demo'

output intLtE bool = firstInt <= secondInt
output stringLtE bool = firstString <= secondString
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intLtE` | boolean | true |
| `stringLtE` | boolean | true |

## <a name="less-than-"></a>Inférieur à <

`operand1 < operand2`

Détermine si la première valeur est inférieure à la seconde.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | entier, chaîne | Première valeur de la comparaison. |
| `operand2` | entier, chaîne | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si la première valeur est inférieure à la seconde. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Une paire d'entiers et une paire de chaînes sont comparées.

```bicep
param firstInt int = 5
param secondInt int = 10

param firstString string = 'demo'
param secondString string = 'Demo'

output intLt bool = firstInt < secondInt
output stringLt bool = firstString < secondString
```

Résultat de l'exemple :

La chaîne est `true` car les lettres minuscules sont inférieures à des lettres majuscules.

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intLt` | boolean | true |
| `stringLt` | boolean | true |

## <a name="equals-"></a>Égal à ==

`operand1 == operand2`

Détermine si les valeurs sont égales.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | chaîne, entier, booléen, tableau, objet | Première valeur de la comparaison. |
| `operand2` | chaîne, entier, booléen, tableau, objet | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si les opérandes sont égaux. Renvoie `false` si les opérandes sont différents.

### <a name="example"></a>Exemple

Les paires d'entiers, de chaînes et de valeurs booléennes sont comparées.

```bicep
param firstInt int = 5
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'demo'

param firstBool bool = true
param secondBool bool = true

output intEqual bool = firstInt == secondInt
output stringEqual bool = firstString == secondString
output boolEqual bool = firstBool == secondBool
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intEqual` | boolean | true |
| `stringEqual` | boolean | true |
| `boolEqual` | boolean | true |

Lors de la comparaison de tableaux, les deux tableaux doivent avoir les mêmes éléments et le même ordre. Les tableaux n’ont pas besoin d’être attribués entre eux.

```bicep
var array1 = [
  1
  2
  3
]

var array2 = [
  1
  2
  3
]

var array3 = array2

var array4 = [
  3
  2
  1
]

output sameElements bool = array1 == array2 // returns true because arrays are defined with same elements
output assignArray bool = array2 == array3 // returns true because one array was defined as equal to the other array
output differentOrder bool = array4 == array1 // returns false because order of elements is different
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| sameElements | bool | true |
| assignArray | bool | true |
| differentOrder | bool | false |

Lors de la comparaison d’objets, les valeurs et les noms de propriété doivent être identiques. Les propriétés n’ont pas besoin d’être définies dans le même ordre.

```bicep
var object1 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object2 = {
  prop1: 'val1'
  prop2: 'val2'
}

var object3 = {
  prop2: 'val2'
  prop1: 'val1'
}

var object4 = object3

var object5 = {
  prop1: 'valX'
  prop2: 'valY'
}

output sameObjects bool = object1 == object2 // returns true because both objects defined with same properties
output differentPropertyOrder bool = object3 == object2 // returns true because both objects have same properties even though order is different
output assignObject bool = object4 == object1 // returns true because one object was defined as equal to the other object
output differentValues bool = object5 == object1 // returns false because values are different
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| sameObjects | bool | true |
| differentPropertyOrder | bool | true |
| assignObject | bool | true |
| differentValues | bool | false |

## <a name="not-equal-"></a>Différent de !=

`operand1 != operand2`

Détermine si deux valeurs sont différentes.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | chaîne, entier, booléen, tableau, objet | Première valeur de la comparaison. |
| `operand2` | chaîne, entier, booléen, tableau, objet | Seconde valeur de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie si les opérandes sont différents. Renvoie `false` si les opérandes sont égaux.

### <a name="example"></a>Exemple

Les paires d'entiers, de chaînes et de valeurs booléennes sont comparées.

```bicep
param firstInt int = 10
param secondInt int = 5

param firstString string = 'demo'
param secondString string = 'test'

param firstBool bool = false
param secondBool bool = true

output intNotEqual bool = firstInt != secondInt
output stringNotEqual bool = firstString != secondString
output boolNotEqual bool = firstBool != secondBool
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `intNotEqual` | boolean | true |
| `stringNotEqual` | boolean | true |
| `boolNotEqual` | boolean | true |

Pour les tableaux et les objets, consultez les exemples dans la section [Égal à](#equals-).

## <a name="equal-case-insensitive-"></a>Égal à - insensible à la casse =~

`operand1 =~ operand2`

Ignore la casse pour déterminer si les deux valeurs sont égales.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1`  | string | Première chaîne de la comparaison. |
| `operand2`  | string | Deuxième chaîne de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie `true` si les chaînes sont égales. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Compare les chaînes qui utilisent des caractères à casse mixte.

```bicep
param firstString string = 'demo'
param secondString string = 'DEMO'

param thirdString string = 'demo'
param fourthString string = 'TEST'

output strEqual1 bool = firstString =~ secondString
output strEqual2 bool = thirdString =~ fourthString
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `strEqual1` | boolean | true |
| `strEqual2` | boolean | false |

## <a name="not-equal-case-insensitive-"></a>Différent de - insensible à la casse !~

`operand1 !~ operand2`

Ignore la casse pour déterminer si les deux valeurs sont différentes.

### <a name="operands"></a>Opérandes

| Opérande | Type | Description |
| ---- | ---- | ---- |
| `operand1` | string | Première chaîne de la comparaison. |
| `operand2` | string | Deuxième chaîne de la comparaison. |

### <a name="return-value"></a>Valeur retournée

Renvoie si les chaînes sont différentes. Sinon, `false` est retournée.

### <a name="example"></a>Exemple

Compare les chaînes qui utilisent des caractères à casse mixte.

```bicep
param firstString string = 'demo'
param secondString string = 'TEST'

param thirdString string = 'demo'
param fourthString string = 'DeMo'

output strNotEqual1 bool = firstString !~ secondString
output strEqual2 bool = thirdString !~ fourthString
```

Résultat de l'exemple :

| Nom | Type | Valeur |
| ---- | ---- | ---- |
| `strNotEqual1` | boolean | true |
| `strNotEqual2` | boolean | false |

## <a name="next-steps"></a>Étapes suivantes

- Pour créer un fichier Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Pour plus d’informations sur la résolution des erreurs de type Bicep, consultez [Fonction any pour Bicep](./bicep-functions-any.md).
- Pour comparer la syntaxe de Bicep et de JSON, consultez [Comparaison de JSON et de Bicep pour les modèles](./compare-template-syntax.md).
- Pour obtenir des exemples de fonctions Bicep, consultez [Fonctions Bicep](./bicep-functions.md).

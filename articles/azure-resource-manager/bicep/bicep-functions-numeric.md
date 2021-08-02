---
title: Fonctions Bicep - Nombres
description: Décrit les fonctions à utiliser dans un fichier Bicep pour utiliser des nombres.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 2eec27d0317b6cfae4969244d069be6881566942
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026362"
---
# <a name="numeric-functions-for-bicep"></a>Fonctions numériques pour Bicep

Resource Manager propose les fonctions suivantes pour utiliser des entiers dans votre fichier Bicep :

* [int](#int)
* [max](#max)
* [min](#min)

Certaines des fonctions numériques JSON Azure Resource Manager sont remplacées par des [opérateurs numériques Bicep](./operators-numeric.md).

## <a name="int"></a>int

`int(valueToConvert)`

Convertit la valeur spécifiée en entier.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui |chaîne ou entier |La valeur à convertir en entier. |

### <a name="return-value"></a>Valeur de retour

Nombre entier de la valeur convertie.

### <a name="example"></a>Exemple

L’exemple ci-après convertit la valeur de paramètre fournie par l’utilisateur en entier.

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur maximale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur maximale de la collection.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser max avec un tableau et une liste d’entiers :

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min (arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur minimale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur minimale de la collection.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser la fonction min avec un tableau et une liste d’entiers :

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [Comprendre la structure et la syntaxe des fichiers Bicep](./file.md).

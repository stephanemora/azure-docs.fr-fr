---
title: Fonctions de modèle – Numérique
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (ARM) pour travailler avec des nombres.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ccfed6794b81b7910310cc5a9fd02dcf0cdb0e0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820421"
---
# <a name="numeric-functions-for-arm-templates"></a>Fonctions numériques pour les modèles ARM

Resource Manager fournit les fonctions ci-après pour travailler avec des entiers dans votre modèle Azure Resource Manager (modèle ARM) :

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

## <a name="add"></a>add

`add(operand1, operand2)`

Retourne la somme des deux entiers fournis.

La fonction `add` n’est pas prise en charge dans Bicep. Utilisez plutôt l’[opérateur `+`](../bicep/operators-numeric.md#add-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
|operand1 |Oui |int |Premier nombre à ajouter. |
|operand2 |Oui |int |Deuxième nombre à ajouter. |

### <a name="return-value"></a>Valeur de retour

Entier qui contient la somme des paramètres.

### <a name="example"></a>Exemple

L’exemple suivant ajoute deux paramètres.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/add.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Retourne l’index d’une boucle d’itération.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| loopName | Non  | string | Nom de la boucle pour l’obtention de l’itération. |
| offset |Non |int |Le nombre à ajouter à la valeur d’itération de base zéro. |

### <a name="remarks"></a>Remarques

Cette fonction est toujours utilisée avec un objet **copy** . Si aucune valeur n’est fournie pour **offset**, la valeur d’itération actuelle est retournée. La valeur d’itération commence à zéro.

La propriété **loopName** permet d’indiquer si copyIndex fait référence à une itération de ressource ou de propriété. Si aucune valeur n’est indiquée pour **loopName**, l’itération du type de ressource actuelle est utilisée. Indiquez une valeur pour **loopName** lors de l’itération sur une propriété.

Pour plus d’informations sur l’utilisation de copy, voir :

* [Itération de ressource dans les modèles ARM](copy-resources.md)
* [Itération de propriété dans les modèles ARM](copy-properties.md)
* [Itération de variable dans les modèles ARM](copy-variables.md)
* [Itération de sortie dans les modèles ARM](copy-outputs.md)

### <a name="example"></a>Exemple

L’exemple suivant montre une boucle de copie ainsi que la valeur d’index incluse dans le nom.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/copyindex.json":::

### <a name="return-value"></a>Valeur de retour

Entier représentant l’index actuel de l’itération.

## <a name="div"></a>div

`div(operand1, operand2)`

Retourne la division entière des deux entiers fournis.

La fonction `div` n’est pas prise en charge dans Bicep. Utilisez plutôt l’[opérateur `/`](../bicep/operators-numeric.md#divide-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre à diviser. |
| operand2 |Oui |int |Le nombre utilisé pour diviser. Ne peut pas être 0. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la division.

### <a name="example"></a>Exemple

L’exemple suivant divise un paramètre par un autre paramètre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/div.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Convertit la valeur en nombre à virgule flottante. Vous utilisez uniquement cette fonction lors de la transmission de paramètres personnalisés à une application, telle qu’une application logique.

La fonction `float` n’est pas prise en charge dans Bicep.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou entier |Valeur à convertir en nombre à virgule flottante. |

### <a name="return-value"></a>Valeur de retour

Nombre à virgule flottante.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser float pour passer des paramètres à une application logique :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/float.json":::

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

Dans l’exemple de modèle suivant, vous convertissez la valeur de paramètre fournie par l’utilisateur en un entier.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/int.json":::

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

L’exemple suivant montre comment utiliser max avec un tableau et une liste d’entiers.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

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

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Retourne le reste de la division entière des deux entiers fournis.

La fonction `mod` n’est pas prise en charge dans Bicep. Utilisez plutôt l’[opérateur %](../bicep/operators-numeric.md#modulo-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre à diviser. |
| operand2 |Oui |int |Le nombre utilisé pour diviser, ne peut pas être 0. |

### <a name="return-value"></a>Valeur retournée

Entier représentant le reste.

### <a name="example"></a> Exemple

L’exemple suivant renvoie le reste de la division d’un paramètre par un autre paramètre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mod.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Retourne la multiplication des deux entiers fournis.

La fonction `mul` n’est pas prise en charge dans Bicep. Utilisez plutôt l’[opérateur *](../bicep/operators-numeric.md#multiply-).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Premier nombre à multiplier. |
| operand2 |Oui |int |Deuxième nombre à multiplier. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la multiplication.

### <a name="example"></a>Exemple

L’exemple suivant multiplie un paramètre par un autre paramètre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/mul.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Retourne la soustraction des deux entiers fournis.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| operand1 |Oui |int |Le nombre auquel est appliquée la soustraction. |
| operand2 |Oui |int |Le nombre qui est soustrait. |

### <a name="return-value"></a>Valeur de retour

Entier représentant la multiplication.

### <a name="example"></a>Exemple

L’exemple suivant soustrait un paramètre à un autre paramètre.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/sub.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).
* Pour itérer un nombre spécifié lors de la création d’un type de ressource, consultez [Itération de ressource dans les modèles ARM](copy-resources.md).

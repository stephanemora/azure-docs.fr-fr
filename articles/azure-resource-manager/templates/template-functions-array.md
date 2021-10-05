---
title: Fonctions de modèle - tableaux
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (ARM) pour travailler avec des tableaux.
ms.topic: conceptual
ms.date: 09/08/2021
ms.openlocfilehash: 25fb249e1840a7538fa1dd14f3b1a930bbefdfee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755992"
---
# <a name="array-functions-for-arm-templates"></a>Fonctions de tableau pour les modèles ARM

Resource Manager fournit plusieurs fonctions pour vous permettre d’utiliser des tableaux dans votre modèle Azure Resource Manager (ARM) :

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Pour obtenir un tableau de valeurs de chaîne délimitée par une valeur, consultez [split](template-functions-string.md#split).

## <a name="array"></a>tableau

`array(convertToArray)`

Convertit la valeur en tableau.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| convertToArray |Oui |int, string, array ou object |Valeur à convertir en tableau. |

### <a name="return-value"></a>Valeur retournée

Tableau.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la fonction de tableau avec des types différents.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/array.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Combine plusieurs tableaux et retourne le tableau concaténé, ou combine plusieurs valeurs de chaîne et renvoie la chaîne concaténée.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |Le premier tableau ou la première chaîne à concaténer. |
| arguments supplémentaires |Non  |tableau ou chaîne |Tableaux ou chaînes supplémentaires en ordre séquentiel pour la concaténation. |

Cette fonction peut prendre n’importe quel nombre d’arguments et accepter à la fois des chaînes ou des tableaux pour les paramètres. Toutefois, vous ne pouvez pas fournir à la fois des tableaux et des chaînes pour les paramètres. Les tableaux sont concaténés uniquement avec d’autres tableaux.

### <a name="return-value"></a>Valeur retournée

Chaîne ou tableau de valeurs concaténées.

### <a name="example"></a> Exemple

L’exemple suivant montre comment combiner deux tableaux.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/concat-array.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

L’exemple suivant montre comment combiner deux valeurs de chaîne et retourner une chaîne concaténée.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/string/concat-string.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Vérifie si un tableau contient une valeur, un objet contient une clé ou une chaîne contient une sous-chaîne. La comparaison de chaînes est sensible à la casse. Cependant, quand vous testez si un objet contient une clé, la comparaison n’est pas sensible à la casse.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| conteneur |Oui |tableau, objet ou chaîne |La valeur qui contient la valeur à rechercher. |
| itemToFind |Oui |chaîne ou entier |La valeur à trouver. |

### <a name="return-value"></a>Valeur retournée

**True** si l’élément est trouvé ; sinon, **False**.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser contains avec différents types :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/contains.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Crée un tableau à partir des paramètres.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| args |Non |Chaîne, entier, tableau ou objet |Valeurs dans le tableau. |

### <a name="return-value"></a>Valeur de retour

Tableau. Quand aucun paramètre n’est fourni, un tableau vide est retourné.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser createArray avec différents types :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/createarray.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Détermine si un tableau, un objet ou une chaîne est vide.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Oui |tableau, objet ou chaîne |Valeur à vérifier pour voir si elle est vide. |

### <a name="return-value"></a>Valeur retournée

Retourne **True** si la valeur est vide ; sinon, **False**.

### <a name="example"></a>Exemple

L’exemple suivant vérifie si un tableau, un objet et une chaîne sont vides.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/empty.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Retourne le premier élément du tableau ou le premier caractère de la chaîne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le premier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Type (chaîne, entier, tableau ou objet) du premier élément d’un tableau ou premier caractère d’une chaîne.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la première fonction avec un tableau et une chaîne.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/first.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>intersection

`intersection(arg1, arg2, arg3, ...)`

Retourne un tableau ou un objet unique avec les éléments communs à partir des paramètres.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |objet ou tableau |La première valeur à utiliser pour rechercher des éléments communs. |
| arg2 |Oui |objet ou tableau |La seconde valeur à utiliser pour rechercher des éléments communs. |
| arguments supplémentaires |Non |objet ou tableau |Valeurs supplémentaires à utiliser pour rechercher des éléments communs. |

### <a name="return-value"></a>Valeur de retour

Tableau ou objet avec les éléments communs.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser l’intersection avec les tableaux et les objets.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/intersection.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

Retourne le dernier élément du tableau ou le dernier caractère de la chaîne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le dernier élément ou caractère. |

### <a name="return-value"></a>Valeur retournée

Type (chaîne, entier, tableau ou objet) du dernier élément d’un tableau ou dernier caractère d’une chaîne.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser la dernière fonction avec un tableau et une chaîne.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/last.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Retourne le nombre d’éléments d’un tableau, les caractères d’une chaîne ou les propriétés au niveau de la racine d’un objet.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau, chaîne ou objet |Tableau à utiliser pour l’obtention du nombre d’éléments, ou chaîne à utiliser pour l’obtention du nombre de caractères, ou l’objet à utiliser pour l’obtention du nombre de propriétés au niveau de la racine. |

### <a name="return-value"></a>Valeur retournée

Un entier.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la longueur avec un tableau et une chaîne.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

Vous pouvez utiliser cette fonction avec un tableau pour spécifier le nombre d’itérations lors de la création de ressources. Dans l’exemple ci-après, le paramètre **siteNames** fait référence à un tableau de noms à utiliser lors de la création de sites web.

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

Pour plus d’informations sur l’utilisation de cette fonction avec un tableau, consultez [Itération sur des ressources dans les modèles ARM](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur maximale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur maximale.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser max avec un tableau et une liste d’entiers.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/max.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min(arg1)`

Retourne la valeur minimale à partir d’un tableau d’entiers ou une liste séparée par des virgules d’entiers.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau d’entiers ou liste séparée par des virgules d’entiers |Collection permettant d’obtenir la valeur minimale. |

### <a name="return-value"></a>Valeur retournée

Entier représentant la valeur minimale.

### <a name="example"></a> Exemple

L’exemple suivant indique comment utiliser la fonction min avec un tableau et une liste d’entiers.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/numeric/min.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Crée un tableau d’entiers à partir d’un entier de départ et contenant un nombre d’éléments.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| index_début |Oui |int |Premier entier du tableau. La somme de startIndex et count ne doit pas être supérieure à 2147483647. |
| count |Oui |int |Nombre d’entiers dans le tableau. Il doit s'agir d'un entier non négatif jusqu'à 10000. |

### <a name="return-value"></a>Valeur retournée

Tableau d’entiers.

### <a name="example"></a> Exemple

L’exemple suivant montre comment utiliser la fonction range.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/range.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourne un tableau avec tous les éléments après le nombre spécifié dans le tableau, ou retourne une chaîne avec tous les caractères après le nombre spécifié dans la chaîne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à utiliser pour ignorer les caractères. |
| numberToSkip |Oui |int |Nombre d’éléments ou de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments ou caractères de la valeur sont renvoyés. Si elle est supérieure à la longueur du tableau ou de la chaîne, un tableau ou une chaîne vide est retourné. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="example"></a> Exemple

L’exemple suivant ignore le nombre spécifié d’éléments dans le tableau et le nombre spécifié de caractères dans une chaîne.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/skip.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourne un tableau ou une chaîne. Un tableau possède le nombre spécifié d’éléments depuis le début du tableau. Une chaîne possède le nombre de caractères spécifié à partir du début de la chaîne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à partir duquel les éléments sont tirés. |
| numberToTake |Oui |int |Nombre d’éléments ou de caractères à prendre. Si cette valeur est inférieure ou égale à 0, une chaîne ou un tableau vide est renvoyé. Si elle est supérieure à la longueur du tableau ou de la chaîne, tous les éléments du tableau ou de la chaîne sont retournés. |

### <a name="return-value"></a>Valeur retournée

Tableau ou chaîne.

### <a name="example"></a> Exemple

L’exemple suivant prend le nombre spécifié d’éléments du tableau, et les caractères d’une chaîne.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/take.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | sur |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Retourne un tableau ou un objet unique avec tous les éléments communs à partir des paramètres. Les valeurs ou les clés en double sont uniquement incluses une seule fois.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |objet ou tableau |La première valeur à utiliser pour joindre des éléments. |
| arg2 |Oui |objet ou tableau |La seconde valeur à utiliser pour joindre des éléments. |
| arguments supplémentaires |Non |objet ou tableau |Valeurs supplémentaires à utiliser pour joindre des éléments. |

### <a name="return-value"></a>Valeur de retour

Objet ou tableau.

### <a name="example"></a>Exemple

L’exemple suivant indique comment utiliser l’intersection avec les tableaux et les objets.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).

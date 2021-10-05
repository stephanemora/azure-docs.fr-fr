---
title: Fonctions de modèle - objets
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager (ARM) pour travailler avec des objets.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 3e6069744b1879e97ef3977916acbfc8aa6a9bd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820437"
---
# <a name="object-functions-for-arm-templates"></a>Fonctions d’objet pour les modèles ARM

Resource Manager fournit plusieurs fonctions pour vous permettre d’utiliser des objets dans votre modèle Azure Resource Manager (ARM) :

* [contains](#contains)
* [createObject](#createobject)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
* [null](#null)
* [union](#union)

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

## <a name="createobject"></a>createObject

`createObject(key1, value1, key2, value2, ...)`

Crée un objet à partir des clés et des valeurs.

La fonction `createObject` n’est pas prise en charge par Bicep.  Créez un objet en utilisant `{}`. Consultez [Objets](../bicep/data-types.md#objects).

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| key1 |Non |string |Nom de la clé. |
| valeur1 |Non |int, boolean, string, object ou array |Valeur pour la clé. |
| clés supplémentaires |Non |string |Noms supplémentaires des clés. |
| valeurs supplémentaires |Non |int, boolean, string, object ou array |Valeurs supplémentaires pour les clés. |

La fonction attend uniquement un nombre pair de paramètres. Chaque clé doit avoir une valeur correspondante.

### <a name="return-value"></a>Valeur de retour

Objet avec chaque paire de clé et valeur.

### <a name="example"></a>Exemple

L’exemple suivant crée un objet à partir de différents types de valeurs.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/createobject.json":::

La sortie de l’exemple précédent avec les valeurs par défaut est un objet nommé `newObject` avec la valeur suivante :

```json
{
  "intProp": 1,
  "stringProp": "abc",
  "boolProp": true,
  "arrayProp": ["a", "b", "c"],
  "objectProp": {"key1": "value1"}
}
```

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

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Convertit une chaîne JSON valide en un type de données JSON.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |string |Valeur à convertir en JSON. La chaîne doit être une chaîne JSON correctement mise en forme. |

### <a name="return-value"></a>Valeur de retour

Le type de données JSON de la chaîne spécifiée ou une valeur vide lorsque **null** est spécifié.

### <a name="remarks"></a>Remarques

Si vous devez inclure une valeur de paramètre ou une variable dans l’objet JSON, utilisez la fonction [concat](template-functions-string.md#concat) pour créer la chaîne que vous passez à la fonction.

Vous pouvez également utiliser [null()](#null) pour obtenir une valeur null.

### <a name="example"></a>Exemple

L’exemple suivant explique comment utiliser la fonction `json`. Notez que vous pouvez transmettre `null` pour un objet vide.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/json.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| emptyObjectOutput | Boolean | True |
| objectOutput | Object | {"a": "b"} |
| stringOutput | String | test |
| booleanOutput | Boolean | True |
| intOutput | Integer | 3 |
| arrayOutput | Array | [ 1, 2, 3 ] |
| concatObjectOutput | Object | {"a": "valeur de démonstration"} |

## <a name="length"></a>length

`length(arg1)`

Retourne le nombre d’éléments d’un tableau, les caractères d’une chaîne ou les propriétés au niveau de la racine d’un objet.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau, chaîne ou objet |Tableau à utiliser pour l’obtention du nombre d’éléments, ou chaîne à utiliser pour l’obtention du nombre de caractères, ou l’objet à utiliser pour l’obtention du nombre de propriétés au niveau de la racine. |

### <a name="return-value"></a>Valeur retournée

Un entier.

### <a name="example"></a>Exemple

L’exemple suivant montre comment utiliser la longueur avec un tableau et une chaîne :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/length.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="null"></a>null

`null()`

Retourne la valeur NULL.

La fonction `null` n’est pas disponible dans Bicep. Utilisez plutôt le mot clé `null`.

### <a name="parameters"></a>Paramètres

La fonction null n’accepte aucun paramètre.

### <a name="return-value"></a>Valeur de retour

Valeur qui correspond toujours à null.

### <a name="example"></a>Exemple

L’exemple suivant utilise la fonction null.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/object/null.json":::

La sortie de l’exemple précédent est :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| emptyOutput | Bool | True |

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

L’exemple suivant indique comment utiliser l’intersection avec les tableaux et les objets :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/array/union.json":::

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle ARM, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).

---
title: Fonctions de modèle - objets
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour travailler avec des objets.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fede4d6c71e45b119e500d4c9c6f91765d052036
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676792"
---
# <a name="object-functions-for-arm-templates"></a>Fonctions d’objet pour les modèles ARM

Resource Manager fournit plusieurs fonctions pour travailler avec des objets dans votre modèle Azure Resource Manager (ARM).

* [contains](#contains)
* [empty](#empty)
* [intersection](#intersection)
* [json](#json)
* [length](#length)
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

### <a name="example"></a> Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) suivant montre comment utiliser contains avec différents types :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="empty"></a>empty

`empty(itemToTest)`

Détermine si un tableau, un objet ou une chaîne est vide.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Oui |tableau, objet ou chaîne |Valeur à vérifier pour voir si elle est vide. |

### <a name="return-value"></a>Valeur retournée

Retourne **True** si la valeur est vide ; sinon, **False**.

### <a name="example"></a> Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) suivant vérifie si un tableau, un objet et une chaîne sont vides.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

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
| arguments supplémentaires |Non  |objet ou tableau |Les valeur supplémentaires à utiliser pour rechercher des éléments communs. |

### <a name="return-value"></a>Valeur retournée

Tableau ou objet avec les éléments communs.

### <a name="example"></a> Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) suivant indique comment utiliser intersection avec des tableaux et des objets :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="json"></a>json

`json(arg1)`

Renvoie un objet JSON.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |string |La valeur à convertir au format JSON. |

### <a name="return-value"></a>Valeur retournée

L’objet JSON à partir de la chaîne spécifiée ou un objet vide lorsque **nul** est spécifié.

### <a name="remarks"></a>Notes 

Si vous devez inclure une valeur de paramètre ou une variable dans l’objet JSON, utilisez la fonction [concat](template-functions-string.md#concat) pour créer la chaîne que vous passez à la fonction.

### <a name="example"></a> Exemple

L’[exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) suivant montre comment utiliser la fonction json. Notez que vous pouvez passer une chaîne qui représente l’objet ou utiliser **null** quand aucune valeur n’est nécessaire.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "jsonObject1": {
            "type": "string",
            "defaultValue": "null"
        },
        "jsonObject2": {
            "type": "string",
            "defaultValue": "{\"a\": \"b\"}"
        },
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput1": {
            "type": "bool",
            "value": "[empty(json(parameters('jsonObject1')))]"
        },
        "jsonOutput2": {
            "type": "object",
            "value": "[json(parameters('jsonObject2'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| jsonOutput1 | Boolean | True |
| jsonOutput2 | Object | {"a": "b"} |
| paramOutput | Object | {"a": "valeur pour démonstration"}

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

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) suivant montre comment utiliser length avec un tableau et une chaîne :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Retourne un tableau ou un objet unique avec tous les éléments communs à partir des paramètres. Les valeurs ou les clés en double sont uniquement incluses une seule fois.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |objet ou tableau |La première valeur à utiliser pour joindre des éléments. |
| arg2 |Oui |objet ou tableau |La seconde valeur à utiliser pour joindre des éléments. |
| arguments supplémentaires |Non  |objet ou tableau |Valeurs supplémentaires à utiliser pour joindre des éléments. |

### <a name="return-value"></a>Valeur retournée

Objet ou tableau.

### <a name="example"></a> Exemple

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) suivant montre comment utiliser intersection avec des tableaux et des objets :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles ARM](template-syntax.md).

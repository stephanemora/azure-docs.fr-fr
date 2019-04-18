---
title: Fonctions de modèle Azure Resource Manager - chaîne | Microsoft Docs
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour travailler avec des chaînes.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: bf9faa34c1f0923761ce583c22ba4084d7bd42a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278783"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Fonctions de chaînes pour les modèles Azure Resource Manager

Resource Manager fournit les fonctions ci-après pour travailler avec des chaînes de caractères :

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [découper](#trim)
* [uniqueString](#uniquestring)
* [URI](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Retourne la représentation en base 64 de la chaîne d'entrée.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_entrée |Oui |string |La valeur à retourner sous la forme d’une représentation en base64. |

### <a name="return-value"></a>Valeur de retour

Une chaîne contenant la représentation en base64.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) suivant montre comment utiliser la fonction base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| base64Output | Chaîne | b25lLCB0d28sIHRocmVl |
| toStringOutput | Chaîne | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Convertit une représentation en base64 en un objet JSON.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Oui |string |La représentation en base64 à convertir en un objet JSON. |

### <a name="return-value"></a>Valeur de retour

Un objet JSON.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) suivant utilise la fonction base64ToJson pour convertir une valeur base64 :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| base64Output | Chaîne | b25lLCB0d28sIHRocmVl |
| toStringOutput | Chaîne | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Convertit une représentation en base64 en une chaîne.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| base64Value |Oui |string |La représentation en base64 à convertir en une chaîne. |

### <a name="return-value"></a>Valeur de retour

Une chaîne de la valeur base64 convertie.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) suivant utilise la fonction base64ToString pour convertir une valeur base64 :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| base64Output | Chaîne | b25lLCB0d28sIHRocmVl |
| toStringOutput | Chaîne | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combine plusieurs valeurs de chaîne et retourne la chaine concaténée, ou combine plusieurs tableaux et retourne le tableau concaténé.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou tableau |La première valeur pour la concaténation. |
| arguments supplémentaires |Non  |string |Valeurs supplémentaires en ordre séquentiel pour la concaténation. |

### <a name="return-value"></a>Valeur de retour
Chaîne ou tableau de valeurs concaténées.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) suivant montre comment combiner deux valeurs de chaîne et retourner une chaîne concaténée.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| concatOutput | Chaîne | prefix-5yj4yjf5mbg72 |

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) suivant montre comment combiner deux tableaux.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| return | Tableau | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Vérifie si un tableau contient une valeur, un objet contient une clé ou une chaîne contient une sous-chaîne. La comparaison de chaînes est sensible à la casse. Cependant, quand vous testez si un objet contient une clé, la comparaison n’est pas sensible à la casse.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| conteneur |Oui |tableau, objet ou chaîne |La valeur qui contient la valeur à rechercher. |
| itemToFind |Oui |chaîne ou entier |La valeur à trouver. |

### <a name="return-value"></a>Valeur de retour

**True** si l’élément est trouvé ; sinon, **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) suivant montre comment utiliser contains avec différents types :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Convertit une valeur en un URI de données.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Oui |string |Valeur à convertir en URI de données. |

### <a name="return-value"></a>Valeur de retour

Une chaîne formatée en tant qu’URI de données.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) suivant convertit une valeur en un URI de données et convertit un URI de données en chaîne :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| dataUriOutput | Chaîne | data: texte/brut;jeu de caractèresdata:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Chaîne | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Convertit une valeur formatée en URI de données en chaîne.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Oui |string |Valeur d’URI de données à convertir. |

### <a name="return-value"></a>Valeur de retour

Chaîne contenant la valeur convertie.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) suivant convertit une valeur en un URI de données et convertit un URI de données en chaîne :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| dataUriOutput | Chaîne | data: texte/brut;jeu de caractèresdata:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Chaîne | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Détermine si un tableau, un objet ou une chaîne est vide.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| itemToTest |Oui |tableau, objet ou chaîne |Valeur à vérifier s’il est vide. |

### <a name="return-value"></a>Valeur de retour

Retourne **True** si la valeur est vide ; sinon, **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) suivant vérifie si un tableau, un objet et une chaîne sont vides.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Détermine si une chaîne se termine par une valeur. La comparaison respecte la casse.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur de retour

**True** si le ou les derniers caractères de la chaîne correspondent à la valeur ; sinon, **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) suivant montre comment utiliser les fonctions startsWith et endsWith :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | true |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Retourne le premier caractère de la chaîne ou le premier élément du tableau.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le premier élément ou caractère. |

### <a name="return-value"></a>Valeur de retour

Chaîne du premier caractère ou type (chaîne, entier, tableau ou objet) du premier élément d’un tableau.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) suivant montre comment utiliser la première fonction avec un tableau et une chaîne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Chaîne | one |
| stringOutput | Chaîne | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Crée une chaîne mise en forme à partir de valeurs d’entrée.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| formatString | Oui | string | La chaîne de format composite. |
| arg1 | Oui | chaîne, entier ou valeur booléenne | La valeur à inclure dans la chaîne mise en forme. |
| arguments supplémentaires | Non  | chaîne, entier ou valeur booléenne | Valeurs supplémentaires à inclure dans la chaîne mise en forme. |

### <a name="remarks"></a>Remarques

Cette fonction permet de mettre en forme une chaîne dans votre modèle. Il utilise les mêmes options de mise en forme en tant que le [System.String.Format](/dotnet/api/system.string.format) méthode dans .NET.

### <a name="examples"></a>Exemples

Le modèle de l’exemple suivant montre comment utiliser la fonction format.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| formatTest | Chaîne | Bonjour, utilisateur. Nombre mis en forme : 8,175,133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Crée une valeur sous la forme d’un identificateur global unique basé sur les valeurs fournies comme paramètres.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Oui |string |Valeur utilisée dans la fonction de hachage pour créer le GUID. |
| paramètres supplémentaires le cas échéant |Non  |string |Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité. |

### <a name="remarks"></a>Remarques

Cette fonction est utile quand vous devez créer une valeur sous la forme d’un identificateur global unique. Vous fournissez des valeurs de paramètre qui limitent l’étendue d’unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour l’abonnement, le groupe de ressources ou le déploiement.

La valeur retournée n’est pas une chaîne aléatoire, mais plutôt le résultat d’une fonction de hachage sur les paramètres. La valeur renvoyée comprend 36 caractères. Il n’est pas globalement unique. Pour créer un nouveau GUID n’est pas basé sur cette valeur de hachage des paramètres, utilisez le [newGuid](#newguid) (fonction).

Les exemples suivants montrent comment utiliser guid pour créer une valeur unique pour des niveaux couramment utilisés.

Unique limité à l’abonnement

```json
"[guid(subscription().subscriptionId)]"
```

Unique limité au groupe de ressources

```json
"[guid(resourceGroup().id)]"
```

Unique limité au déploiement pour un groupe de ressources

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valeur de retour

Chaîne contenant 36 caractères sous la forme d’un identificateur global unique.

### <a name="examples"></a>Exemples

L’[exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) suivant retourne les résultats à partir de guid :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Retourne la première position d’une valeur dans une chaîne. La comparaison respecte la casse.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur de retour

Entier qui représente la position de l’élément à rechercher. La valeur est basée sur zéro. Si l’élément est introuvable, -1 est retourné.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) suivant montre comment utiliser les fonctions indexOf et lastIndexOf :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

Retourne le dernier caractère de la chaîne ou le dernier élément du tableau.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |La valeur permettant de récupérer le dernier élément ou caractère. |

### <a name="return-value"></a>Valeur de retour

Chaîne du dernier caractère ou type (chaîne, entier, tableau ou objet) du dernier élément d’un tableau.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) suivant montre comment utiliser la dernière fonction avec un tableau et une chaîne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Chaîne | three |
| stringOutput | Chaîne | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Retourne la dernière position d’une valeur dans une chaîne. La comparaison respecte la casse.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur de retour

Entier qui représente la dernière position de l’élément à rechercher. La valeur est basée sur zéro. Si l’élément est introuvable, -1 est retourné.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) suivant montre comment utiliser les fonctions indexOf et lastIndexOf :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Retourne le nombre de caractères dans une chaîne ou le nombre d’éléments dans un tableau.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |tableau ou chaîne |Tableau à utiliser pour l’obtention du nombre d’éléments, ou chaîne à utiliser pour l’obtention du nombre de caractères. |

### <a name="return-value"></a>Valeur de retour

Un entier. 

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) suivant montre comment utiliser length avec un tableau et une chaîne :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |

## <a name="newguid"></a>newGuid

`newGuid()`

Retourne une valeur dans le format d’un identificateur global unique. **Cette fonction peut uniquement être utilisée dans la valeur par défaut pour un paramètre.**

### <a name="remarks"></a>Remarques

Vous pouvez uniquement utiliser cette fonction dans une expression pour la valeur par défaut d’un paramètre. À l’aide de cette fonction n’importe où dans un modèle renvoie une erreur. La fonction n’est pas autorisée dans d’autres parties du modèle, car elle retourne une valeur différente à chaque fois qu’elle est appelée. Déploiement du même modèle avec les mêmes paramètres n’aurait pas produire fiable les mêmes résultats.

La fonction newGuid diffère la [guid](#guid) , car il n’accepte aucun paramètre de fonction. Lorsque vous appelez guid avec le même paramètre, elle retourne le même identificateur à chaque fois. Utilisez le guid lorsque vous avez besoin générer de manière fiable le même GUID pour un environnement spécifique. Utilisez newGuid lorsque vous avez besoin à un autre identificateur à chaque fois, telles que le déploiement des ressources dans un environnement de test.

Si vous utilisez le [option redéployer un déploiement réussi précédemment](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)et que le déploiement antérieures inclut un paramètre qui utilise newGuid, le paramètre n’est pas réévalué. Au lieu de cela, la valeur du paramètre du déploiement antérieures est automatiquement réutilisée dans le déploiement de la restauration.

Dans un environnement de test, vous devrez peut-être déployer à plusieurs reprises des ressources qui résident uniquement pendant une courte période. Au lieu de construire des noms uniques, vous pouvez utiliser newGuid avec [uniqueString](#uniquestring) pour créer des noms uniques.

Soyez prudent redéployez un modèle qui s’appuie sur la fonction newGuid pour une valeur par défaut. Lorsque vous redéployez et que vous ne fournissez pas une valeur pour le paramètre, la fonction est réévaluée. Si vous souhaitez mettre à jour une ressource existante au lieu de créer un nouveau, passez la valeur de paramètre à partir du déploiement antérieures.

### <a name="return-value"></a>Valeur de retour

Chaîne contenant 36 caractères sous la forme d’un identificateur global unique.

### <a name="examples"></a>Exemples

L’exemple de modèle suivant illustre un paramètre avec un nouvel identificateur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

La sortie de l’exemple précédent varie pour chaque déploiement, mais sera semblable à :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

L’exemple suivant utilise la fonction newGuid pour créer un nom unique pour un compte de stockage. Ce modèle peut fonctionner pour l’environnement de test dans lequel le compte de stockage existe pendant une courte période et n’est pas redéployé.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

La sortie de l’exemple précédent varie pour chaque déploiement, mais sera semblable à :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Renvoie une chaîne alignée à droite en lui ajoutant des caractères sur la gauche jusqu’à ce que la longueur totale spécifiée ait été atteinte.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| valeur_à_remplir |Oui |chaîne ou entier |Valeur à aligner à droite. |
| longueur_totale |Oui |int |Nombre total de caractères de la chaîne renvoyée. |
| caractère_de_remplissage |Non  |caractère unique |Caractère de remplissage à insérer sur la gauche jusqu’à ce que la longueur totale soit atteinte. La valeur par défaut est un espace. |

Si la chaîne d’origine est plus longue que le nombre de caractères de remplissage, aucun caractère n’est ajouté.

### <a name="return-value"></a>Valeur de retour

Chaîne avec au moins le nombre de caractères spécifié.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) suivant montre comment remplir la valeur de paramètre fournie par l’utilisateur avec le caractère zéro jusqu’à atteindre le nombre total de caractères. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| stringOutput | Chaîne | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Renvoie une nouvelle chaîne dans laquelle toutes les instances d’une chaîne ont été remplacées par une autre.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_initiale |Oui |string |La valeur qu’ont toutes les instances d’une chaîne ont été remplacées par une autre. |
| oldString |Oui |string |Chaîne à supprimer de la chaîne initiale. |
| newString |Oui |string |Chaîne à ajouter à la place de la chaîne supprimée. |

### <a name="return-value"></a>Valeur de retour

Chaîne contenant les caractères remplacés.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) suivant montre comment supprimer tous les tirets de la chaîne fournie par l’utilisateur et comment remplacer une partie de la chaîne par une autre chaîne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| firstOutput | Chaîne | 1231231234 |
| secodeOutput | Chaîne | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Retourne une chaîne avec tous les caractères après le nombre spécifié de caractères, ou un tableau avec tous les éléments après le nombre spécifié d’éléments.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à utiliser pour ignorer les caractères. |
| numberToSkip |Oui |int |Nombre d’éléments ou de caractères à ignorer. Si cette valeur est inférieure ou égale à 0, tous les éléments ou caractères de la valeur sont renvoyés. Si elle est supérieure à la longueur du tableau ou de chaîne, un tableau vide ou une chaîne est retournée. |

### <a name="return-value"></a>Valeur de retour

Tableau ou chaîne.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) suivant ignore le nombre spécifié d’éléments dans le tableau et le nombre spécifié de caractères dans une chaîne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Tableau | ["three"] |
| stringOutput | Chaîne | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Renvoie un tableau de chaînes qui contient les sous-chaînes de la chaîne d’entrée séparées par les délimiteurs spécifiés.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_entrée |Oui |string |Chaîne à fractionner. |
| delimiter |Oui |chaîne ou tableau de chaînes |Le séparateur à utiliser pour fractionner la chaîne. |

### <a name="return-value"></a>Valeur de retour

Tableau de chaînes.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) suivant fractionne la chaîne d’entrée à l’aide d’une virgule et d’une virgule ou d’un point-virgule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| firstOutput | Tableau | ["one", "two", "three"] |
| secondOutput | Tableau | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Détermine si une chaîne commence par une valeur. La comparaison respecte la casse.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Oui |string |La valeur qui contient l’élément à rechercher. |
| stringToFind |Oui |string |La valeur à trouver. |

### <a name="return-value"></a>Valeur de retour

**True** si le ou les premiers caractères de la chaîne correspondent à la valeur ; sinon, **False**.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) suivant montre comment utiliser les fonctions startsWith et endsWith :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | true |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Convertit la valeur spécifiée en chaîne.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Oui | Quelconque |Valeur à convertir en chaîne. N’importe quel type de valeur peut être converti, y compris les objets et des tableaux. |

### <a name="return-value"></a>Valeur de retour

Chaîne de la valeur convertie.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) suivant montre comment convertir différents types de valeurs en chaînes :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| objectOutput | Chaîne | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | Chaîne | ["a","b","c"] |
| intOutput | Chaîne | 5. |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Retourne une sous-chaîne qui commence à la position de caractère spécifiée et qui contient le nombre de caractères spécifié.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_analyser |Oui |string |La chaîne d’origine de laquelle la sous-chaîne est extraite. |
| index_début |Non  |int |La position de caractère (commençant à zéro) de la sous-chaîne. |
| length |Non  |int |Le nombre de caractères de la sous-chaîne. Doit faire référence à un emplacement au sein de la chaîne. Doit être égal à zéro ou supérieur. |

### <a name="return-value"></a>Valeur de retour

Sous-chaîne. Ou une chaîne vide si la longueur est égale à zéro.

### <a name="remarks"></a>Remarques

La fonction échoue lorsque la sous-chaîne s’étend au-delà de la fin de la chaîne ou lorsque la longueur est inférieure à zéro. L’exemple suivant échoue avec l’erreur « Les paramètres d’index et de longueur doivent correspondre à un emplacement au sein de la chaîne. Le paramètre d'index : '0', le paramètre de longueur : '11', le paramètre de longueur de la chaîne : '10' ».

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) suivant extrait une sous-chaîne à partir d’un paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| substringOutput | Chaîne | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Retourne une chaîne avec le nombre spécifié de caractères à partir du début de la chaîne, ou un tableau avec le nombre spécifié d’éléments à partir du début du tableau.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Oui |tableau ou chaîne |Tableau ou chaîne à partir duquel les éléments sont tirés. |
| numberToTake |Oui |int |Nombre d’éléments ou de caractères à prendre. Si cette valeur est inférieure ou égale à 0, une chaîne ou un tableau vide est renvoyé. Si elle est supérieure à la longueur de la chaîne ou tableau donné, tous les éléments du tableau ou de la chaîne sont retournés. |

### <a name="return-value"></a>Valeur de retour

Tableau ou chaîne.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) suivant prend le nombre spécifié d’éléments du tableau, et les caractères d’une chaîne.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| arrayOutput | Tableau | ["one", "two"] |
| stringOutput | Chaîne | sur |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Convertit la chaîne spécifiée en minuscules.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |string |La valeur à convertir en minuscules. |

### <a name="return-value"></a>Valeur de retour

Chaîne convertie en minuscules.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) suivant convertit une valeur de paramètre en minuscules et en majuscules.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| toLowerOutput | Chaîne | one two three |
| toUpperOutput | Chaîne | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Convertit la chaîne spécifiée en majuscules.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| chaîne_à_modifier |Oui |string |La valeur à convertir en majuscules. |

### <a name="return-value"></a>Valeur de retour

Chaîne convertie en majuscules.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) suivant convertit une valeur de paramètre en minuscules et en majuscules.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| toLowerOutput | Chaîne | one two three |
| toUpperOutput | Chaîne | ONE TWO THREE |

## <a name="trim"></a>découper

`trim (stringToTrim)`

Supprime tous les espaces de début et de fin de la chaîne indiquée.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Oui |string |La valeur à supprimer. |

### <a name="return-value"></a>Valeur de retour

Chaîne sans les premiers et derniers caractères d’espace.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) suivant supprime les espaces blancs du paramètre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| return | Chaîne | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Crée une chaîne de hachage déterministe basée sur les valeurs fournies en tant que paramètres. 

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseString |Oui |string |La valeur utilisée dans la fonction de hachage pour créer une chaîne unique. |
| paramètres supplémentaires le cas échéant |Non  |string |Vous pouvez ajouter autant de chaînes que nécessaire pour créer la valeur qui spécifie le niveau d’unicité. |

### <a name="remarks"></a>Remarques

Cette fonction est utile lorsque vous avez besoin de créer un nom unique pour une ressource. Vous fournissez des valeurs de paramètre qui limitent l’étendue d’unicité pour le résultat. Vous pouvez spécifier si le nom est unique pour l’abonnement, le groupe de ressources ou le déploiement. 

La valeur retournée n’est pas une chaîne aléatoire, mais plutôt le résultat d’une fonction de hachage. La valeur renvoyée comprend 13 caractères. Il n’est pas globalement unique. Il se peut que vous souhaitiez associer un préfixe de votre convention d’affectation de noms à la valeur pour créer un nom explicite. L’exemple suivant montre le format de la valeur renvoyée. La valeur réelle varie en fonction des paramètres fournis.

    tcvhiyu5h2o5o

Les exemples suivants montrent comment utiliser uniqueString afin de créer une valeur unique pour des niveaux couramment utilisés.

Unique limité à l’abonnement

```json
"[uniqueString(subscription().subscriptionId)]"
```

Unique limité au groupe de ressources

```json
"[uniqueString(resourceGroup().id)]"
```

Unique limité au déploiement pour un groupe de ressources

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

L'exemple suivant montre comment créer un nom unique pour un compte de stockage basé sur votre groupe de ressources. Dans le groupe de ressources, le nom n’est pas unique si construit de la même façon.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Si vous avez besoin créer un nouveau nom unique à chaque fois que vous déployez un modèle et n’envisagez pas de mettre à jour de la ressource, vous pouvez utiliser la [utcNow](#utcnow) avec uniqueString (fonction). Vous pouvez utiliser cette approche dans un environnement de test. Pour obtenir un exemple, consultez [utcNow](#utcnow).

### <a name="return-value"></a>Valeur de retour

Chaîne contenant 13 caractères.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) suivant retourne les résultats à partir d’uniquestring :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>URI

`uri (baseUri, relativeUri)`

Crée un URI absolu en combinant le baseUri et la chaîne relativeUri.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Oui |string |La chaîne d’URI de base. |
| relativeUri |Oui |string |La chaîne d’URI relatif à ajouter à la chaîne d’URI de base. |

La valeur du paramètre **baseUri** peut inclure un fichier spécifique, mais seul le chemin de base est utilisé lors de la construction de l’URI. Par exemple, si vous passez `http://contoso.com/resources/azuredeploy.json` comme paramètre baseUri, l’URI de base résultant est `http://contoso.com/resources/`.

### <a name="return-value"></a>Valeur de retour

Chaîne représentant l’URI absolu pour les valeurs de base et relative.

### <a name="examples"></a>Exemples

L’exemple suivant montre comment créer un lien vers un modèle imbriqué en fonction de la valeur du modèle parent.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| uriOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Chaîne | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Encode un URI.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Oui |string |Valeur à encoder. |

### <a name="return-value"></a>Valeur de retour

Chaîne de la valeur encodée de l’URI.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| uriOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Chaîne | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Retourne une chaîne de la valeur encodée de l’URI.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Oui |string |Valeur encodée de l’URI à convertir en une chaîne. |

### <a name="return-value"></a>Valeur de retour

Chaîne décodée de la valeur encodée de l’URI.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) suivant montre comment utiliser les paramètres uri, uriComponent et uriComponentToString :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| uriOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Chaîne | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Chaîne | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Retourne la valeur de date/heure actuelle (UTC) au format spécifié. Si aucun format n’est fourni, le format ISO 8601 (AAAAMMJJThhmmssZ) est utilisé. **Cette fonction peut uniquement être utilisée dans la valeur par défaut pour un paramètre.**

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| format |Non  |string |Valeur encodée de l’URI à convertir en une chaîne. Utilisez [chaînes de format standard](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou [chaînes de format personnalisées](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Remarques

Vous pouvez uniquement utiliser cette fonction dans une expression pour la valeur par défaut d’un paramètre. À l’aide de cette fonction n’importe où dans un modèle renvoie une erreur. La fonction n’est pas autorisée dans d’autres parties du modèle, car elle retourne une valeur différente à chaque fois qu’elle est appelée. Déploiement du même modèle avec les mêmes paramètres n’aurait pas produire fiable les mêmes résultats.

Si vous utilisez le [option redéployer un déploiement réussi précédemment](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)et que le déploiement antérieures inclut un paramètre qui utilise utcNow, le paramètre n’est pas réévalué. Au lieu de cela, la valeur du paramètre du déploiement antérieures est automatiquement réutilisée dans le déploiement de la restauration.

Soyez prudent redéployez un modèle qui s’appuie sur la fonction utcNow pour une valeur par défaut. Lorsque vous redéployez et que vous ne fournissez pas une valeur pour le paramètre, la fonction est réévaluée. Si vous souhaitez mettre à jour une ressource existante au lieu de créer un nouveau, passez la valeur de paramètre à partir du déploiement antérieures.

### <a name="return-value"></a>Valeur de retour

La valeur de date/heure UTC actuelle.

### <a name="examples"></a>Exemples

L’exemple de modèle suivant montre des formats différents pour la valeur de date/heure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

La sortie de l’exemple précédent varie pour chaque déploiement, mais sera semblable à :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

L’exemple suivant montre comment utiliser une valeur à partir de la fonction lors de la définition d’une valeur de balise.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


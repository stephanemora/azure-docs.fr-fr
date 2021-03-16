---
title: Types de données dans les modèles
description: Décrit les types de données disponibles dans les modèles Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125001"
---
# <a name="data-types-in-arm-templates"></a>Types de données dans les modèles ARM

Cet article décrit les types de données pris en charge dans les modèles Azure Resource Manager (modèles ARM). Il couvre les types de données JSON et Bicep.

## <a name="supported-types"></a>Types pris en charge

Dans un modèle ARM, vous pouvez utiliser les types de données suivants :

* tableau
* bool
* int
* object
* secureObject : indiqué par un modificateur dans Bicep
* secureString : indiqué par un modificateur dans Bicep
* string

## <a name="arrays"></a>Tableaux

Les tableaux commencent par un crochet ouvrant (`[`) et se terminent par un crochet fermant (`]`).

Dans JSON, il est possible de déclarer un tableau dans une ou plusieurs lignes. Les éléments sont séparés par des virgules.

Dans Bicep, un tableau doit être déclaré dans plusieurs lignes. N’utilisez pas de virgules entre les valeurs.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Les éléments d’un tableau peuvent être du même type ou de types différents.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>valeurs booléennes

Quand vous spécifiez des valeurs booléennes, utilisez `true` ou `false`. N’entourez pas la valeur de guillemets.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Entiers

Quand vous spécifiez des valeurs entières, n’utilisez pas de guillemets.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Pour les entiers passés comme paramètres inclus, la plage de valeurs peut être limitée par le SDK ou l’outil en ligne de commande que vous utilisez pour le déploiement. Par exemple, si vous utilisez PowerShell pour déployer un modèle, les types d’entiers peuvent être compris entre -2147483648 et 2147483647. Pour éviter cette limite, spécifiez des valeurs entières élevées dans un [fichier de paramètres](parameter-files.md). Les types de ressources appliquent leurs propres limites aux propriétés d’entiers.

## <a name="objects"></a>Objets

Les objets commencent par une accolade ouvrante (`{`) et se terminent par une accolade fermante (`}`). Chaque propriété d’un objet se compose d’une clé et d’une valeur. La clé et la valeur sont séparées par un signe deux-points (`:`).

Dans JSON, la clé est placée entre guillemets doubles. Les propriétés sont séparées par des virgules.

Dans Bicep, la clé n’est pas entourée de guillemets. N’utilisez pas de virgules entre les propriétés.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>Chaînes

Dans JSON, les chaînes sont marquées par des guillemets doubles. Dans Bicep, les chaînes sont marquées avec des guillemets simples.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Sécuriser les chaînes et objets

La chaîne sécurisée utilise le même format que la chaîne, et l’objet sécurisé utilise le même format que l’objet. Lorsque vous définissez un paramètre sur une chaîne sécurisée ou un objet sécurisé, la valeur du paramètre n’est pas enregistrée dans l’historique de déploiement et n’est pas consignée. Toutefois, si vous définissez cette valeur sécurisée sur une propriété qui n’attend pas une valeur sécurisée, la valeur n’est pas protégée. Par exemple, si vous définissez une chaîne sécurisée sur une balise, cette valeur est stockée sous forme de texte brut. Utilisez des chaînes sécurisées pour les mots de passe et les secrets.

Avec Bicep, vous ajoutez le modificateur `@secure()` à une chaîne ou un objet.

L’exemple suivant présente deux paramètres sécurisés :

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir la syntaxe du modèle, consultez [Comprendre la structure et la syntaxe des modèles ARM](template-syntax.md).

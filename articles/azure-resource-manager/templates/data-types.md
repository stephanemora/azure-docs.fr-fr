---
title: Types de données dans les modèles
description: Décrit les types de données disponibles dans les modèles Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 06/24/2021
ms.openlocfilehash: 4fc69126ee1f555e71e152a7c0369e4b81b8bf6a
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894227"
---
# <a name="data-types-in-arm-templates"></a>Types de données dans les modèles ARM

Cet article décrit les types de données pris en charge dans les modèles Azure Resource Manager (modèles ARM).

## <a name="supported-types"></a>Types pris en charge

Dans un modèle ARM, vous pouvez utiliser les types de données suivants :

* tableau
* bool
* int
* object
* secureObject
* secureString
* string

## <a name="arrays"></a>Tableaux

Les tableaux commencent par un crochet ouvrant (`[`) et se terminent par un crochet fermant (`]`). Il est possible de déclarer un tableau dans une ou plusieurs lignes. Les éléments sont séparés par des virgules.

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

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('exampleArray')]"
  },
  "firstExampleArrayElement": {
    "type": "int",
    "value": "[parameters('exampleArray')[0]]"
  }
}
```

Les éléments d’un tableau peuvent être du même type ou de types différents.

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('mixedArray')]"
  },
  "firstMixedArrayElement": {
    "type": "string",
    "value": "[variables('mixedArray')[0]]"
  }
}
```

## <a name="booleans"></a>valeurs booléennes

Quand vous spécifiez des valeurs booléennes, utilisez `true` ou `false`. N’entourez pas la valeur de guillemets.

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

## <a name="integers"></a>Entiers

Quand vous spécifiez des valeurs entières, n’utilisez pas de guillemets.

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

Pour les entiers passés comme paramètres inclus, la plage de valeurs peut être limitée par le SDK ou l’outil en ligne de commande que vous utilisez pour le déploiement. Par exemple, si vous utilisez PowerShell pour déployer un modèle, les types d’entiers peuvent être compris entre -2147483648 et 2147483647. Pour éviter cette limite, spécifiez des valeurs entières élevées dans un [fichier de paramètres](parameter-files.md). Les types de ressources appliquent leurs propres limites aux propriétés d’entiers.

## <a name="objects"></a>Objets

Les objets commencent par une accolade ouvrante (`{`) et se terminent par une accolade fermante (`}`). Chaque propriété d’un objet se compose d’une `key` et d’une `value`. La `key` et la `value` sont placées entre guillemets doubles et séparées par un signe deux-points (`:`). Les propriétés sont séparées par des virgules.

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

## <a name="strings"></a>Chaînes

Les chaînes sont marquées par des guillemets doubles.

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

## <a name="secure-strings-and-objects"></a>Sécuriser les chaînes et objets

La chaîne sécurisée utilise le même format que la chaîne, et l’objet sécurisé utilise le même format que l’objet. Lorsque vous définissez un paramètre sur une chaîne sécurisée ou un objet sécurisé, la valeur du paramètre n’est pas enregistrée dans l’historique de déploiement et n’est pas consignée. Toutefois, si vous définissez cette valeur sécurisée sur une propriété qui n’attend pas une valeur sécurisée, la valeur n’est pas protégée. Par exemple, si vous définissez une chaîne sécurisée sur une balise, cette valeur est stockée sous forme de texte brut. Utilisez des chaînes sécurisées pour les mots de passe et les secrets.

L’exemple suivant présente deux paramètres sécurisés.

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

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir la syntaxe du modèle, consultez [Comprendre la structure et la syntaxe des modèles ARM](./syntax.md).
---
title: Fonctions logiques Bicep
description: Décrit les fonctions à utiliser dans un fichier Bicep pour déterminer les valeurs logiques.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: e2b0f79b2d8e0bf1f00e4db108b7a5f33a1be9a7
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026367"
---
# <a name="logical-functions-for-bicep"></a>Fonctions logiques pour Bicep

Resource Manager fournit une fonction `bool` pour Bicep. Certaines des fonctions logiques JSON Azure Resource Manager sont remplacées par des [opérateurs logiques Bicep](./operators-logical.md).

## <a name="bool"></a>bool

`bool(arg1)`

Convertit le paramètre en valeur booléenne.

### <a name="parameters"></a>Paramètres

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Oui |chaîne ou entier |La valeur à convertir en booléen. |

### <a name="return-value"></a>Valeur de retour

Valeur booléenne de la valeur convertie.

### <a name="examples"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) suivant montre comment utiliser bool avec une chaîne ou un entier.

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | Type | Valeur |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une description des sections d’un fichier Bicep, consultez [comprendre la structure et la syntaxe des fichiers Bicep](./file.md).

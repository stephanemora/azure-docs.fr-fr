---
title: Fonctions logiques Bicep
description: Décrit les fonctions à utiliser dans un fichier Bicep pour déterminer les valeurs logiques.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 8fe09057ea293300754fc35614c00a9b808da247
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524998"
---
# <a name="logical-functions-for-bicep"></a>Fonctions logiques pour Bicep

Resource Manager fournit une fonction `bool` pour Bicep. 

La plupart des fonctions logiques dans des modèles Azure Resource Manager sont remplacées par des [opérateurs logiques](./operators-logical.md) dans Bicep.

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

* Pour la plupart des opérations logiques, consultez [Opérateurs logiques Bicep](./operators-logical.md).
* Pour obtenir une description des sections d’un fichier Bicep, consultez [comprendre la structure et la syntaxe des fichiers Bicep](./file.md).

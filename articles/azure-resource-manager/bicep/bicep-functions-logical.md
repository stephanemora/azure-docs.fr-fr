---
title: Fonctions logiques Bicep
description: Décrit les fonctions à utiliser dans un fichier Bicep pour déterminer les valeurs logiques.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e3a811102c31301386135042c79c205eeb9f7f9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758424"
---
# <a name="logical-functions-for-bicep"></a>Fonctions logiques pour Bicep

Bicep fournit la fonction `bool` pour la conversion de valeurs en valeurs booléennes.

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

L’exemple suivant montre comment utiliser le booléen avec une chaîne ou un entier.

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

* Pour d’autres actions impliquant des valeurs logiques, consultez [Opérateurs logiques](./operators-logical.md).

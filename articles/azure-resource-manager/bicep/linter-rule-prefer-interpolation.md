---
title: Règle de linter - préférer l’interpolation
description: Règle de linter - préférer l’interpolation
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 952359c7c258c084e19d6faf152536a3e50212b2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164474"
---
# <a name="linter-rule---prefer-interpolation"></a>Règle de linter - préférer l’interpolation

Cette règle recherche les utilisations de la fonction concat qui peuvent être remplacées par l’interpolation de chaîne.

## <a name="returned-code"></a>Code retourné

`prefer-interpolation`

## <a name="solution"></a>Solution

Utilisez une interpolation de chaîne plutôt que la fonction concat.

L’exemple suivant échoue à ce test, car la fonction concat est utilisée.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)
```

Vous pouvez résoudre ce problème en remplaçant concat par l’interpolation de chaîne. L’exemple suivant réussit ce test.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).

---
title: Règle de linter - préférer l’interpolation
description: Règle de linter - préférer l’interpolation
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 648b924b545e801f8a97ef5202f8022252ef94c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602502"
---
# <a name="linter-rule---prefer-interpolation"></a>Règle de linter - préférer l’interpolation

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel de règles de linter est minimal et extrait des [cas de test arm-ttk](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser le linter Bicep](./linter.md).

## <a name="code"></a>Code

`prefer-interpolation`

## <a name="description"></a>Description

L’interpolation de chaîne doit être utilisée à la place de la fonction concat.

## <a name="examples"></a>Exemples

L’exemple suivant échoue à ce test, car la fonction concat est utilisée.

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

L’exemple suivant réussit ce test.

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

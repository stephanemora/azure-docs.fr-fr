---
title: Règle Linter - Simplifier l’interpolation
description: Règle Linter - Simplifier l’interpolation
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: aff3df9bd7b357dcfdfd62f87971580399b3f2ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700554"
---
# <a name="linter-rule---simplify-interpolation"></a>Règle Linter - Simplifier l’interpolation

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel de règles de linter est minimal et extrait de [arm-ttk test cases](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser le linter Bicep](./linter.md).

## <a name="code"></a>Code

`simplify-interpolation`

## <a name="description"></a>Description

Il n’est pas nécessaire d’utiliser l’interpolation pour référencer un paramètre ou une variable.

## <a name="examples"></a>Exemples

L’exemple suivant échoue à ce test.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

L’exemple suivant réussit ce test.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

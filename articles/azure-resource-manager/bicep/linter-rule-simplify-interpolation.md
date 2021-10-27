---
title: Règle Linter - Simplifier l’interpolation
description: Règle Linter - Simplifier l’interpolation
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: e2e0f9eca607ececad22c025e89b7c976cd0d05a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165351"
---
# <a name="linter-rule---simplify-interpolation"></a>Règle Linter - Simplifier l’interpolation

Cette règle recherche une syntaxe qui utilise l’interpolation de chaîne lorsqu’elle n’est pas nécessaire.

## <a name="returned-code"></a>Code renvoyé

`simplify-interpolation`

## <a name="solution"></a>Solution

Supprimez toute utilisation de l’interpolation de chaîne qui ne fait pas partie d’une expression pour combiner des valeurs.

L’exemple suivant échoue à ce test, car il fait simplement référence à un paramètre.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: '${AutomationAccountName}'
  ...
}
```

Vous pouvez résoudre ce problème en supprimant la syntaxe d’interpolation de chaîne.

```bicep
param AutomationAccountName string

resource AutomationAccount 'Microsoft.Automation/automationAccounts@2020-01-13-preview' = {
  name: AutomationAccountName
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).

---
title: Règle de linter - Le nom d’utilisateur administrateur ne doit pas être un littéral
description: Règle de linter - Le nom d’utilisateur administrateur ne doit pas être un littéral
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: fe13ed227b0f7c7d5a7b738ed675f6c5d54eded2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167795"
---
# <a name="linter-rule---admin-user-name-should-not-be-literal"></a>Règle de linter - Le nom d’utilisateur administrateur ne doit pas être un littéral

Cette règle détecte quand un nom d’utilisateur administrateur est défini sur une valeur littérale.

## <a name="returned-code"></a>Code retourné

`adminusername-should-not-be-literal`

## <a name="solution"></a>Solution

N’utilisez pas une valeur littérale ou une expression qui prend pour valeur une valeur littérale. Au lieu de cela, créez un paramètre pour le nom d’utilisateur et affectez-le au nom d’utilisateur administrateur.

L’exemple suivant échoue à ce test, car le nom d’utilisateur est une valeur littérale.

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: 'adminUsername'
    }
  }
}
```

L’exemple suivant échoue à ce test car l’expression prend une valeur littérale quand la valeur par défaut est utilisée.

```bicep
var defaultAdmin = 'administrator'
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: defaultAdmin
    }
  }
}
```

Cet exemple réussit ce test.

```bicep
@secure()
param adminUsername string
param location string
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: adminUsername
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).

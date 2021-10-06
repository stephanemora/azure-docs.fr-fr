---
title: Règle de linter - Paramètre de sécurité par défaut
description: Règle de linter - Paramètre de sécurité par défaut
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700555"
---
# <a name="linter-rule---secure-parameter-default"></a>Règle de linter - Paramètre de sécurité par défaut

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel de règles de linter est minimal et extrait de [arm-ttk test cases](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser le linter Bicep](./linter.md).

## <a name="code"></a>Code

`secure-parameter-default`

## <a name="description"></a>Description

Ne fournissez pas de valeur par défaut codée en dur pour un [paramètre sécurisé](./parameters.md#secure-parameters) dans votre modèle, sauf si elle est vide ou s’il s’agit d’une expression contenant un appel à [newGuid()](./bicep-functions-string.md#newguid).

Vous utilisez l’élément décoratif @secure() sur des paramètres qui contiennent des valeurs sensibles, comme des mots de passe. Lorsqu’un paramètre utilise un élément décoratif sécurisé, sa valeur n’est pas enregistrée ou stockée dans l’historique de déploiement. Cette action empêche un utilisateur malveillant de découvrir la valeur sensible.

Toutefois, lorsque vous fournissez une valeur par défaut pour un paramètre sécurisé, cette valeur est détectable par quiconque peut accéder au modèle ou à l’historique de déploiement.

## <a name="examples"></a>Exemples

L’exemple suivant échoue à ce test :

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

Les exemples suivants réussissent ce test :

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

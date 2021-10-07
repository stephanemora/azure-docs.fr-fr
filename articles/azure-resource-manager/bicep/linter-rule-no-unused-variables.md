---
title: 'Règle de linter : aucune variable inutilisée'
description: 'Règle de linter : aucune variable inutilisée'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 80ace33219024f19792ee909c092e498b645665e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602476"
---
# <a name="linter-rule---no-unused-variables"></a>Règle de linter : aucune variable inutilisée

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel des règles de linter est minimal et provient des [cas de test arm-ttk](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser un linter Bicep](./linter.md).

## <a name="code"></a>Code

`no-unused-vars`

## <a name="description"></a>Description

Pour améliorer la lisibilité de votre modèle, supprimez toutes les variables qui sont définies mais qui ne sont pas utilisées. Ce test recherche les variables qui ne sont utilisées nulle part dans le modèle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

---
title: 'Règle de linter : aucun paramètre inutilisé'
description: 'Règle de linter : aucun paramètre inutilisé'
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: e8304948c2b373d16a7fa48c38ac4bac9ec806df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700641"
---
# <a name="linter-rule---no-unused-parameters"></a>Règle de linter : aucun paramètre inutilisé

Le linter facilite l’application des normes de codage en donnant des conseils pendant le développement. L’ensemble actuel de règles de linter est minimal et extrait des [cas de test arm-ttk](../templates/template-test-cases.md) :

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Pour plus d’informations, consultez [Utiliser le linter Bicep](./linter.md).

## <a name="code"></a>Code

`no-unused-params`

## <a name="description"></a>Description

Pour améliorer la lisibilité de votre modèle, supprimez tous les paramètres qui sont définis mais qui ne sont pas utilisés. Ce test recherche les paramètres qui ne sont utilisés nulle part dans le modèle. L’élimination des paramètres inutilisés facilite également le déploiement de votre modèle, car vous n’avez pas besoin de fournir des valeurs inutiles.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Visual Studio Code et l’extension Bicep, consultez [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

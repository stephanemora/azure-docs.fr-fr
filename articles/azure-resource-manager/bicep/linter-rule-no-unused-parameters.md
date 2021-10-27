---
title: 'Règle de linter : aucun paramètre inutilisé'
description: 'Règle de linter : aucun paramètre inutilisé'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: d02d13c44fd6cc6a174f629c8f3e32777bd643af
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161547"
---
# <a name="linter-rule---no-unused-parameters"></a>Règle de linter : aucun paramètre inutilisé

Cette règle recherche les paramètres qui ne sont pas référencés dans le fichier Bicep.

## <a name="returned-code"></a>Code retourné

`no-unused-params`

## <a name="solution"></a>Solution

Pour améliorer la lisibilité de votre modèle, supprimez tous les paramètres qui sont définis mais qui ne sont pas utilisés. Ce test recherche les paramètres qui ne sont utilisés nulle part dans le modèle. L’élimination des paramètres inutilisés facilite également le déploiement de votre modèle, car vous n’avez pas besoin de fournir des valeurs inutiles.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).

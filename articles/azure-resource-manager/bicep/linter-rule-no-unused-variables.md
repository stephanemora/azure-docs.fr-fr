---
title: 'Règle de linter : aucune variable inutilisée'
description: 'Règle de linter : aucune variable inutilisée'
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 3b59b8d72d12c365952dd8eef2c65a0f851ea561
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161869"
---
# <a name="linter-rule---no-unused-variables"></a>Règle de linter : aucune variable inutilisée

Cette règle recherche les variables qui ne sont référencées nulle part dans le fichier Bicep.

## <a name="returned-code"></a>Code renvoyé

`no-unused-vars`

## <a name="solution"></a>Solution

Pour améliorer la lisibilité de votre modèle, supprimez toutes les variables qui sont définies mais qui ne sont pas utilisées. Ce test recherche les variables qui ne sont utilisées nulle part dans le modèle.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le linter, consultez [Utiliser le linter Bicep](./linter.md).

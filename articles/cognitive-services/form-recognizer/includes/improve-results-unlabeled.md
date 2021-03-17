---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467237"
---
Examinez les valeurs `"confidence"` pour chaque résultat de clé/valeur sous le nœud `"pageResults"`. Vous devez également examiner les scores de confiance dans le nœud `"readResults"`, qui correspondent à l’opération de lecture de texte. La confiance des résultats de lecture n’affecte pas la confiance des résultats de l’extraction de clé/valeur. Vous devez donc vérifier les deux.
* Si les scores de confiance pour l’opération de lecture sont faibles, essayez d’améliorer la qualité de vos documents d’entrée (voir [Critères des entrées](../overview.md#input-requirements)).
* Si les scores de confiance pour l’opération d’extraction de clé/valeur sont faibles, assurez-vous que les documents en cours d’analyse sont du même type que les documents utilisés dans le jeu d’entraînement. Si les documents du jeu d’entraînement présentent des variantes d’apparence, envisagez de les diviser en différents dossiers et d’entraîner un modèle pour chaque variante.

Les scores de confiance que vous ciblez dépendent de votre cas d’usage, mais il est généralement recommandé de cibler un score de 80 % ou plus. Pour les cas plus sensibles, tels que la lecture de dossiers médicaux ou de relevés de facturation, un score de 100 % est recommandé.
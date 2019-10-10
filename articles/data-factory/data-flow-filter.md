---
title: Transformation de filtre de mappage de flux de données pour Azure Data Factory
description: Transformation de filtre de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029360"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformation de filtre Azure Data Factory



Les transformations de filtre fournissent un filtrage des lignes. Créez une expression qui définit la condition de filtre. Cliquez sur la zone de texte pour lancer le Générateur d'expressions. Dans le Générateur d'expressions, créez une expression de filtre pour déterminer quelles lignes du flux de données actuel sont autorisées à passer (filtre) à la transformation suivante. Considérez la transformation de filtre en tant que clause WHERE d’une instruction SQL.

## <a name="filter-on-loan_status-column"></a>Filtrer sur la colonne loan_status :

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrer sur la colonne année de la démo Films :

```
year > 1980
```

## <a name="next-steps"></a>Étapes suivantes

Essayer une colonne de filtrage de transformation, [transformation de sélection](data-flow-select.md)

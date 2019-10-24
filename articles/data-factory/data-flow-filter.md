---
title: Transformation de filtre de flux de données de mappage pour Azure Data Factory
description: Transformation de filtre de flux de données de mappage pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387810"
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

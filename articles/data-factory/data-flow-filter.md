---
title: Transformation de filtre de mappage de flux de données pour Azure Data Factory
description: Transformation de filtre de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234411"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformation de filtre de fabrique de données Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les transformations de filtre fournissent un filtrage des lignes. Créez une expression qui définit la condition de filtre. Cliquez sur la zone de texte pour lancer le Générateur d'expressions. Dans le Générateur d'expressions, créez une expression de filtre pour déterminer quelles lignes du flux de données actuel sont autorisées à passer (filtre) à la transformation suivante. Considérez la transformation de filtre en tant que la clause WHERE d’une instruction SQL.

## <a name="filter-on-loanstatus-column"></a>Filtrer sur la colonne de loan_status :

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrer sur la colonne année de la démo Films :

```
year > 1980
```

## <a name="next-steps"></a>Étapes suivantes

Essayez une colonne de filtrage de transformation, le [sélectionnez transformation](data-flow-select.md)

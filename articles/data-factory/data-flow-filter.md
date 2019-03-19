---
title: Transformation de filtre de mappage de flux de données pour Azure Data Factory
description: Transformation de filtre de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864726"
---
# <a name="azure-data-factoryfilter-transformation"></a>Transformation de FactoryFilter de données Azure

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

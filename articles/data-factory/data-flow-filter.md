---
title: Transformation de filtre de mappage de flux de données pour Azure Data Factory
description: Transformation de filtre de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271165"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Transformation de filtre de mappage de flux de données pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Les transformations de filtre fournissent un filtrage des lignes. Créez une expression qui définit la condition de filtre. Cliquez sur la zone de texte pour lancer le Générateur d'expressions. Dans le Générateur d'expressions, créez une expression de filtre pour déterminer quelles lignes du flux de données actuel sont autorisées à passer (filtre) à la transformation suivante.

Par exemple, filtrer sur la colonne état_prêt :

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrer sur la colonne année de la démo Films :

```
year > 1980
```

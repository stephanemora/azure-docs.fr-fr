---
title: Fichier Include
description: Fichier Include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76279388"
---
Spécifie le nombre d’appels de fonction agrégés lorsque vous [calculez des métriques pour Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriété |Default  | Description |
|---------|---------|---------| 
|batchSize|1 000|Nombre maximal de requêtes à agréger.| 
|flushTimeout|00:00:30|Période maximale d’agrégation.| 

Les appels de fonction sont agrégés lorsque la première des deux limites est atteinte.

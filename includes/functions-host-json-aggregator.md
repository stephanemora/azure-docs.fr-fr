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
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
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

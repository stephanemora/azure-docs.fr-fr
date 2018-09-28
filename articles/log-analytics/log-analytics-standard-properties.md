---
title: Propriétés standard dans les enregistrements Azure Monitor Log Analytics | Microsoft Docs
description: Décrit les propriétés communes à plusieurs types de données dans Azure Monitor Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955865"
---
# <a name="standard-properties-in-log-analytics-records"></a>Propriétés standard dans les enregistrements Log Analytics
Les données dans [Log Analytics](../log-analytics/log-analytics-queries.md) sont stockées sous la forme d’un jeu d’enregistrements, chacun ayant un type de données particulier comportant un ensemble de propriétés unique. De nombreux types de données comportent des propriétés standard qui sont communes à plusieurs types. Cet article décrit ces propriétés et fournit des exemples sur la façon dont vous pouvez les utiliser dans des requêtes.

Certaines de ces propriétés sont toujours en cours d’implémentation, vous pouvez donc les voir dans certains types de données, mais pas encore dans d’autres.


## <a name="resourceid"></a>_ResourceId
La propriété **_ResourceId** contient un identificateur unique de la ressource à laquelle l’enregistrement est associé. Vous disposez ainsi d’une propriété standard à utiliser pour étendre votre requêtes aux enregistrements d’une ressource particulière seulement, ou pour associer des données liées entre plusieurs tables.

Pour les ressources Azure, la valeur de **_ResourceId** est l’[URL d’ID de la ressource Azure](../azure-resource-manager/resource-group-template-functions-resource.md). La propriété est actuellement limitée aux ressources Azure, mais sera étendue aux ressources en dehors d’Azure comme des ordinateurs locaux. 

### <a name="examples"></a>Exemples
L’exemple suivant montre une requête qui associe des données de performances et d’événement pour chaque ordinateur. Il montre tous les événements dont l’ID est _101_ et l’utilisation des processeurs est supérieure à 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

L’exemple suivant montre une requête qui associe des enregistrements _AzureActivity_ à des enregistrements _SecurityEvent_. Il montre toutes les opérations d’activité avec les utilisateurs qui se sont connectés à ces machines.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le stockage des [données Log Analytics](../log-analytics/log-analytics-queries.md).
- Obtenez une leçon sur l’[écriture de requêtes dans Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Obtenez une leçon sur l’[association de tables dans Log Analytics](../log-analytics/query-language/joins.md).
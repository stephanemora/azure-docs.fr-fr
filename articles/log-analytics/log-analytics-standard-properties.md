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
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: c43f8acf7f9278ca412bee9ad15f2d51541cd27b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044329"
---
# <a name="standard-properties-in-log-analytics-records"></a>Propriétés standard dans les enregistrements Log Analytics
Les données dans [Log Analytics](../log-analytics/log-analytics-queries.md) sont stockées sous la forme d’un jeu d’enregistrements, chacun ayant un type de données particulier comportant un ensemble de propriétés unique. De nombreux types de données comportent des propriétés standard qui sont communes à plusieurs types. Cet article décrit ces propriétés et fournit des exemples sur la façon dont vous pouvez les utiliser dans des requêtes.

Certaines de ces propriétés sont toujours en cours d’implémentation, vous pouvez donc les voir dans certains types de données, mais pas encore dans d’autres.

## <a name="timegenerated"></a>TimeGenerated
La propriété **TimeGenerated** contient la date et l’heure auxquelles l’enregistrement a été créé. Elle fournit une propriété commune à utiliser pour le filtrage ou pour effectuer un résumé par heure. Quand vous sélectionnez un intervalle de temps pour un affichage ou un tableau de bord dans le Portail Azure, la propriété TimeGenerated est utilisée pour filtrer les résultats.

### <a name="examples"></a>Exemples

La requête suivante renvoie le nombre d’événements d’erreur créés pour chaque jour de la semaine précédente.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>type
La propriété **Type** contient le nom du tableau à partir duquel l’enregistrement a été récupéré, qui peut également être considéré comme le type d’enregistrement. Cette propriété est utile dans les requêtes qui combinent des enregistrements issus de plusieurs tableaux, comme ceux utilisés par l’opérateur `search`, pour distinguer les enregistrements de différents types. **$table** peut être utilisé à la place de **Type** à certains endroits.

### <a name="examples"></a>Exemples
La requête suivante renvoie le nombre d’enregistrements par type collectés au cours de la dernière heure.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
La propriété **\_ResourceId** contient un identificateur unique de la ressource à laquelle l’enregistrement est associé. Vous disposez ainsi d’une propriété standard à utiliser pour étendre votre requêtes aux enregistrements d’une ressource particulière seulement, ou pour associer des données liées entre plusieurs tables.

Pour les ressources Azure, la valeur de **_ResourceId** est l’[URL d’ID de la ressource Azure](../azure-resource-manager/resource-group-template-functions-resource.md). La propriété est actuellement limitée aux ressources Azure, mais sera étendue aux ressources en dehors d’Azure comme des ordinateurs locaux.

> [!NOTE]
> Certains types de données ont déjà des champs qui contiennent les ID de ressource Azure ou au moins des parties de ceux-ci, comme l’ID d’abonnement. Même si ces fichiers sont conservés à des fins de compatibilité descendante, il est recommandé d’utiliser la propriété _ResourceId pour établir une corrélation, puisqu’elle est plus cohérente.

### <a name="examples"></a>Exemples
La requête suivante associe des données de performances et d’événement pour chaque ordinateur. Il montre tous les événements dont l’ID est _101_ et l’utilisation des processeurs est supérieure à 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

La requête suivante associe des enregistrements _AzureActivity_ à des enregistrements _SecurityEvent_. Il montre toutes les opérations d’activité avec les utilisateurs qui se sont connectés à ces machines.

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

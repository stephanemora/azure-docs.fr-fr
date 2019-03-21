---
title: Propriétés standard dans les enregistrements de journaux Azure Monitor | Microsoft Docs
description: Décrit les propriétés communes à plusieurs types de données dans les journaux Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: c01cdb967fd7f9516b4403aa4f0c76f2577d5050
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294720"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Propriétés standard dans les enregistrements de journaux Azure Monitor
Les données de journal dans Azure Monitor sont [stockées sous la forme d’un jeu d’enregistrements](../log-query/log-query-overview.md), chacun ayant un type de données particulier comportant un ensemble de propriétés unique. De nombreux types de données comportent des propriétés standard qui sont communes à plusieurs types. Cet article décrit ces propriétés et fournit des exemples sur la façon dont vous pouvez les utiliser dans des requêtes.

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

## <a name="type"></a>Type
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

Pour les ressources Azure, la valeur de **_ResourceId** est l’[URL d’ID de la ressource Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). La propriété est actuellement limitée aux ressources Azure, mais sera étendue aux ressources en dehors d’Azure comme des ordinateurs locaux.

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

La requête suivante analyse **_ResourceId** et agrégats facturé par abonnement Azure, les volumes de données.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

L’exécution d’analyses sur différents types de données étant coûteuse, utilisez ces requêtes `union withsource = tt *` avec parcimonie.

## <a name="isbillable"></a>\_IsBillable
La propriété **\_IsBillable** spécifie si les données ingérées sont facturables. Les données dont la propriété **\_IsBillable** est égale à _false_ sont collectées gratuitement et ne sont pas facturées sur votre compte Azure.

### <a name="examples"></a>Exemples
Pour obtenir la liste des ordinateurs qui envoient des types de données facturés, utilisez la requête suivante :

> [!NOTE]
> Utilisez les requêtes accompagnées de `union withsource = tt *` avec parcimonie, car l’exécution d’analyses sur différents types de données est coûteuse. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Cela peut être étendu pour renvoyer le nombre d'ordinateurs par heure qui envoient des types de données facturés :

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
La propriété **\_BilledSize** spécifie la taille en octets des données qui seront facturées sur votre compte Azure si la propriété **\_IsBillable** est true.

### <a name="examples"></a>Exemples
Pour connaître la taille des événements facturables ingérés par ordinateur, utilisez la propriété `_BilledSize` qui fournit la taille en octets :

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Pour connaître le nombre d'événements ingérés par ordinateur, utilisez la requête suivante :

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Pour connaître le nombre d'événements facturables ingérés par ordinateur, utilisez la requête suivante : 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Pour connaître le nombre de types de données facturables qui ont envoyé des données à un ordinateur spécifique, utilisez la requête suivante :

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur le stockage des [données de journal Azure Monitor](../log-query/log-query-overview.md).
- Obtenez une leçon sur [l'écriture de requêtes de journaux](../../azure-monitor/log-query/get-started-queries.md).
- Obtenez une leçon sur l’[association de tables dans les requêtes de journaux](../../azure-monitor/log-query/joins.md).

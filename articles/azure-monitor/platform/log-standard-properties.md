---
title: Propriétés standard dans les enregistrements de journaux Azure Monitor | Microsoft Docs
description: Décrit les propriétés communes à plusieurs types de données dans les journaux d’activité Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 31a6c53ec269c512ad641fcdc10469ccf16a1fe9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979743"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Propriétés standard dans les journaux Azure Monitor
Les données dans les journaux Azure Monitor sont [stockées sous la forme d’un jeu d’enregistrements dans un espace de travail Log Analytics ou dans une application Application Insights](../log-query/logs-structure.md), chacun ayant un type de données particulier associé à un ensemble unique de propriétés. De nombreux types de données comportent des propriétés standard qui sont communes à plusieurs types. Cet article décrit ces propriétés et fournit des exemples sur la façon dont vous pouvez les utiliser dans des requêtes.

> [!NOTE]
> Certaines des propriétés standard n'apparaîtront pas dans la vue schéma ou IntelliSense de Log Analytics, et n'apparaîtront pas dans les résultats des requêtes, à moins de spécifier explicitement la propriété dans la sortie.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated et timestamp
Les propriétés **TimeGenerated** (espace de travail Log Analytics) et **timestamp** (application Application Insights) contiennent la date et l’heure de création de l’enregistrement par la source de données. Pour plus d’informations, consultez [Durée d’ingestion de données de journal dans Azure Monitor](data-ingestion-time.md).

**TimeGenerated** et **timestamp** fournissent une propriété commune à utiliser pour le filtrage ou pour effectuer un résumé par heure. Quand vous sélectionnez un intervalle de temps pour une vue ou un tableau de bord dans le portail Azure, la propriété TimeGenerated ou timestamp permet de filtrer les résultats. 

### <a name="examples"></a>Exemples

La requête suivante renvoie le nombre d’événements d’erreur créés pour chaque jour de la semaine précédente.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

La requête suivante retourne le nombre d’exceptions créées chaque jour de la semaine précédente.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
La propriété **\_TimeReceived** contient la date et l'heure auxquelles l'enregistrement a été reçu par le point d'ingestion Azure Monitor dans le cloud Azure. Cela peut être utile pour identifier les problèmes de latence entre la source de données et le cloud. Par exemple, un problème de mise en réseau entraîne un retard dans l’envoi des données par un agent. Pour plus d’informations, consultez [Durée d’ingestion de données de journal dans Azure Monitor](data-ingestion-time.md).

La requête suivante fournit la latence moyenne par heure pour les enregistrements d'événements d'un agent. Cela comprend le temps entre l'agent et le cloud et le temps total pendant lequel l'enregistrement doit être disponible pour les requêtes du journal.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type et itemType
Les propriétés **Type** (espace de travail Log Analytics) et **itemType** (application Application Insights) contiennent le nom de la table d’où l’enregistrement a été récupéré, qui peut également être considéré comme le type de l’enregistrement. Cette propriété est utile dans les requêtes qui combinent des enregistrements issus de plusieurs tableaux, comme ceux utilisés par l’opérateur `search`, pour distinguer les enregistrements de différents types. **$table** peut être utilisé à la place de **Type** à certains endroits.

### <a name="examples"></a>Exemples
La requête suivante renvoie le nombre d’enregistrements par type collectés au cours de la dernière heure.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
La propriété **\_ItemId** contient un identificateur unique pour l’enregistrement.


## <a name="_resourceid"></a>\_ResourceId
La propriété **\_ResourceId** contient un identificateur unique de la ressource à laquelle l’enregistrement est associé. Vous disposez ainsi d’une propriété standard à utiliser pour étendre votre requêtes aux enregistrements d’une ressource particulière seulement, ou pour associer des données liées entre plusieurs tables.

Pour les ressources Azure, la valeur de **_ResourceId** est l’[URL d’ID de la ressource Azure](../../azure-resource-manager/templates/template-functions-resource.md). La propriété est actuellement limitée aux ressources Azure, mais sera étendue aux ressources en dehors d’Azure comme des ordinateurs locaux.

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

La requête suivante analyse **_ResourceId** et agrège les volumes de données facturés par abonnement Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

L’exécution d’analyses sur différents types de données étant coûteuse, utilisez ces requêtes `union withsource = tt *` avec parcimonie.

## <a name="_isbillable"></a>\_IsBillable
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

## <a name="_billedsize"></a>\_BilledSize
La propriété **\_BilledSize** spécifie la taille en octets des données qui seront facturées sur votre compte Azure si la propriété **\_IsBillable** est true.


### <a name="examples"></a>Exemples
Pour connaître la taille des événements facturables ingérés par ordinateur, utilisez la propriété `_BilledSize` qui fournit la taille en octets :

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Pour connaître la taille des événements facturables ingérés par abonnement, exécutez la requête suivante :

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Pour connaître la taille des événements facturables ingérés par groupe de ressources, exécutez la requête suivante :

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

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

Pour connaître le nombre de types de données facturables issues d’un ordinateur spécifique, exécutez la requête suivante :

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

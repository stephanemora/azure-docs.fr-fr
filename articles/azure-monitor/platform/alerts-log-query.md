---
title: Requêtes d’alertes de journal dans Azure Monitor | Microsoft Docs
description: Fournit des recommandations concernant l’écriture de requêtes performantes pour les alertes de journal dans les mises à jour d’Azure Monitor et un processus de conversion des requêtes existantes.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667786"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Requêtes d’alertes de journal dans Azure Monitor
[Les règles d’alerte basées sur des journaux d’activité Azure Monitor](alerts-unified-log.md) s’exécutent à intervalles réguliers ; vous devez donc vous assurer que leur écriture minime la surcharge et la latence. Cet article fournit des recommandations concernant l’écriture de requêtes performantes pour les alertes de journal et un processus de conversion des requêtes existantes. 

## <a name="types-of-log-queries"></a>Types de requêtes de journal
Les [requêtes de journal dans Azure Monitor](../log-query/log-query-overview.md) commencent par une table ou par un opérateur [search](/azure/kusto/query/searchoperator) ou [union](/azure/kusto/query/unionoperator).

Par exemple la requête suivante a pour étendue la table _SecurityEvent_ et recherche un ID d’événement spécifique. Il s’agit de la seule table que la requête doit traiter.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Les requêtes qui commencent par `search` ou `union` vous permettent d’effectuer une recherche dans plusieurs colonnes d’une table ou même plusieurs tables. Les exemples suivants illustrent plusieurs méthodes pour la recherche du terme _Memory_ :

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Bien que `search` et `union` soient utiles durant l’exploration des données, ils recherchent les termes dans le modèle de données entier et sont donc moins efficaces que le recours à une table, dans la mesure où ils doivent analyser plusieurs tables. Les requêtes dans les règles d’alerte étant exécutées à intervalles réguliers, cela peut entraîner une surcharge excessive et ajouter de la latence à l’alerte. En raison de cette surcharge, les requêtes pour les règles d’alerte dans Azure doivent toujours commencer par une table afin de définir une étendue claire, ce qui améliore à la fois les performances des requêtes et la pertinence des résultats.

## <a name="unsupported-queries"></a>Requêtes non prises en charge
À compter du 11 janvier 2019, la création et la modification de règles d’alerte de journal qui utilisent l’opérateur `search` ou `union` ne seront plus prises en charge dans le portail Azure. L’utilisation de ces opérateurs dans une règle d’alerte retournera un message d’erreur. Les règles d’alerte existantes et celles créées et modifiées avec l’API Log Analytics ne sont pas affectées par ce changement. Néanmoins, vous devez quand même envisager de modifier les règles d’alerte qui utilisent ces types de requêtes, afin d’améliorer leur efficacité.  

Les règles d’alerte de journal utilisant des [requêtes inter-ressources](../log-query/cross-workspace-query.md) ne sont pas affectées par ce changement, car les requêtes inter-ressources utilisent `union`, qui limite l’étendue de la requête à des ressources spécifiques. Cela n’équivaut pas à `union *`, qui ne peut pas être utilisé.  L’exemple suivant serait valide dans une règle d’alerte de journal :

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Les [requêtes inter-ressources](../log-query/cross-workspace-query.md) des alertes de journal sont prises en charge par la nouvelle [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Par défaut, Azure Monitor utilise l'[API Alerte Log Analytics héritée](api-alerts.md) pour créer de nouvelles règles d'alerte de journal à partir du portail Azure, sauf si vous basculez depuis l'[API Alertes de journal héritée](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Après le basculement, la nouvelle API devient la valeur par défaut des nouvelles règles d'alerte du portail Azure et vous permet de créer des règles d'alertes de journal pour les requêtes inter-ressources. Vous pouvez créer des règles d'alertes de journal pour les [requêtes inter-ressources](../log-query/cross-workspace-query.md) sans basculer en utilisant le [modèle ARM de l'API schededuRuRules](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template). Mais cette règle d'alerte est gérable via l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) et non à partir du portail Azure.

## <a name="examples"></a>Exemples
Les exemples suivants incluent des requêtes de journal qui utilisent `search` et `union`, et présentent les étapes permettant de modifier ces requêtes pour une utilisation avec des règles d’alerte.

### <a name="example-1"></a>Exemple 1
Vous souhaitez créer une règle d’alerte de journal avec la requête suivante qui récupère des informations de performances à l’aide de `search` : 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Pour modifier cette requête, commencez par exécuter la requête suivante afin d’identifier la table à laquelle appartiennent les propriétés :

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Le résultat de cette requête montre que la propriété _CounterName_ provient de la table _Perf_. 

Vous pouvez utiliser ce résultat pour créer la requête suivante, que vous utiliseriez pour la règle d’alerte :

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Exemple 2
Vous souhaitez créer une règle d’alerte de journal avec la requête suivante qui récupère des informations de performances à l’aide de `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Pour modifier cette requête, commencez par exécuter la requête suivante afin d’identifier la table à laquelle appartiennent les propriétés :

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Le résultat de cette requête montre que les propriétés _ObjectName_ et _CounterName_ proviennent de la table _Perf_. 

Vous pouvez utiliser ce résultat pour créer la requête suivante, que vous utiliseriez pour la règle d’alerte :

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Exemple 3

Vous souhaitez créer une règle d’alerte de journal avec la requête suivante qui utilise `search` et `union` pour récupérer des informations de performances : 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Pour modifier cette requête, commencez par exécuter la requête suivante afin d’identifier la table à laquelle appartiennent les propriétés dans la première partie de la requête : 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Le résultat de cette requête montre que toutes ces propriétés proviennent de la table _Perf_. 

À présent, utilisez `union` avec la commande `withsource` pour identifier la table source ayant contribué à chaque ligne.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Le résultat de cette requête montre que ces propriétés proviennent aussi de la table _Perf_. 

Vous pouvez utiliser ces résultats pour créer la requête suivante, que vous utiliseriez pour la règle d’alerte : 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Exemple 4
Vous souhaitez créer une règle d’alerte de journal avec la requête suivante qui joint les résultats de deux requêtes `search` :

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Pour modifier la requête, commencez par exécuter la requête suivante afin d’identifier la table qui contient les propriétés figurant du côté gauche de la jointure : 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Le résultat indique que les propriétés figurant du côté gauche de la jointure appartiennent à la table _SecurityEvent_. 

Exécutez maintenant la requête suivante afin d’identifier la table qui contient les propriétés figurant du côté droit de la jointure : 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Le résultat indique que les propriétés figurant du côté droit de la jointure appartiennent à la table Heartbeat. 

Vous pouvez utiliser ces résultats pour créer la requête suivante, que vous utiliseriez pour la règle d’alerte : 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en davantage sur les [alertes de journal](alerts-log.md) dans Azure Monitor.
- Apprenez-en davantage sur les [requêtes de journal](../log-query/log-query-overview.md).


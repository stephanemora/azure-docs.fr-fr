---
title: Alertes de journal à partir de Container Insights | Microsoft Docs
description: Cet article explique comment créer des alertes de journal personnalisées pour l’utilisation de la mémoire et du processeur à partir de Container Insights.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524699"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Création d’alertes de journal à partir de Container Insights

Container Insights surveille les performances des charges de travail de conteneur déployées sur des clusters Kubernetes gérés ou auto-gérés. Pour alerter sur ce qui est important, cet article décrit comment créer des alertes basées sur le journal en réponse aux situations suivantes avec des clusters AKS :

- Lorsque l’utilisation de la mémoire ou de l’UC sur les nœuds de cluster dépasse un seuil
- Lorsque l’utilisation de la mémoire ou de l’UC sur n’importe quel conteneur au sein d’un contrôleur dépasse un seuil par rapport à une limite définie sur la ressource correspondante
- Nombre de nœuds à l’état *Non prêt*
- Nombre de phases de pod à l’état *Échec*, *En attente*, *Inconnu*, *En cours d’exécution* ou *Réussite*
- Lorsque espace disque libre sur les nœuds de cluster dépasse un seuil

Pour alerter en cas d’utilisation élevée de la mémoire ou de l’UC, ou de manque d’espace disponible sur les nœuds de cluster, utilisez les requêtes fournies pour créer une alerte de métrique ou une alerte de mesure de métrique. Si les alertes métriques ont une latence inférieure à celle des alertes de journal, celles-ci offrent des capacités de requête avancées et une plus grande sophistication. Les requêtes d’alerte de journal comparent une valeur DateHeure au présent en utilisant l’opérateur *now* et en remontant d’une heure. (Container Insights stocke toutes les dates au format de temps universel coordonné (UTC).)

> [!IMPORTANT]
> La plupart des règles d’alerte ont un coût qui dépend du type de règle, du nombre de dimensions incluses et de la fréquence d’exécution. Reportez-vous à **Règles d’alerte** dans [Tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) avant de créer des règles d’alerte.

Avant de commencer, si vous n’êtes pas familiarisé avec les alertes d’Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure](../alerts/alerts-overview.md). Pour en savoir plus sur les alertes utilisant des requêtes de journal, consultez [Alertes de journal dans Azure Monitor](../alerts/alerts-unified-log.md). Pour en savoir plus sur les alertes de métrique, consultez [Alertes de métriques dans Azure Monitor](../alerts/alerts-metric-overview.md).

## <a name="log-query-measurements"></a>Mesures des requêtes de journal
Les alertes de journal peuvent effectuer deux mesures différentes du résultat d’une requête de journal, chacune prenant en charge des scénarios distincts pour la supervision des machines virtuelles.

La [mesure de métrique](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) crée une alerte distincte pour chaque enregistrement dans les résultats de requête dont la valeur dépasse un seuil défini dans la règle d’alerte. Celles-ci sont idéales pour les données numériques telles que le processeur.

Le [nombre de résultats](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) crée une alerte unique lorsqu’une requête retourne au moins un nombre spécifié d’enregistrements. Elles sont idéales pour les données non numériques, comme ou pour l’analyse des tendances de performances sur plusieurs ordinateurs. Vous pouvez également choisir cette stratégie si vous souhaitez réduire au maximum le nombre d’alertes ou créer une alerte uniquement lorsque plusieurs composants présentent la même condition d’erreur.

> [!NOTE]
> Les règles d’alerte de journal centrées sur les ressources, actuellement en préversion publique, simplifient les alertes de requête de journal et remplacent les fonctionnalités actuellement fournies par les requêtes de mesure de métrique. Vous pouvez utiliser le cluster AKS comme cible de la règle, ce qui permettra de mieux l’identifier en tant que ressource affectée. Quand les alertes de requête de journal du centre de ressources seront mises à la disposition générale, les instructions de ce scénario seront mises à jour.

## <a name="create-a-log-query-alert-rule"></a>Créer une règle d’alerte de requête de journal
La [comparaison des mesures d’alerte de requête de journal](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures) fournit une procédure pas à pas complète des règles d’alerte de requête de journal pour chaque type de mesure, y compris une comparaison des requêtes de journal qui les prennent en charge. Vous pouvez utiliser ces mêmes processus pour créer des règles d’alerte pour les clusters AKS à l’aide de requêtes similaires à celles de cet article.

## <a name="resource-utilization"></a>Utilisation des ressources 

**Utilisation moyenne du processeur en tant que moyenne de l’utilisation du processeur par les nœuds membres à chaque minute (mesure de métrique)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

**Utilisation moyenne de la mémoire en tant que moyenne de l’utilisation de la mémoire par les nœuds membres à chaque minute (mesure de métrique)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>Les requêtes suivantes utilisent les valeurs d’espace réservé \<your-cluster-name> et \<your-controller-name> pour représenter votre cluster et votre contrôleur. Remplacez-les par des valeurs propres à votre environnement lorsque vous définissez des alertes.

**Utilisation moyenne du processeur par tous les conteneurs d’un contrôleur en tant que moyenne de l’utilisation du processeur par chaque instance de conteneur dans un contrôleur à chaque minute (mesure de métrique)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

**Utilisation moyenne de la mémoire par tous les conteneurs d’un contrôleur en tant que moyenne de l’utilisation de la mémoire par chaque instance de conteneur dans un contrôleur à chaque minute (mesure de métrique)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>Disponibilité des ressources 

**Nœuds et nombres dont l’état est Ready (Prêt) et NotReady (Non prêt) (mesure métrique)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
La requête suivante renvoie le nombre de phase de pod en fonction de toutes les phases : *Échec*, *En attente*, *Inconnu*, *En cours d’exécution* ou *Réussite*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Pour alerter sur certaines phases pod, telles que *En attente*, *Échec* ou *Inconnu*, modifiez la dernière ligne de la requête. Par exemple, pour générer des alertes sur *FailedCount*, utilisez : <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

La requête suivante renvoie les disques de nœuds de clusters qui dépassent 90 % d’espace utilisé. Pour obtenir l’ID de cluster, exécutez tout d’abord la requête suivante et copiez la valeur de la propriété `ClusterId` :

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



**Redémarrages de conteneur individuels (nombre de résultats)**<br>
Avertit lorsque le nombre de redémarrages du conteneur système individuel a dépassé un seuil au cours des 10 dernières minutes.

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [exemples de requêtes de journal](container-insights-log-query.md) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.

- Pour en savoir plus sur Azure Monitor et sur la façon d’analyser d’autres aspects de votre cluster Kubernetes, consultez [Afficher les performances du cluster Kubernetes](container-insights-analyze.md) et [Afficher l’intégrité du cluster Kubernetes](./container-insights-overview.md).
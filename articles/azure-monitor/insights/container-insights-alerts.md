---
title: Créer des alertes de performances à l’aide d’Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article explique comment créer des alertes personnalisées basées sur des requêtes de journal relatives à l’utilisation de la mémoire et de l’UC à partir d’Azure Monitor pour conteneurs.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/26/2019
ms.openlocfilehash: dd92f5aedd1fbc51531730e6a7826322570cd1b1
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195018"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Comment configurer des alertes pour des problèmes de performances dans Azure Monitor pour conteneurs
Azure Monitor pour conteneurs supervise les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS).

Cet article décrit comment activer les alertes pour les situations suivantes :

- Lorsque l’utilisation de la mémoire ou de l’UC sur les nœuds de cluster dépasse un seuil
- Lorsque l’utilisation de la mémoire ou de l’UC sur n’importe quel conteneur au sein d’un contrôleur dépasse un seuil par rapport à une limite définie sur la ressource correspondante
- Nombre de nœuds à l’état *Non prêt*
- Nombre de phases de pod à l’état *Échec*, *En attente*, *Inconnu*, *En cours d’exécution* ou *Réussite*
- Lorsque espace disque libre sur les nœuds de cluster dépasse un seuil 

Pour alerter en cas d’utilisation élevée de la mémoire ou de l’UC, ou de manque d’espace disponible sur les nœuds de cluster, utilisez les requêtes fournies pour créer une alerte de métrique ou une alerte de mesure de métrique. Les alertes de métrique ont une latence plus faible que les alertes de journal. Mais les alertes de journal permettent d’effectuer des requêtes avancées et plus complexes. Les requêtes d’alerte comparent une valeur DateHeure au présent en utilisant l’opérateur *maintenant* et en remontant d’une heure. (Azure Monitor pour conteneurs stocke toutes les dates au format de temps universel coordonné (UTC).)

Avant de commencer, si vous n’êtes pas familiarisé avec les alertes d’Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure](../platform/alerts-overview.md). Pour en savoir plus sur les alertes utilisant des requêtes de journal, consultez [Alertes de journal dans Azure Monitor](../platform/alerts-unified-log.md). Pour en savoir plus sur les alertes de métrique, consultez [Alertes de métriques dans Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Requêtes de recherche de journal concernant l’utilisation des ressources
Les requêtes de cette section prennent en charge chaque scénario d’alerte. Elles sont utilisées à l’étape 7 de la section [Créer une alerte](#create-an-alert-rule) de cet article.

La requête suivante calcule l’utilisation moyenne de l’UC en tant que moyenne de l’utilisation par l’UC par les nœuds membres à chaque minute.  

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

La requête suivante calcule l’utilisation moyenne de la mémoire en tant que moyenne de l’utilisation de la mémoire par les nœuds membres à chaque minute.

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
>Les requêtes suivantes utilisent les valeurs d’espace réservé \<your-cluster-name > et \<your-controller-name > pour représenter votre cluster et votre contrôleur. Remplacez-les par des valeurs propres à votre environnement lorsque vous définissez des alertes.

La requête suivante calcule l’utilisation moyenne de l’UC de tous les conteneurs d’un contrôleur en tant que moyenne de l’utilisation de l’UC de chaque instance de conteneur dans un contrôleur à chaque minute. La mesure est un pourcentage de la limite configurée pour un conteneur.

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

La requête suivante calcule l’utilisation moyenne de la mémoire de tous les conteneurs d’un contrôleur en tant que moyenne de l’utilisation de la mémoire de chaque instance de conteneur dans un contrôleur à chaque minute. La mesure est un pourcentage de la limite configurée pour un conteneur.

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

La requête suivante renvoie tous les nœuds et combien d’entre eux ont l’état *Prêt* et *Non prêt*.

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
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
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

## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte
Suivez ces étapes pour créer une alerte de journal dans Azure Monitor en utilisant l’une des règles de recherche dans les journaux fournis précédemment.  

>[!NOTE]
>La procédure suivante visant à créer une règle d’alerte pour l’utilisation des ressources de conteneur requiert de basculer vers une nouvelle API d’alertes de journal, comme décrit dans [Changer de préférence d’API pour les alertes de journal](../platform/alerts-log-api-switch.md).
>

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Monitor** dans le volet situé à gauche. Sous **Insights**, sélectionnez **Conteneurs**.
3. Dans la liste de l’onglet **Clusters supervisés**, sélectionnez un cluster.
4. Dans le volet situé à gauche sous **Surveillance**, sélectionnez **Journaux** pour ouvrir la page de journaux Azure Monitor. Cette page vous permet d’écrire et exécuter des requêtes Azure Log Analytics.
5. Sur la page **Journaux**, sélectionnez **+ Nouvelle règle d’alerte**.
6. Dans la section **Condition**, sélectionnez la condition de journal personnalisée prédéfinie **Chaque fois que la recherche dans les journaux est \<logique non définie>** . Le type de signal **Recherche dans les journaux d’activité personnalisée** est automatiquement sélectionné pour nous parce que nous créons une règle d’alerte directement à partir de la page de journaux d’Azure Monitor.  
7. Collez l’une des [requêtes](#resource-utilization-log-search-queries) fournies précédemment dans le champ **Requête de recherche**.
8. Configurez l’alerte comme suit :

    1. Dans la liste déroulante **Basé sur**, sélectionnez **Mesure des métriques**. Une mesure des métriques crée une alerte pour chaque objet de la requête dont la valeur dépasse le seuil spécifié.
    1. Pour **Condition**, sélectionnez **Supérieur à**, puis entrez **75** comme **Seuil** de ligne de base initiale pour les alertes d’utilisation de la mémoire et de l’UC. Pour l’alerte d’espace disque faible, entrez **90**. Ou saisissez une valeur différente qui répond à vos critères.
    1. Dans la section **Déclencher l’alerte selon**, sélectionnez **Violations consécutives**. Dans la liste déroulante, sélectionnez **Supérieur à**, puis entrez **2**.
    1. Pour configurer une alerte pour l’utilisation de la mémoire ou l’UC du conteneur, sous **Agréger sur**, sélectionnez **ContainerName**. Pour configurer l’alerte de disque faible de nœud de cluster, sélectionnez **ClusterId**.
    1. Dans la section **Évaluées sur la base de**, définissez la valeur de **Période** sur **60 minutes**. La règle s’exécute toutes les 5 minutes et renvoie les enregistrements qui ont été créés au cours de la dernière heure. Le fait de définir la période sur une fenêtre d’une certaine amplitude permet de tenir compte d’une potentielle latence des données. Cela garantit également que la requête renvoie des données afin d’éviter un faux négatif dans lequel l’alerte se déclenche jamais.

9. Sélectionnez **Terminé** pour terminer la règle d’alerte.
10. Entrez un nom dans le champ **Nom de la règle d’alerte**. Spécifiez une **description** qui fournit des détails sur l’alerte. Enfin, sélectionnez un niveau de gravité approprié parmi les options proposées.
11. Pour activer immédiatement la règle d’alerte, acceptez la valeur par défaut pour l’option **Activer la règle lors de sa création**.
12. Sélectionnez un **groupe d’actions** existant ou créez-en un. Cette étape garantit que chaque déclenchement d’alerte entraîne la même action. Choisissez la configuration en fonction de la façon dont l’équipe chargée des opérations informatiques ou de DevOps gère les incidents.
13. Sélectionnez **Créer une règle d’alerte** pour terminer la règle d’alerte. Son exécution démarre immédiatement.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
- Pour en savoir plus sur Azure Monitor et sur la façon d’analyser d’autres aspects de votre cluster Kubernetes, consultez [Afficher les performances du cluster Kubernetes](container-insights-analyze.md) et [Afficher l’intégrité du cluster Kubernetes](container-insights-health.md).

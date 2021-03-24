---
title: Comment interroger des journaux à partir de Container Insights | Microsoft Docs
description: Container Insights collecte des métriques et des données de journal. Cet article décrit les enregistrements correspondants et inclut des exemples de requêtes.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201377"
---
# <a name="how-to-query-logs-from-container-insights"></a>Comment interroger des journaux à partir de Container Insights

Container Insights collecte des métriques de performances, des données d’inventaire et des informations sur l’état d’intégrité à partir d’hôtes de conteneur et de conteneurs. Les données sont collectées toutes les trois minutes et transférées à l’espace de travail Log Analytics dans Azure Monitor. Ces données sont disponibles pour la [requête](../logs/log-query-overview.md) dans Azure Monitor. Vous pouvez appliquer ces données à divers scénarios tels que la planification de la migration, l’analyse de la capacité, la détection et la résolution de problèmes de performances à la demande.

## <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente en détail les enregistrements collectés par Container Insights. Pour obtenir la liste des descriptions de colonne, consultez la référence pour les tables [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) et [ContainerLog](/azure/azure-monitor/reference/tables/containerlog).

| Données | Source de données | Type de données | Champs |
|------|-------------|-----------|--------|
| Inventaire de conteneur | Kubelet | `ContainerInventory` | TimeGenerated, Computer, Name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Journal de conteneur | Docker | `ContainerLog` | TimeGenerated, Computer, image ID, Name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventaire du nœud de conteneur | API Kube | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaire des pods dans un cluster Kubernetes | API Kube | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventaire de la partie des nœuds d’un cluster Kubernetes | API Kube | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
|Inventaire des volumes persistants dans un cluster Kubernetes |API Kube |`KubePVInventory` | TimeGenerated, PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, ClusterId, ClusterName, _ResourceId, SourceSystem |
| Événements Kubernetes | API Kube | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Services dans le cluster Kubernetes | API Kube | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métriques de performances de la partie des nœuds du cluster Kubernetes | Les métriques d’utilisation proviennent de cAdvisor et les limites de l’API Kube. | `Perf \| where ObjectName == "K8SNode"` | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques de performances de la partie des conteneurs du cluster Kubernetes | Les métriques d’utilisation proviennent de cAdvisor et les limites de l’API Kube. | `Perf \| where ObjectName == "K8SContainer"` | CounterName &#40;cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques personnalisées ||`InsightsMetrics` | Computer, Name, Namespace, Origin, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> La propriété *Tags* représente [plusieurs dimensions](../essentials/data-platform-metrics.md#multi-dimensional-metrics) pour la métrique correspondante. Pour plus d’informations sur les métriques collectées et stockées dans la table `InsightsMetrics`, ainsi qu’une description des propriétés des enregistrements, consultez [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Rechercher des journaux d’activité pour analyser les données

Les journaux Azure Monitor peuvent vous aider à rechercher des tendances, à diagnostiquer les goulots d’étranglement, à effectuer des prévisions ou à mettre en corrélation des données afin de déterminer si la configuration de cluster actuelle garantit des performances optimales. Les recherches prédéfinies dans les journaux peuvent être utilisées immédiatement. Elles peuvent également être personnalisées afin de retourner les informations comme vous le souhaitez.

Vous pouvez analyser de façon interactive des données dans l’espace de travail en sélectionnant l’option **Afficher les journaux des événements Kubernetes** ou **Afficher les journaux du conteneur** dans le volet de visualisation de la liste déroulante **Afficher dans Analytics**. La page **Recherche dans les journaux** s’affiche à droite de la page du portail Azure.

![Analyser des données dans Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Les sorties des journaux de conteneur transmises à votre espace de travail sont STDOUT et STDERR. Étant donné qu’Azure Monitor surveille des Kubernetes managés Azure (AKS), le système Kube n’est pas collecté aujourd’hui en raison de l’important volume de données générées. 

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal

Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

### <a name="list-all-of-a-containers-lifecycle-information"></a>Liste de toutes les informations sur le cycle de vie d’un conteneur

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Événements Kubernetes

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Inventaire des images

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>Processeur du conteneur

**Sélectionner l’option d’affichage de graphique en courbes**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Mémoire du conteneur

**Sélectionner l’option d’affichage de graphique en courbes**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Demandes par minute avec des métriques personnalisées

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

## <a name="query-prometheus-metrics-data"></a>Interroger les données de métriques Prometheus

L’exemple suivant est une requête de mesures Prometheus indiquant le nombre de lectures de disque par seconde, par disque et par nœud.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Pour voir les métriques Prometheus récupérées par Azure Monitor et filtrées par espace de noms, spécifiez « prometheus ». Voici un exemple de requête permettant de voir les métriques Prometheus à partir de l’espace de noms kubernetes `default`.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Les données Prometheus peuvent également être interrogées directement par nom.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Interroger les erreurs de récupération ou de configuration

Pour examiner les erreurs de récupération ou de configuration, l’exemple de requête suivant retourne des événements d’information à partir de la table `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

Les résultats obtenus sont similaires à ceux de l’exemple suivant :

![Enregistrer les résultats de requête des événements d’information à partir de l’agent](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Étapes suivantes

Container Insights n’inclut pas d’ensemble prédéfini d’alertes. Consultez [Créer des alertes de performances avec Container Insights](./container-insights-log-alerts.md) pour savoir comment créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, afin de prendre en charge vos procédures et processus opérationnels ou DevOps.
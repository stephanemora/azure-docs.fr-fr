---
title: Guide pratique pour interroger des journaux à partir d’Azure Monitor pour conteneurs | Microsoft Docs
description: Azure Monitor pour conteneurs collecte des métriques et des données de journal. Cet article décrit les enregistrements correspondants et inclut des exemples de requêtes.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: d6e65331db53be5ba13a75e6b03b271f1071716d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67989831"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Guide pratique pour interroger des journaux à partir d’Azure Monitor pour conteneurs

Azure Monitor pour conteneurs collecte des métriques de performances, les données d’inventaire et des informations concernant l’état d’intégrité à partir d’hôtes de conteneur et de conteneurs, puis les transfère à l’espace de travail Log Analytics dans Azure Monitor. Les données sont collectées toutes les trois minutes. Ces données sont disponibles pour la [requête](../../azure-monitor/log-query/log-query-overview.md) dans Azure Monitor. Vous pouvez appliquer ces données à divers scénarios tels que la planification de la migration, l’analyse de la capacité, la détection et la résolution de problèmes de performances à la demande.

## <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par Azure Monitor pour les conteneurs et les types de données qui s’affichent dans les résultats de recherche dans les journaux :

| Type de données | Type de données dans Recherche de journaux | Champs |
| --- | --- | --- |
| Performances des hôtes et des conteneurs | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventaire de conteneur | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Journal de conteneur | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventaire du nœud de conteneur | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaire des pods dans un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventaire de la partie des nœuds d’un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Événements Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Services dans le cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métriques de performances de la partie des nœuds du cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques de performances de la partie des conteneurs du cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques personnalisées |`InsightsMetrics` | Computer, Name, Namespace, Origin, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> La propriété *Tags* représente [plusieurs dimensions](../platform/data-platform-metrics.md#multi-dimensional-metrics) pour la métrique correspondante. Pour plus d’informations sur les métriques collectées et stockées dans la table `InsightsMetrics`, ainsi qu’une description des propriétés des enregistrements, consultez [InsightsMetrics Overview](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>La prise en charge de Prometheus est une fonctionnalité de la préversion publique actuellement.
>

## <a name="search-logs-to-analyze-data"></a>Rechercher des journaux d’activité pour analyser les données

Les journaux Azure Monitor peuvent vous aider à rechercher des tendances, à diagnostiquer les goulots d’étranglement, à effectuer des prévisions ou à mettre en corrélation des données afin de déterminer si la configuration de cluster actuelle garantit des performances optimales. Les recherches prédéfinies dans les journaux peuvent être utilisées immédiatement. Elles peuvent également être personnalisées afin de retourner les informations comme vous le souhaitez.

Vous pouvez effectuer une analyse interactive des données dans l’espace de travail en sélectionnant l’option **View Kubernetes event logs** (Afficher les journaux des événements Kubernetes) ou **View container logs** (Afficher les journaux d’activité de conteneur) dans le volet de visualisation. La page **Recherche dans les journaux** s’affiche à droite de la page du portail Azure.

![Analyser des données dans Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Les sorties des journaux de conteneur transmises à votre espace de travail sont STDOUT et STDERR. Étant donné qu’Azure Monitor surveille des Kubernetes managés Azure (AKS), le système Kube n’est pas collecté aujourd’hui en raison de l’important volume de données générées. 

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal

Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

| Requête | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste de toutes les informations sur le cycle de vie d’un conteneur| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Événements Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventaire des images | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Processeur du conteneur | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Mémoire du conteneur |

L’exemple suivant est une requête de métriques Prometheus. Les métriques collectées sont des décomptes et afin de déterminer le nombre d’erreurs qui se sont produites au cours d’une période spécifique, nous devons effectuer une soustraction à partir de ce décompte. Le jeu de données est partitionné par *partitionKey*, ce qui signifie que pour chaque ensemble unique *Name*, *Hostname* et *OperationType*, nous exécutons une sous-requête sur cet ensemble qui classe les journaux sur *TimeGenerated*, un processus qui permet de trouver le *TimeGenerated* précédent et le décompte enregistré pour cette heure, afin de déterminer un taux.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

La sortie indiquera des résultats similaires à ce qui suit :

![Consignez les résultats de requête du volume d’ingestion de données](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Étapes suivantes

Azure Monitor pour conteneurs n’inclut pas d’un ensemble prédéfini d’alertes. Pour savoir comment créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, consultez [Créer des alertes de performances avec Azure Monitor pour conteneurs](container-insights-alerts.md). 
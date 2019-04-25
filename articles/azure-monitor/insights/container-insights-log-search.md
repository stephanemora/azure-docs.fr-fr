---
title: Comment les journaux des requêtes à partir d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Azure Monitor pour les conteneurs collecte les données de journaux et de métriques et cet article décrit les enregistrements et inclut des exemples de requêtes.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494766"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Comment interroger les journaux à partir d’Azure Monitor pour les conteneurs
Azure Monitor pour les conteneurs collecte les mesures de performances, les données d’inventaire et les informations d’état d’intégrité à partir de conteneurs et hôtes de conteneur et les transfère à l’espace de travail Analytique de journal dans Azure Monitor. Les données sont collectées toutes les trois minutes. Ces données sont disponibles pour [requête](../../azure-monitor/log-query/log-query-overview.md) dans Azure Monitor. Vous pouvez appliquer ces données à divers scénarios tels que la planification de la migration, l’analyse de la capacité, la détection et la résolution de problèmes de performances à la demande.

## <a name="container-records"></a>Enregistrements de conteneur

Le tableau suivant présente des exemples d’enregistrements collectés par Azure Monitor pour les conteneurs et les types de données qui s’affichent dans les résultats de recherche dans les journaux :

| Type de données | Type de données dans Recherche de journaux | Champs |
| --- | --- | --- |
| Performances des hôtes et des conteneurs | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventaire de conteneur | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Journal de conteneur | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventaire du nœud de conteneur | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaire des pods dans un cluster Kubernetes | `KubePodInventory` | TimeGenerated, ordinateur, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, Nom_contrôleur, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, nom, PodLabel, Namespace, PodStatus, ClusterName, IP du bloc, SourceSystem |
| Inventaire de la partie des nœuds d’un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Événements Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Services dans le cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métriques de performances de la partie des nœuds du cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Ordinateur, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métriques de performances de la partie des conteneurs du cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath et SourceSystem | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Rechercher des journaux d’activité pour analyser les données
Journaux d’analyse Azure peut vous aider à rechercher des tendances, diagnostiquer les goulots d’étranglement, prévisions ou de mettre en corrélation des données qui peuvent vous aider à déterminent si la configuration actuelle du cluster assure des performances optimales. Les recherches prédéfinies dans les journaux peuvent être utilisées immédiatement. Elles peuvent également être personnalisées afin de retourner les informations comme vous le souhaitez. 

Vous pouvez effectuer une analyse interactive des données dans l’espace de travail en sélectionnant l’option **View Kubernetes event logs** (Afficher les journaux des événements Kubernetes) ou **View container logs** (Afficher les journaux d’activité de conteneur) dans le volet de visualisation. La page **Recherche dans les journaux** s’affiche à droite de la page du portail Azure.

![Analyser des données dans Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

La sortie de journaux de conteneur qui est transférée vers votre espace de travail sont STDOUT et STDERR. Étant donné qu’Azure Monitor surveille des Kubernetes managés Azure (AKS), le système Kube n’est pas collecté aujourd’hui en raison de l’important volume de données générées. 

### <a name="example-log-search-queries"></a>Exemples de requêtes de recherche de journal
Il est souvent utile de créer des requêtes en commençant par un exemple ou deux, puis en les modifiant afin de les adapter à vos besoins. Pour créer des requêtes plus avancées, vous pouvez utiliser les exemples de requêtes suivants :

| Requête | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Liste de toutes les informations sur le cycle de vie d’un conteneur| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Événements Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventaire des images | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Processeur du conteneur | 
| Sélectionnez l’option d’affichage de graphique **Courbes** :<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Mémoire du conteneur |

## <a name="next-steps"></a>Étapes suivantes
Azure Monitor pour les conteneurs n’inclut pas d’un ensemble prédéfini d’alertes. Examinez le [créer des alertes de performances avec Azure Monitor pour les conteneurs](container-insights-alerts.md) pour apprendre à créer des alertes recommandées pour une utilisation élevée du processeur et de la mémoire prendre en charge votre DevOps ou les processus opérationnels et les procédures. 
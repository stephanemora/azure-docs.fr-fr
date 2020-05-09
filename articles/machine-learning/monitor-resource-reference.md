---
title: Informations de référence sur les données de monitoring | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Découvrez les données et les ressources collectées pour Azure Machine Learning et disponibles dans Azure Monitor. Azure Monitor collecte et met au jour les données relatives à votre espace de travail Azure Machine Learning et vous permet d’afficher les métriques, de définir des alertes et d’analyser les données consignées.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: 1abd52c98cb1fa6ebe1014fc7a65e756d038d683
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187594"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Informations de référence sur les données de monitoring Azure Machine Learning

Découvrez les données et les ressources collectées par Azure Monitor à partir de votre espace de travail Azure Machine Learning. Pour plus d’informations sur la collecte et l’analyse des données de monitoring, voir [Monitoring d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="resource-logs"></a>Journaux d’activité de ressources

Le tableau suivant liste les propriétés des journaux de ressources Azure Machine Learning lorsqu’ils sont collectés dans les journaux Azure Monitor ou dans le Stockage Azure.

### <a name="amlcomputejobevents-table"></a>Tableau AmlComputeJobEvents

| Propriété | Description |
|:--- |:---|
| TimeGenerated | Heure de génération de l’entrée du journal |
| NomOpération | Nom de l’opération associée à l’événement de journal |
| Category | Nom de l’événement de journal, AmlComputeClusterNodeEvent |
| JobId | ID du travail soumis |
| ExperimentId | ID de l’expérience |
| ExperimentName | Nom de l’expérience |
| CustomerSubscriptionId | SubscriptionId où l’expérience et le travail sont envoyés |
| WorkspaceName | Nom de l’espace de travail Machine Learning |
| ClusterName | Nom du cluster |
| ProvisioningState | État de la soumission du travail |
| ResourceGroupName | Nom du groupe de ressources |
| JobName | Nom du travail |
| ClusterId | ID du cluster |
| Type d’événement | Type de l’événement de travail (exemples : JobSubmitted, JobRunning, JobFailed, JobSucceeded) |
| ExecutionState | État du travail (exécution) (exemples : Queued, Running, Succeeded, Failed) |
| ErrorDetails | Détails de l’erreur du travail |
| CreationApiVersion | Version de l’API utilisée pour créer le travail |
| ClusterResourceGroupName | Nom du groupe de ressources du cluster |
| TFWorkerCount | Nombre de Workers TF |
| TFParameterServerCount | Nombre de serveurs de paramètres TF |
| ToolType | Type d’outil utilisé |
| RunInContainer | Indicateur décrivant si le travail doit être exécuté à l’intérieur d’un conteneur |
| JobErrorMessage | Message détaillé de l’erreur du travail |
| NodeId | ID du nœud créé sur lequel le travail s’exécute |

### <a name="amlcomputeclusterevents-table"></a>Table AmlComputeClusterEvents

| Propriété | Description |
|:--- |:--- |
| TimeGenerated | Heure de génération de l’entrée du journal |
| NomOpération | Nom de l’opération associée à l’événement de journal |
| Category | Nom de l’événement de journal, AmlComputeClusterNodeEvent |
| ProvisioningState | État d’approvisionnement du cluster |
| ClusterName | Nom du cluster |
| ClusterType | Type du cluster |
| CreatedBy | Utilisateur qui a créé le cluster |
| CoreCount | Nombre de cœurs du cluster |
| VmSize | Taille de machine virtuelle du cluster |
| VmPriority | Priorité des nœuds créés à l’intérieur d’un cluster (Dedicated/LowPriority) |
| ScalingType | Type de mise à l’échelle du cluster (manual/auto) |
| InitialNodeCount | Nombre initial de nœuds du cluster |
| MinimumNodeCount | Nombre minimal de nœuds du cluster |
| MaximumNodeCount | Nombre maximal de nœuds du cluster |
| NodeDeallocationOption | Manière dont le nœud doit être libéré |
| Serveur de publication | Éditeur du type de cluster |
| Offre | Offre avec laquelle le cluster est créé |
| Sku | Référence SKU du nœud/de la machine virtuelle créé dans le cluster |
| Version | Version de l’image utilisée lors de la création du nœud/de la machine virtuelle |
| SubnetId | SubnetId du cluster |
| AllocationState | État d’allocation du cluster |
| CurrentNodeCount | Nombre actuel de nœuds du cluster |
| TargetNodeCount | Nombre cible de nœuds du cluster lors de la mise à l’échelle |
| Type d’événement | Type d’événement lors de la création du cluster |
| NodeIdleTimeSecondsBeforeScaleDown | Durée d’inactivité en secondes avant de descendre en puissance le cluster |
| PreemptedNodeCount | Nombre de nœuds réquisitionnés du cluster |
| IsResizeGrow | Indicateur signifiant que le cluster est en cours de montée en puissance |
| VmFamilyName | Nom de la famille de machines virtuelles des nœuds qui peuvent être créés dans le cluster |
| LeavingNodeCount | Nombre de nœuds sortants du cluster |
| UnusableNodeCount | Nombre de nœuds inutilisables du cluster |
| IdleNodeCount | Nombre de nœuds inactifs du cluster |
| RunningNodeCount | Nombre de nœuds en cours d’exécution du cluster |
| PreparingNodeCount | Nombre de nœuds en préparation du cluster |
| QuotaAllocated | Quota alloué au cluster |
| QuotaUtilized | Quota utilisé du cluster |
| AllocationStateTransitionTime | Durée de transition d’un état à un autre |
| ClusterErrorCodes | Code d’erreur reçu lors de la création ou de la mise à l’échelle du cluster |
| CreationApiVersion | Version de l’API utilisée lors de la création du cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tableau AmlComputeClusterNodeEvents

| Propriété | Description |
|:--- |:--- |
| TimeGenerated | Heure de génération de l’entrée du journal |
| NomOpération | Nom de l’opération associée à l’événement de journal |
| Category | Nom de l’événement de journal, AmlComputeClusterNodeEvent |
| ClusterName | Nom du cluster |
| NodeId | ID du nœud de cluster créé |
| VmSize | Taille de machine virtuelle du nœud |
| VmFamilyName | Famille de machines virtuelles à laquelle appartient le nœud |
| VmPriority | Priorité du nœud créé (Dedicated/LowPriority) |
| Serveur de publication | Éditeur de l’image de machine virtuelle (exemple : microsoft-dsvm) |
| Offre | Offre associée à la création de la machine virtuelle |
| Sku | Référence SKU du nœud/de la machine virtuelle créé |
| Version | Version de l’image utilisée lors de la création du nœud/de la machine virtuelle |
| ClusterCreationTime | Heure de création du cluster |
| ResizeStartTime | Heure de début de la mise à l’échelle du cluster |
| ResizeEndTime | Heure de fin de la mise à l’échelle du cluster |
| NodeAllocationTime | Heure à laquelle le nœud a été alloué |
| NodeBootTime | Heure à laquelle le nœud a été démarré |
| StartTaskStartTime | Heure à laquelle la tâche a été affectée à un nœud et a démarré |
| StartTaskEndTime | Heure à laquelle la tâche affectée à un nœud s’est terminée |
| TotalE2ETimeInSeconds | Durée totale d’activité du nœud |

### <a name="metrics"></a>Mesures

Les tableaux suivants listent les métriques de plateforme collectées pour Azure Machine Learning. Toutes les métriques sont stockées dans l’espace de noms **Espace de travail Azure Machine Learning**.

**Modèle**

| Métrique | Unité | Description |
| ----- | ----- | ----- |
| Déploiements de modèles ayant échoué | Count | Nombre de déploiements de modèles ayant échoué. |
| Déploiements de modèles commencés | Count | Nombre de déploiements de modèles ayant commencé. |
| Déploiements de modèles réussis | Count | Nombre de déploiements de modèles ayant réussi. |
| Enregistrements de modèles ayant échoué | Count | Nombre d’enregistrements de modèles ayant échoué. |
| Enregistrements de modèles réussis | Count | Nombre d’enregistrements de modèles ayant réussi. |

**Quota**

Les informations de quota concernent uniquement le calcul Azure Machine Learning.

| Métrique | Unité | Description |
| ----- | ----- | ----- |
| Cœurs actifs | Count | Nombre de cœurs de calcul actifs. |
| Nœuds actifs | Count | Nombre de nœuds actifs. |
| Cœurs inactifs | Count | Nombre de cœurs de calcul inactifs. |
| Nœuds inactifs | Count | Nombre de nœuds de calcul inactifs. |
| Cœurs sortants | Count | Nombre de cœurs sortants. |
| Nœuds sortants | Count | Nombre de nœuds sortants. |
| Cœurs réquisitionnés | Count | Nombre de cœurs réquisitionnés. |
| Nœuds reportés | Count | Nombre de nœuds réquisitionnés. |
| Pourcentage d’utilisation du quota | Pourcentage | Pourcentage du quota utilisé. |
| Nombre total de cœurs | Count | Nombre total de cœurs. |
| Nombre total de nœuds | Count | Nombre total de nœuds. |
| Cœurs inutilisables | Count | Nombre de cœurs inutilisables. |
| Nœuds inutilisables | Count | Nombre de nœuds inutilisables. |

Voici les dimensions qui peuvent être utilisées pour filtrer les métriques de quota :

| Dimension | Métrique(s) disponible(s) avec | Description |
| ---- | ---- | ---- |
| Nom du cluster | Toutes les métriques de quota | Nom de l’instance de calcul. |
| Nom de la famille de machines virtuelles | Pourcentage d’utilisation du quota | Nom de la famille de machines virtuelles utilisée par le cluster. |
| Priorité de la machine virtuelle | Pourcentage d’utilisation du quota | Priorité de la machine virtuelle.

**Ressource**

| Métrique | Unité | Description |
| ----- | ----- | ----- |
| CpuUtilization | Pourcentage | Pourcentage d’utilisation du processeur pour un nœud donné au cours d’une exécution/tâche. Cette métrique est publiée uniquement lorsqu’une tâche est en cours d’exécution sur un nœud. Une tâche peut utiliser un ou plusieurs nœuds. Cette métrique est publiée par nœud. |
| GpuUtilization | Pourcentage | Pourcentage d’utilisation du processeur graphique (GPU) pour un nœud donné au cours d’une exécution/tâche. Un nœud peut avoir un ou plusieurs processeurs graphiques. Cette métrique est publiée par GPU, par nœud. |

Voici les dimensions qui peuvent être utilisées pour filtrer les métriques de ressources :

| Dimension | Description |
| ----- | ----- |
| CreatedTime | |
| deviceId | ID du dispositif (GPU). Disponible uniquement pour GpuUtilization. |
| NodeId | ID du nœud créé où la tâche s’exécute. |
| RunId | ID de l’exécution/tâche. |

**Exécuter**

Informations sur les exécutions d’apprentissage.

| Métrique | Unité | Description |
| ----- | ----- | ----- |
| Exécutions terminées | Count | Nombre d’exécutions effectuées. |
| Exécutions ayant échoué | Count | Nombre d’exécutions ayant échoué. |
| Exécutions commencées | Count | Nombre d’exécutions commencées. |

Voici les dimensions qui peuvent être utilisées pour filtrer les métriques d’exécution :

| Dimension | Description |
| ---- | ---- |
| ComputeType | Type de calcul utilisé par l’exécution. |
| PipelineStepType | Type de [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) utilisé dans l’exécution. |
| PublishedPipelineId | ID du pipeline publié utilisé dans l’exécution. |
| RunType | Type d’exécution. |

Voici les valeurs valides de la dimension RunType :

| Valeur | Description |
| ----- | ----- |
| Expérience | Exécutions hors pipeline. |
| PipelineRun | Exécution de pipeline, parente d’une StepRun. |
| StepRun | Exécution d’une étape de pipeline. |
| ReusedStepRun | Exécution d’une étape de pipeline qui réutilise une exécution précédente. |

## <a name="see-also"></a> Voir aussi

- Pour une description du monitoring d’Azure Machine Learning , voir [Monitoring d’Azure Machine Learning](monitor-azure-machine-learning.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).

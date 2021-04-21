---
title: Analyser la référence de données Azure Machine Learning | Microsoft Docs
description: Documentation de référence pour la supervision d’Azure Machine Learning. Découvrez les données et les ressources collectées et disponibles dans Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031067"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Informations de référence sur l’analyse des données Azure Machine Learning

Découvrez les données et les ressources collectées par Azure Monitor à partir de votre espace de travail Azure Machine Learning. Pour plus d’informations sur la collecte et l’analyse des données de monitoring, voir [Monitoring d’Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="metrics"></a>Mesures

Cette section répertorie toutes les métriques de plateforme collectées automatiquement pour Azure Machine Learning. Le fournisseur de ressources pour ces métriques est [Microsoft.MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modèle**

| Métrique | Unité | Description |
|--|--|--|
| Enregistrements de modèles réussis | Count | Nombre d’enregistrements de modèles ayant réussi dans cet espace de travail |
| Enregistrements de modèles ayant échoué | Count | Nombre d’enregistrements de modèles ayant échoué dans cet espace de travail |
| Déploiements de modèles commencés | Count | Nombre de déploiements de modèles ayant commencé dans cet espace de travail |
| Déploiements de modèles réussis | Count | Nombre de déploiements de modèles ayant réussi dans cet espace de travail |
| Échec du déploiement de modèle | Count | Nombre de déploiements de modèles ayant échoué dans cet espace de travail |

**Quota**

Les informations de quota concernent uniquement le calcul Azure Machine Learning.

| Métrique | Unité | Description |
|--|--|--|
| Nombre total de nœuds | Count | Nombre total de nœuds. Ce total comprend certains des nœuds actifs, des nœuds inactifs, des nœuds inutilisables, des nœuds reportés et des nœuds sortants |
| Nœuds actifs | Count | Nombre de nœuds actifs. Les nœuds qui exécutent activement un travail. |
| Nœuds inactifs | Count | Nombre de nœuds inactifs. Les nœuds inactifs sont les nœuds qui n’exécutent aucun travail, mais qui peuvent accepter un nouveau travail, le cas échéant. |
| Nœuds inutilisables | Count | Nombre de nœuds inutilisables. Les nœuds inutilisables ne sont pas fonctionnels en raison d’un problème insoluble. Azure recycle ces nœuds. |
| Nœuds reportés | Count | Nombre de nœuds reportés. Ces nœuds sont les nœuds de basse priorité qui sont éloignés du pool de nœuds disponibles. |
| Nœuds sortants | Count | Nombre de nœuds sortants. Les nœuds sortants sont les nœuds qui viennent de terminer le traitement d’un travail et qui passent à l’état inactif. |
| Nombre total de cœurs | Count | Nombre total de cœurs |
| Cœurs actifs | Count | Nombre de cœurs actifs |
| Cœurs inactifs | Count | Nombre de cœurs inactifs |
| Cœurs inutilisables | Count | Nombre de cœurs inutilisables |
| Cœurs réquisitionnés | Count | Nombre de cœurs réquisitionnés |
| Cœurs sortants | Count | Nombre de cœurs sortants |
| Pourcentage d’utilisation du quota | Count | Pourcentage de quota utilisé |

**Ressource**

| Métrique| Unité | Description |
|--|--|--|
| CpuUtilization | Count | Pourcentage d’utilisation de la mémoire sur un nœud de processeur. L’utilisation est rapportée à intervalles d’une minute. |
| GpuUtilization | Count | Pourcentage d’utilisation sur un nœud de GPU. L’utilisation est rapportée à intervalles d’une minute. |
| GpuMemoryUtilization | Count | Pourcentage d’utilisation de la mémoire sur un nœud de GPU. L’utilisation est rapportée à intervalles d’une minute. |
| GpuEnergyJoules | Nombre | Énergie par intervalle en joules sur un nœud GPU. L’énergie est rapportée à intervalles d’une minute. |

**Exécuter**

Informations sur les exécutions de formation pour l’espace de travail.

| Métrique | Unité | Description |
|--|--|--|
| Exécutions annulées | Count | Nombre d’exécutions annulées pour cet espace de travail. Le nombre est mis à jour lorsque l’annulation d’une exécution aboutit. |
| Annuler les exécutions demandées | Count | Nombre d’exécutions pour lesquelles l’annulation a été demandée dans cet espace de travail. Le nombre est mis à jour à la réception d’une demande d’annulation pour une exécution. |
| Exécutions terminées | Count | Nombre d’exécutions réussies pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution est terminée et que la sortie a été collectée. |
| Exécutions échouées | Count | Nombre d’exécutions en échec pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution échoue. |
| Exécutions en cours de finalisation | Count | Nombre d’exécutions entrées en état de finalisation pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution est terminée, mais que la collecte de sortie est toujours en cours. | 
| Exécutions sans réponse | Count | Nombre d’exécutions sans réponse pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution passe à l’état Sans réponse. |
| Exécutions non démarrées | Count | Nombre d’exécutions à l’état Non démarré pour cet espace de travail. Le nombre est mis à jour lorsqu’une demande de création d’une exécution est reçue, mais que les informations d’exécution n’ont pas encore été remplies. |
| Exécutions en cours de préparation | Count | Nombre d’exécutions en cours de préparation pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution passe à l’état En préparation pendant la préparation de l’environnement d’exécution. |
| Exécutions en cours de provisionnement | Count | Nombre d’exécutions en cours de provisionnement pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution attend la création ou le provisionnement d’une cible de calcul. |
| Exécutions en file d’attente | Count | Nombre d’exécutions mises en file d’attente pour cet espace de travail. Le nombre est mis à jour lorsqu’une exécution est mise en file d’attente dans la cible de calcul. Peut se produire en attendant que les nœuds de calcul nécessaires soient prêts. |
| Exécutions démarrées | Count | Nombre d’exécutions en cours pour cet espace de travail. Le nombre est mis à jour lorsque l’exécution commence sur les ressources requises. |
| Exécutions en cours de démarrage | Count | Nombre d’exécutions démarrées pour cet espace de travail. Le nombre est mis à jour après que la demande de création de l’exécution et des informations d’exécution (par exemple, l’ID d’exécution) a été remplie. |
| Erreurs | Count | Nombre d’erreurs d’exécution dans cet espace de travail. Le nombre est mis à jour chaque fois que l’exécution rencontre une erreur. |
| Avertissements | Count | Nombre d’avertissements d’exécution dans cet espace de travail. Le nombre est mis à jour chaque fois qu’une exécution rencontre un avertissement. |

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning a les dimensions suivantes associées à ses métriques.

| Dimension | Description |
| ---- | ---- |
| Nom du cluster | Nom de la ressource de cluster de calcul. Disponible pour toutes les métriques de quota. |
| Nom de la famille de machines virtuelles | Nom de la famille de machines virtuelles utilisée par le cluster. Pourcentage Disponible pour l’utilisation du quota. |
| Priorité de la machine virtuelle | Priorité de la machine virtuelle. Pourcentage Disponible pour l’utilisation du quota.
| CreatedTime | Disponible uniquement pour CpuUtilization et GpuUtilization. |
| deviceId | ID du dispositif (GPU). Disponible uniquement pour GpuUtilization. |
| NodeId | ID du nœud créé où la tâche s’exécute. Disponible uniquement pour CpuUtilization et GpuUtilization. |
| RunId | ID de l’exécution/tâche. Disponible uniquement pour CpuUtilization et GpuUtilization. |
| ComputeType | Type de calcul utilisé par l’exécution. Disponible uniquement pour les exécutions terminées, les échecs d’exécutions et les exécutions démarrées. |
| PipelineStepType | Type de [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) utilisé dans l’exécution. Disponible uniquement pour les exécutions terminées, les échecs d’exécutions et les exécutions démarrées. |
| PublishedPipelineId | ID du pipeline publié utilisé dans l’exécution. Disponible uniquement pour les exécutions terminées, les échecs d’exécutions et les exécutions démarrées. |
| RunType | Type d’exécution. Disponible uniquement pour les exécutions terminées, les échecs d’exécutions et les exécutions démarrées. |

Voici les valeurs valides de la dimension RunType :

| Valeur | Description |
| ----- | ----- |
| Expérience | Exécutions hors pipeline. |
| PipelineRun | Exécution de pipeline, parente d’une StepRun. |
| StepRun | Exécution d’une étape de pipeline. |
| ReusedStepRun | Exécution d’une étape de pipeline qui réutilise une exécution précédente. |

## <a name="activity-log"></a>Journal d’activité

Le tableau suivant répertorie les opérations relatives à Azure Machine Learning qui peuvent être créées dans le journal d’activité.

| Opération | Description |
|:---|:---|
| Crée ou met à jour un espace de travail Machine Learning | Un espace de travail a été créé ou mis à jour |
| CheckComputeNameAvailability | Vérifie si un nom de calcul est déjà utilisé |
| Crée ou met à jour les ressources de calcul | Une ressource de calcul a été créée ou mise à jour |
| Supprime les ressources de calcul | Une ressource de calcul a été supprimée |
| Afficher la liste des secrets | Sur l’opération, répertorie des secrets pour un espace de travail Machine Learning |

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour l’espace de travail Azure Machine Learning.

Fournisseur et type de ressources : [Microsoft.MachineLearningServices/workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Category | Nom d’affichage |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schémas

Les schémas suivants sont utilisés par Azure Machine Learning.

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
| Type d’événement | Type de l’événement de travail. Par exemple, JobSubmitted, JobRunning, JobFailed, JobSucceeded |
| ExecutionState | État du travail (exécution). Par exemple, En file d’attente, En cours d’exécution, Réussi, Échec |
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
| Serveur de publication | Éditeur de l’image de machine virtuelle. Par exemple, microsoft-dsvm |
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


## <a name="see-also"></a>Voir aussi

- Pour une description du monitoring d’Azure Machine Learning , voir [Monitoring d’Azure Machine Learning](monitor-azure-machine-learning.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

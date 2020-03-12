---
title: Supervision d’Azure Machine Learning | Microsoft Docs
description: Découvrez comment utiliser Azure Monitor pour voir, analyser et créer des alertes sur des métriques à partir d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399103"
---
# <a name="monitoring-azure-machine-learning"></a>Supervision d’Azure Machine Learning

Cet article décrit les données de supervision générées par Azure Machine Learning. Il indique également comment vous pouvez utiliser Azure Monitor pour analyser vos données et définir des alertes.

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux administrateurs, car elles décrivent la supervision d’Azure Machine Learning. Si vous êtes scientifique des données ou développeur, et que vous voulez superviser des informations spécifiques aux exécutions de votre modèle d’entraînement, consultez les documents suivants :
>
> * [Démarrer, superviser et annuler des exécutions d’entraînement](how-to-manage-runs.md)
> * [Journaliser des métriques pour les exécutions d’entraînement](how-to-track-experiments.md)
> * [Suivre des expériences avec MLflow](how-to-use-mlflow.md)
> * [Visualiser les exécutions avec TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning journalise les données de supervision à l’aide d’Azure Monitor, qui est un service de supervision de pile complète dans Azure. Azure Monitor fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure. Il peut également superviser des ressources dans d’autres clouds et localement.

Commencez par lire l’article [Vue d’ensemble d’Azure Monitor](/azure/azure-monitor/overview), qui fournit une vue d’ensemble des fonctionnalités de supervision. Les sections suivantes s’appuient sur ces informations en fournissant des détails sur l’utilisation d’Azure Monitor avec Azure Machine Learning.

Pour comprendre les coûts associés à Azure Monitor, consultez [Utilisation et estimation des coûts](/azure/azure-monitor/platform/usage-estimated-costs). Pour comprendre le temps nécessaire à l’affichage de vos données dans Azure Monitor, consultez [Durée d’ingestion des données de journal](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Supervision de données à partir d’Azure Machine Learning

Azure Machine Learning collecte les mêmes types de données de supervision que les autres ressources Azure, qui sont décrites dans [Supervision de des données à partir de ressources Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Pour obtenir une référence détaillée des journaux et des métriques créés par Azure Machine Learning, consultez [Informations de référence sur les données de supervision Azure Machine Learning](monitor-resource-reference.md).

## <a name="analyzing-metric-data"></a>Analyse des données de métrique

Vous pouvez analyser les métriques d’Azure Machine Learning en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

Toutes les métriques d’Azure Machine Learning se trouvent dans l’espace de noms **Espace de travail de service Machine Learning**.

![Metrics Explorer avec l’espace de travail de service Machine Learning sélectionné](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrage et fractionnement

Pour les métriques qui prennent en charge les dimensions, vous pouvez appliquer des filtres à l’aide d’une valeur de dimension. Par exemple, le filtrage **Cœurs actifs** pour le **Nom de cluster**`cpu-cluster`. 

Vous pouvez également fractionner une métrique par dimension pour comparer différents segments de celle-ci entre eux. Par exemple, le fractionnement du **type d’étape de pipeline** pour voir le nombre de types d’étapes utilisés dans le pipeline.

Pour plus d’informations sur le filtrage et le fractionnement, consultez [Fonctionnalités avancées d’Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alertes

Vous pouvez accéder aux alertes pour Azure Machine Learning en ouvrant **Alertes** à partir du menu **Azure Monitor**. Pour plus d’informations sur la création d’alertes, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-metric).

Le tableau suivant liste les règles d’alerte de métrique courantes et recommandées pour Azure Machine Learning :

| Type d’alerte | Condition | Description |
|:---|:---|:---|
| Échec du déploiement de modèle | Type d’agrégation : Totale, Opérateur : Supérieur à, Valeur de seuil : 0 | Quand un ou plusieurs déploiements de modèle ont échoué |
| Pourcentage d’utilisation du quota | Type d’agrégation : Moyenne, Opérateur : Supérieur à, Valeur de seuil : 90| Quand le pourcentage d’utilisation du quota est supérieur à 90 % |
| Nœuds inutilisables | Type d’agrégation : Totale, Opérateur : Supérieur à, Valeur de seuil : 0 | Quand un ou plusieurs nœuds sont inutilisables |

## <a name="configuration"></a>Configuration

> [!IMPORTANT]
> __Les métriques d’Azure Machine Learning n’ont pas besoin d’être configurées__ : elles sont collectées automatiquement et sont disponibles dans Metrics Explorer pour la supervision et la génération d’alertes.

Vous pouvez ajouter un paramètre de diagnostic pour configurer les fonctionnalités suivantes :

* Archiver les informations du journal et des métriques dans un compte de stockage Azure.
* Diffuser un flux des informations du journal et des métriques vers un hub d’événements Azure.
* Envoyer les informations du journal et des métriques à Azure Monitor Log Analytics.

L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

Pour plus d’informations sur la création d’un paramètre de diagnostic, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](/azure/azure-monitor/platform/diagnostic-settings).

Vous pouvez configurer les journaux suivants pour Azure Machine Learning :

| Category | Description |
|:---|:---|
| AmlComputeClusterEvent | Événements provenant des clusters de calcul Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Événements provenant des nœuds d’un cluster de calcul Azure Machine Learning. |
| AmlComputeJobEvent | Événements provenant des travaux en cours d’exécution sur la capacité de calcul Azure Machine Learning. |

> [!NOTE]
> Quand vous activez les métriques dans un paramètre de diagnostic, les informations de dimension ne sont actuellement pas incluses dans les informations envoyées à un compte de stockage, un hub d’événements ou Log Analytics.

## <a name="analyzing-log-data"></a>Analyse des données de journal

L’utilisation d’Azure Monitor Log Analytics vous oblige à créer une configuration de diagnostic et à activer __Envoyer des informations à Log Analytics__. Pour plus d’informations, consultez la section [Configuration](#configuration).

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Azure Machine Learning stocke les données dans les tables suivantes :

| Table de charge de travail | Description |
|:---|:---|
| AmlComputeClusterEvent | Événements provenant des clusters de calcul Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Événements provenant des nœuds d’un cluster de calcul Azure Machine Learning. |
| AmlComputeJobEvent | Événements provenant des travaux en cours d’exécution sur la capacité de calcul Azure Machine Learning. |

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Machine Learning, Log Analytics est ouvert avec l’étendue de requête définie sur l’espace de travail actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données d’autres bases de données ou des données provenant d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

Pour obtenir une référence détaillée des journaux et des métriques, consultez [Informations de référence sur les données de supervision Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-queries"></a>Exemples de requêtes

Voici des requêtes que vous pouvez utiliser pour vous aider à superviser vos ressources Azure Machine Learning : 

+ Obtenir les travaux en échec au cours des cinq derniers jours :

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obtenir les enregistrements pour un nom de travail spécifique :

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Obtenir les événements de cluster survenus au cours des cinq derniers jours pour les clusters pour lesquels la taille de machine virtuelle est Standard_D1_V2 :

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Obtenir les nœuds alloués au cours des huit derniers jours :

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques, consultez [Informations de référence sur les données de supervision Azure Machine Learning](monitor-resource-reference.md).
- Pour plus d’informations sur l’utilisation des quotas liés à Azure Machine Learning, consultez [Gérer et demander des quotas pour les ressources Azure](how-to-manage-quotas.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).

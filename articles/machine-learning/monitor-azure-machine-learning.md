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
ms.date: 10/01/2020
ms.openlocfilehash: 3470f969034a051b17e762b685a89c0f910e0cbb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747117"
---
# <a name="monitor-azure-machine-learning"></a>Superviser Azure Machine Learning

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. Cet article décrit les données de supervision générées par Azure Machine Learning, ainsi que l’analyse et les alertes sur ces données à l’aide d’Azure Monitor.

> [!TIP]
> Les informations contenues dans ce document sont principalement destinées aux administrateurs, car elles décrivent la supervision d’Azure Machine Learning au niveau de l’ *espace de travail*. Si vous êtes scientifique des données ou développeur, et que vous voulez superviser des informations spécifiques aux *exécutions de votre modèle d’entraînement* , consultez les documents suivants :
>
> * [Démarrer, superviser et annuler des exécutions d’entraînement](how-to-manage-runs.md)
> * [Journaliser des métriques pour les exécutions d’entraînement](how-to-track-experiments.md)
> * [Suivre des expériences avec MLflow](how-to-use-mlflow.md)
> * [Visualiser les exécutions avec TensorBoard](how-to-monitor-tensorboard.md)

## <a name="what-is-azure-monitor"></a>Qu’est-ce qu’Azure Monitor ?

Azure Machine Learning crée des données de supervision à l’aide d’[Azure Monitor](/azure/azure-monitor/overview), qui est un service de supervision de pile complète dans Azure. Azure Monitor fournit un ensemble complet de fonctionnalités pour superviser vos ressources Azure. Il peut également superviser des ressources dans d’autres clouds et localement.

Commencez avec l’article [Supervision de ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource), qui décrit les concepts suivants :

- Qu’est-ce qu’Azure Monitor ?
- Coûts associés à la supervision
- Analyse des données collectées dans Azure
- Configuration de la collecte des données
- Outils standard dans Azure pour l’analyse et la génération d’alertes sur les données analysées

Les sections suivantes s’appuient sur cet article en décrivant les données spécifiques collectées pour Azure Machine Learning. Ces sections fournissent également des exemples de configuration de la collecte de données et d’analyse de ces données à l’aide des outils Azure.

> [!TIP]
> Pour comprendre les coûts associés à Azure Monitor, consultez [Utilisation et estimation des coûts](/azure/azure-monitor/platform/usage-estimated-costs). Pour comprendre le temps nécessaire à l’affichage de vos données dans Azure Monitor, consultez [Durée d’ingestion des données de journal](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Supervision de données à partir d’Azure Machine Learning

Azure Machine Learning collecte les mêmes types de données de supervision que les autres ressources Azure, qui sont décrites dans [Supervision des données à partir de ressources Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources). 

Pour obtenir une référence détaillée des journaux et des métriques créés par Azure Machine Learning, consultez [Informations de référence sur les données de supervision Azure Machine Learning](monitor-resource-reference.md).

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](/azure/azure-monitor/platform/diagnostic-settings). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour Azure Machine Learning sont répertoriées dans [Informations de référence sur les données de monitoring Azure Machine Learning](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

Vous pouvez configurer les journaux suivants pour Azure Machine Learning :

| Category | Description |
|:---|:---|
| AmlComputeClusterEvent | Événements provenant des clusters de calcul Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Événements provenant des nœuds d’un cluster de calcul Azure Machine Learning. |
| AmlComputeJobEvent | Événements provenant des travaux en cours d’exécution sur la capacité de calcul Azure Machine Learning. |

> [!NOTE]
> Quand vous activez les métriques dans un paramètre de diagnostic, les informations de dimension ne sont actuellement pas incluses dans les informations envoyées à un compte de stockage, un hub d’événements ou Log Analytics.

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques d’Azure Machine Learning ainsi que celles d’autres services Azure en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

Pour obtenir la liste des métriques de plateforme collectées, consultez [Informations de référence sur l’analyse des données Azure Machine Learning – Métriques](monitor-resource-reference.md#metrics).

Toutes les métriques d’Azure Machine Learning se trouvent dans l’espace de noms **Espace de travail de service Machine Learning**.

![Metrics Explorer avec l’espace de travail de service Machine Learning sélectionné](./media/monitor-azure-machine-learning/metrics.png)

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](/azure/azure-monitor/platform/metrics-supported).

### <a name="filtering-and-splitting"></a>Filtrage et fractionnement

Pour les métriques qui prennent en charge les dimensions, vous pouvez appliquer des filtres à l’aide d’une valeur de dimension. Par exemple, le filtrage **Cœurs actifs** pour le **Nom de cluster**`cpu-cluster`. 

Vous pouvez également fractionner une métrique par dimension pour comparer différents segments de celle-ci entre eux. Par exemple, le fractionnement du **type d’étape de pipeline** pour voir le nombre de types d’étapes utilisés dans le pipeline.

Pour plus d’informations sur le filtrage et le fractionnement, consultez [Fonctionnalités avancées d’Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analyse des journaux d’activité

L’utilisation d’Azure Monitor Log Analytics vous oblige à créer une configuration de diagnostic et à activer __Envoyer des informations à Log Analytics__. Pour plus d’informations, consultez la section [Collecte et routage](#collection-and-routing).

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques. Azure Machine Learning stocke les données dans les tables suivantes :

| Table de charge de travail | Description |
|:---|:---|
| AmlComputeClusterEvent | Événements provenant des clusters de calcul Azure Machine Learning. |
| AmlComputeClusterNodeEvent | Événements provenant des nœuds d’un cluster de calcul Azure Machine Learning. |
| AmlComputeJobEvent | Événements provenant des travaux en cours d’exécution sur la capacité de calcul Azure Machine Learning. |

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Machine Learning, Log Analytics est ouvert avec l’étendue de requête définie sur l’espace de travail actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données d’autres bases de données ou des données provenant d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

Pour obtenir une référence détaillée des journaux et des métriques, consultez [Informations de référence sur les données de supervision Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!IMPORTANT]
> Quand vous sélectionnez **Journaux** dans le menu Azure Machine Learning, Log Analytics est ouvert avec l’étendue de requête définie sur l’espace de travail Azure Machine Learning actuel. Cela signifie que les requêtes de journal n’incluront que les données de cette ressource. Si vous voulez exécuter une requête qui inclut des données provenant d’autres espaces de travail ou d’autres services Azure, sélectionnez **Journaux** dans le menu **Azure Monitor**. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope/).

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

## <a name="alerts"></a>Alertes

Vous pouvez accéder aux alertes pour Azure Machine Learning en ouvrant **Alertes** à partir du menu **Azure Monitor**. Pour plus d’informations sur la création d’alertes, consultez [Créer, afficher et gérer des alertes de métrique à l’aide d’Azure Monitor](/azure/azure-monitor/platform/alerts-metric).

Le tableau suivant liste les règles d’alerte de métrique courantes et recommandées pour Azure Machine Learning :

| Type d’alerte | Condition | Description |
|:---|:---|:---|
| Échec du déploiement de modèle | Type d’agrégation : Totale, Opérateur : Supérieur à, Valeur de seuil : 0 | Quand un ou plusieurs déploiements de modèle ont échoué |
| Pourcentage d’utilisation du quota | Type d’agrégation : Moyenne, Opérateur : Supérieur à, Valeur de seuil : 90| Quand le pourcentage d’utilisation du quota est supérieur à 90 % |
| Nœuds inutilisables | Type d’agrégation : Totale, Opérateur : Supérieur à, Valeur de seuil : 0 | Quand un ou plusieurs nœuds sont inutilisables |

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une référence des journaux et des métriques, consultez [Informations de référence sur l’analyse des données Azure Machine Learning](monitor-resource-reference.md).
- Pour plus d’informations sur l’utilisation des quotas liés à Azure Machine Learning, consultez [Gérer et demander des quotas pour les ressources Azure](how-to-manage-quotas.md).
- Pour plus d’informations sur la supervision des ressources Azure, consultez [Supervision de ressources Azure avec Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource).

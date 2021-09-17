---
title: Superviser Azure Batch
description: Découvrez les services de supervision Azure, les métriques, les journaux de diagnostic et d’autres fonctionnalités de supervision d’Azure Batch.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: b2b73261b07a7e5eb269c1f58a45f97d99133ac4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768885"
---
# <a name="monitor-batch-solutions"></a>Solutions Monitor Batch

[Azure Monitor](../azure-monitor/overview.md) et le service Batch fournissent une gamme de services, d’outils et d’API permettant de surveiller vos solutions Batch. Cet article de présentation vous aide à choisir une approche de surveillance correspondant à vos besoins.

## <a name="subscription-level-monitoring"></a>Surveillance au niveau de l’abonnement

Au niveau de l’abonnement, qui inclut les comptes Batch, le [journal d’activité Azure](../azure-monitor/essentials/activity-log.md) collecte les données des événements opérationnels dans plusieurs catégories.

Plus précisément, pour les comptes Batch, le journal d’activité collecte les événements liés à la gestion des clés et à la création ou la suppression de comptes.

Vous pouvez afficher le journal d’activité dans le portail Azure ou appeler des événements à l’aide de l’interface CLI Azure, des applets de commande PowerShell ou de l’API REST Azure Monitor. Vous pouvez également exporter le journal d’activité ou configurer les [alertes de journal d’activité](../azure-monitor/alerts/alerts-activity-log.md).

## <a name="batch-account-level-monitoring"></a>Surveillance au niveau du compte Batch

Surveillez chaque compte Batch à l’aide des fonctionnalités d’[Azure Monitor](../azure-monitor/overview.md). Azure Monitor collecte des [métriques](../azure-monitor/essentials/data-platform-metrics.md) et éventuellement des [journaux de ressource](../azure-monitor/essentials/resource-logs.md) pour les ressources au niveau d’un compte Batch, comme les pools, les travaux et les tâches. Collectez et utilisez ces données manuellement ou par programmation pour surveiller les activités dans votre compte Batch et diagnostiquer les problèmes. Pour plus d’informations, consultez [Métriques, alertes et journaux Batch pour l’évaluation de diagnostic et la surveillance](batch-diagnostics.md).

> [!NOTE]
> Les métriques sont disponibles par défaut dans votre compte Batch sans configuration supplémentaire, et elles ont un historique roulant de 30 jours. Vous devez créer un paramètre de diagnostic pour un compte Batch afin d’envoyer ses journaux de ressources à un espace de travail Log Analytics, ce qui peut entraîner des coûts supplémentaires pour le stockage ou le traitement des données des journaux de ressources.

## <a name="batch-resource-monitoring"></a>Surveillance des ressources Batch

Dans vos applications Batch, utilisez les API Batch pour surveiller ou interroger l’état de vos ressources, notamment les travaux, les tâches, les nœuds et les pools. Par exemple :

- [Compter les tâches et les nœuds de calcul par état](batch-get-resource-counts.md)
- [Créer des requêtes pour répertorier les ressources Batch efficacement](batch-efficient-list-queries.md)
- [Créer des dépendances de tâches pour exécuter des tâches qui dépendent d’autres tâches](batch-task-dependencies.md)
- Utiliser une [tâche du gestionnaire de travaux](/rest/api/batchservice/job/add#jobmanagertask)
- Surveiller l’[état des tâches](/rest/api/batchservice/task/list#taskstate)
- Surveiller l’[état des nœuds](/rest/api/batchservice/computenode/list#computenodestate)
- Surveiller l’[état des pools](/rest/api/batchservice/pool/get#poolstate)
- Surveiller l’[utilisation des pools dans le compte](/rest/api/batchservice/pool/listusagemetrics)
- Compter les [nœuds de pool par état](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="additional-monitoring-solutions"></a>Solutions de surveillance supplémentaires

Utiliser les [Insights d’application](../azure-monitor/app/app-insights-overview.md) pour surveiller par programmation la disponibilité, la performance et l’utilisation de vos travaux et tâches Batch. Les Insights d’application vous permettent de surveiller les compteurs de performances à partir de nœuds de calcul (machines virtuelles) et de récupérer des informations personnalisées pour les tâches qui s’exécutent sur ces derniers.

Par exemple, consultez [Monitor and debug a Batch .NET application with Application Insights (Surveiller et déboguer une application .NET Batch avec Application Insights)](monitor-application-insights.md) et l’[exemple de code](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) fourni avec.

> [!NOTE]
> L’utilisation d’Application Insights peut induire des frais supplémentaires. Consultez les [informations de tarification](https://azure.microsoft.com/pricing/details/application-insights/).

[Batch Explorer](https://github.com/Azure/BatchExplorer) est un outil client autonome gratuit, doté de nombreuses fonctionnalités aidant à créer, déboguer et surveiller les applications Azure Batch. Téléchargez un [package d’installation](https://azure.github.io/BatchExplorer/) pour Mac, Linux ou Windows. En option, vous pouvez utiliser les [Insights Azure Batch](https://github.com/Azure/batch-insights) pour obtenir des statistiques système pour vos nœuds Batch, tels que les compteurs de performances de machine virtuelle, dans Batch Explorer.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [outils et API Batch](batch-apis-tools.md) disponibles pour créer des solutions Batch.
- En savoir plus sur les [journaux de diagnostic](batch-diagnostics.md) avec Batch.

---
title: Informations de référence sur les données de surveillance d’Azure Container Registry
description: Matériel de référence important nécessaire lorsque vous surveillez votre registre de conteneurs Azure. Fournit des détails sur les métriques, les journaux de ressources et les schémas de journaux.
author: dlepow
ms.author: danlep
ms.topic: reference
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: 86c8047727711655a262425032b7a9fe3daa6dc1
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059973"
---
# <a name="monitoring-azure-container-registry-data-reference"></a>Informations de référence sur les données de surveillance d’Azure Container Registry

Pour plus d’informations sur la collecte et l’analyse des données de surveillance pour Azure Container Registry, consultez [Superviser Azure Container Registry](monitor-service.md).

## <a name="metrics"></a>Mesures

### <a name="container-registry-metrics"></a>Métriques Container Registry

Type et fournisseur de ressources : [Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries)

| Métrique | Exportable par le biais des paramètres de diagnostic ? | Unité | Type d’agrégation | Description | Dimensions  |  
|:-------|:-----|:-----|:------------|:------------------|:----- |
|     AgentPoolCPUTime   | Oui |   Secondes   | Total |   Temps UC utilisé par les [tâches ACR](container-registry-tasks-overview.md) qui s’exécutent sur des [pools d’agents](tasks-agent-pools.md) dédiés             | None | 
|     RunDuration   | Oui |  Millisecondes   |  Total |  Durée des exécutions de [tâches ACR](container-registry-tasks-overview.md)       | Aucun | 
|     StorageUsed   |  Non | Octets   |   Average | Stockage utilisé par le registre de conteneurs<br/><br/>Somme du stockage des couches uniques et partagées, des fichiers manifeste et des copies de réplica dans tous les référentiels<sup>1</sup>            | Géolocalisation | 
|     SuccessfulPullCount | Oui  |   Count   | Total | Tirages (pull) réussis d’images conteneur et d’autres artefacts du registre              | None | 
|     SuccessfulPushCount   | Oui |   Count   | Total | Envois (push) réussis d’images conteneur et d’autres artefacts du registre          | Aucun | 
|     TotalPullCount   |   Oui | Count   |     Total |  Nombre total de tirages (pull) d’images conteneur et d’autres artefacts du registre         | None | 
|     TotalPushCount   | Oui |  Count   |   Total |  Nombre total d’envois (push) d’images conteneur et d’autres artefacts du registre           | Aucun | 

<sup>1</sup>En raison du partage des couches, le stockage du registre utilisé peut être inférieur à la somme du stockage des référentiels individuels. Lorsque vous [supprimez](container-registry-delete.md) un référentiel ou une balise, vous récupérez uniquement le stockage utilisé par les fichiers manifeste et les couches uniques référencées.

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Container Registry a les dimensions suivantes associées à ses métriques.

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **Géolocalisation** | Région Azure d’un registre ou d’un [géoréplica](container-registry-geo-replication.md). |


## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour Azure Container Registry. 

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

### <a name="container-registries"></a>Registres de conteneurs

Type et fournisseur de ressources : [Microsoft.ContainerRegistry/registries](../azure-monitor/essentials/resource-logs-categories.md#microsoftcontainerregistryregistries)

| Category | Nom d’affichage | Détails  |
|:---------|:-------------|------------------|
| ContainerRegistryLoginEvents  | Événements de connexion | Événements et état relatifs à l’authentification du registre, y compris l’identité et l’adresse IP entrantes |
| ContainerRegistryRepositoryEvents | Événements de référentiel           | Opérations sur les images et autres artefacts dans les référentiels de registre<br/><br/> Les opérations suivantes sont consignées : envoyer (push), tirer (pull), supprimer une balise, supprimer (y compris la suppression du référentiel), vider la balise et vider le manifeste<sup>1</sup> |

<sup>1</sup>Les événements de vidage sont consignés uniquement si une [stratégie de rétention](container-registry-retention-policy.md) du registre est configurée.

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Cette section fait référence à toutes les tables Kusto de Journaux d’activité Azure Monitor qui sont pertinentes pour Azure Container Registry et qui peuvent être interrogées par Log Analytics. 

### <a name="container-registry"></a>Container Registry

| Table de charge de travail |  Description | 
|:---------|:-------------|
| [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)   | Entrées du journal d’activité Azure offrant un aperçu de tous les événements survenus dans Azure au niveau de l’abonnement ou du groupe d’administration. | 
| [AzureMetrics](/azure/azure-monitor/reference/tables/azuremetrics) | Données de métriques émises par les services Azure et qui mesurent leur intégrité et leurs performances.    |  
|  [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/containerregistryloginevents)               | Journaux d’audit de connexion Azure Container Registry                             |                                                     
|  [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/containerregistryrepositoryevents)               | Journaux d’audit de référentiel Azure Container Registry                         |                                                    

Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="activity-log"></a>Journal d’activité

Le tableau suivant répertorie les opérations relatives à Azure Container Registry qui peuvent être créées dans le [journal d’activité](../azure-monitor/essentials/activity-log.md). Cette liste n’est pas exhaustive.

| Opération | Description |
|:---|:---|
| Créer ou mettre à jour un groupe de conteneurs | Créer un registre de conteneurs ou mettre à jour une propriété de registre |
| Supprimer un registre de conteneurs | Supprimer un registre de conteneurs |
| Répertorier les informations d’identification de connexion du registre de conteneurs | Afficher les informations d’identification du compte administrateur du registre |
| Importer une image | Importer une image ou un autre artefact dans un registre |
| Créer une attribution de rôle | Attribuer à une identité un rôle RBAC pour accéder à une ressource  |


## <a name="schemas"></a>Schémas

Les schémas suivants sont utilisés par les journaux de ressources d’Azure Container Registry.

| schéma | Description |
|:--- |:---|
| [ContainerRegistryLoginEvents](/azure/azure-monitor/reference/tables/ContainerRegistryLoginEvents)  | Schéma des événements et de l’état relatifs à l’authentification du registre, y compris l’identité et l’adresse IP entrantes |
| [ContainerRegistryRepositoryEvents](/azure/azure-monitor/reference/tables/ContainerRegistryRepositoryEvents) | Schéma des opérations sur les images et autres artefacts dans les référentiels de registre |
## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une description de l’analyse d’un registre de conteneurs Azure, consultez [Superviser Azure Container Registry](monitor-service.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](/azure/azure-monitor/overview).

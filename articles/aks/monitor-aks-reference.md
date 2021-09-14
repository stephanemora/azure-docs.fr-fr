---
title: Informations de référence sur les données de supervision AKS
description: Matériel de référence important requis pour superviser AKS
ms.service: container-service
ms.custom: subject-monitoring
ms.date: 07/29/2021
ms.topic: reference
ms.openlocfilehash: 4dfbe6ba3e8bb64e2fdf41a06932f84fb1b7be94
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122525970"
---
# <a name="monitoring-aks-data-reference"></a>Informations de référence sur les données de supervision AKS

Consultez [Supervision AKS](monitor-aks.md) pour plus d’informations sur la collecte et l’analyse des données de supervision pour AKS.

## <a name="metrics"></a>Mesures

Le tableau suivant liste les métriques de plateforme collectées pour AKS.  Suivez chaque lien pour obtenir la liste détaillée des métriques pour chaque type particulier.

|Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Clusters managés | [Microsoft.ContainerService/managedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters)
| Clusters connectés | [microsoft.kubernetes/connectedClusters](../azure-monitor/essentials/metrics-supported.md#microsoftkubernetesconnectedclusters)
| Machines virtuelles| [Microsoft.Compute/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
| Groupes identiques de machines virtuelles | [Microsoft.Compute/virtualMachineScaleSets](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
| Machines virtuelles des groupes de machines virtuelles identiques | [Microsoft.Compute/virtualMachineScaleSets/virtualMachines](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)|

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Dimensions de métrique

Le tableau suivant répertorie les [dimensions](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics) pour les métriques AKS. 

<!-- listed here /azure/azure-monitor/essentials/metrics-supported#microsoftcontainerservicemanagedclusters-->

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| requestKind | Utilisée par des métriques telles que les *demandes en cours* à diviser par type de demande. |
| condition | Utilisée par des métriques telles que les *états pour les différentes conditions de nœud*, le *nombre de pods à l’état Prêt* à diviser selon le type de condition. |
| status | Utilisée par des métriques telles que les *états pour les différentes conditions de nœud* à diviser selon l’état de la condition. |
| status2 | Utilisée par des métriques telles que les *états pour les différentes conditions de nœud* à diviser selon l’état de la condition.  |
| nœud | Utilisée par des métriques telles que l’*utilisation de l’UC en millicœurs* à diviser selon le nom du nœud. |
| phase | Utilisée par des métriques telles que le *nombre de pods par phase* à diviser selon la phase du pod. |
| espace de noms | Utilisée par des métriques telles que le *nombre de pods par phase* à diviser selon l’espace de noms du pod. |
| pod | Utilisée par des métriques telles que le *nombre de pods par phase* à diviser selon le nom du pod. |
| nodepool | Utilisée par des métriques telles que le nombre d’*octets utilisés sur le disque* à diviser selon le nom du pool de nœuds. |
| device | Utilisée par des métriques telles que le nombre d’*octets utilisés sur le disque* à diviser selon le nom de l’appareil. |

## <a name="resource-logs"></a>Journaux d’activité de ressources

Le tableau suivant répertorie les catégories de journaux de ressources que vous pouvez collecter pour AKS. Il s’agit des journaux des composants du plan de contrôle AKS. Consultez [Configurer la supervision](monitor-aks.md#configure-monitoring) pour plus d’informations sur la création d’un paramètre de diagnostic pour collecter ces journaux et les recommandations sur lesquelles activer. Consultez [Comment interroger des journaux à partir de Container Insights](../azure-monitor/containers/container-insights-log-query.md#resource-logs) pour obtenir des exemples de requête.

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md). 

| Category                | Description |
|:---|:---|
| cluster-autoscale       | Comprenez la raison pour laquelle le cluster AKS effectue un scale-up ou un scale-down, ce qui n’est peut-être pas prévu. Ces informations sont également utiles pour mettre en corrélation les intervalles de temps où une action intéressante a pu se produire dans le cluster. |
| guard                   | Audits managés d’Azure Active Directory et d’Azure RBAC. Pour Azure AD managé, cela inclut un jeton en entrée et des informations utilisateur en sortie. Pour Azure RBAC, cela inclut des révisions d’accès en entrée et en sortie. |
| kube-apiserver          | Journaux provenant du serveur d’API. |
| kube-audit              | Données du journal d’audit pour chaque événement d’audit, y compris les événements get, list, create, update, delete, patch et post. |
| kube-audit-admin        | Sous-ensemble de la catégorie de journal kube-audit. Réduit considérablement le nombre de journaux en excluant les événements d’audit get et list du journal. |
| kube-controller-manager | Obtenez une visibilité plus poussée des problèmes qui peuvent survenir entre Kubernetes et le plan de contrôle Azure. Un exemple typique est le cluster AKS qui n’a pas assez d’autorisations pour interagir avec Azure. |
| kube-scheduler          | Journaux provenant du planificateur. |
| AllMetrics              | Inclut toutes les métriques de plateforme. Envoie ces valeurs à l’espace de travail Log Analytics où elles peuvent être évaluées avec d’autres données à l’aide de requêtes de journal. |

## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs

Cette section fait référence à toutes les tables Azure Monitor Logs pertinentes pour AKS et disponibles pour une requête par Log Analytics. 



|Type de ressource | Notes |
|-------|-----|
| [Services Kubernetes](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) | Suivez ce lien pour obtenir la liste de toutes les tables utilisées par AKS et une description de leur structure. |


Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure/azure-monitor/reference/tables/tables-resourcetype).


## <a name="activity-log"></a>Journal d’activité

Le tableau suivant liste quelques exemples d’opérations relatives à AKS qui peuvent être créées dans le [journal d’activité](../azure-monitor/essentials/activity-log.md). Utilisez le journal d’activité pour suivre des informations telles que le moment où un cluster a été créé ou a vu sa configuration être modifiée. Vous pouvez consulter ces informations dans le portail ou créer une alerte de journal d’activité pour être averti de manière proactive lorsqu’un événement se produit.

| Opération | Description |
|:---|:---|
| Microsoft.ContainerService/managedClusters/write | Créer ou mettre à jour le cluster managé |
| Microsoft.ContainerService/managedClusters/delete | Supprimer le cluster managé |
| Microsoft.ContainerService/managedClusters/listClusterMonitoringUserCredential/action | Lister les informations d’identification clusterMonitoringUser |
| Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lister les informations d’identification clusterAdmin |
| Microsoft.ContainerService/managedClusters/agentpools/write | Créer ou mettre à jour un pool d’agents |

Pour obtenir la liste complète des entrées de journal possibles, consultez [Options du fournisseur de ressources Microsoft.ContainerService](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).

Pour plus d’informations sur le schéma des entrées du journal d’activité, consultez [Schéma du journal d’activité](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Voir aussi

- Consultez [Supervision d’Azure AKS](monitor-aks.md) pour obtenir une description de la supervision d’Azure AKS.
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](/azure/azure-monitor/essentials/monitor-azure-resource).

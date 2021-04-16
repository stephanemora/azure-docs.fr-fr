---
title: Afficher les métriques de cluster pour Azure Kubernetes Service (AKS)
description: Affichez les métriques de cluster pour Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975815"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Afficher les métriques de cluster pour Azure Kubernetes Service (AKS)

AKS fournit un ensemble de métriques pour le plan de contrôle, notamment le serveur d’API et l’autoscaler de cluster, ainsi que les nœuds de cluster. Ces métriques vous permettent de surveiller l’intégrité de votre cluster et de résoudre les problèmes. Vous pouvez afficher les métriques de votre cluster à l’aide du Portail Azure.

> [!NOTE]
> Ces métriques de cluster AKS se chevauchent avec un sous-ensemble de [métriques fournies par Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Afficher les métriques de votre cluster AKS à l’aide du Portail Azure

Pour afficher les métriques de votre cluster AKS :

1. Connectez-vous au [Portail Azure][azure-portal] et accédez à votre cluster AKS.
1. À gauche, sous *Supervision*, sélectionnez *Métriques*.
1. Créez un graphique pour les métriques que vous souhaitez afficher. Par exemple, créez un graphique :
    1. Pour *Étendue*, choisissez votre cluster.
    1. Pour *Espace de noms métrique*, choisissez les *métriques standard du service de conteneur (managé)* .
    1. Pour *Métrique*, sous *Pods*, choisissez *Nombre de pods par phase*.
    1. Pour *Agrégation*, choisissez *Moy*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

L’exemple ci-dessus montre les métriques pour le nombre moyen de pods pour *myAKSCluster*.

## <a name="available-metrics"></a>Métriques disponibles

Les métriques de cluster suivantes sont disponibles :

| Nom | Group | id | Description |
| --- | --- | --- | ---- |
| Demandes en cours | Serveur d’API (préversion) |apiserver_current_inflight_requests | Nombre maximal de requêtes en vol actuellement actives sur le serveur d’API par type de requête. |
| Intégrité des clusters | Autoscaler de cluster (préversion) | cluster_autoscaler_cluster_safe_to_autoscale | Détermine si l’autoscaler de cluster effectue ou non une action sur le cluster. |
| Effectuer un scale-down Cooldown | Autoscaler de cluster (préversion) | cluster_autoscaler_scale_down_in_cooldown | Détermine si la descente en puissante se trouve dans Cooldown. Aucun nœud n’est supprimé au cours de cette période. |
| Nœuds inutiles | Autoscaler de cluster (préversion) | cluster_autoscaler_unneeded_nodes_count | L’autoscaler de cluster marque ces nœuds comme des candidats pour la suppression et sont finalement supprimés. |
| Pods non planifiables | Autoscaler de cluster (préversion) | cluster_autoscaler_unschedulable_pods_count | Nombre de pods actuellement non planifiables dans le cluster. |
| Nombre total de cœurs d’unité centrale disponibles dans un cluster géré | Nœuds | kube_node_status_allocatable_cpu_cores | Nombre total de cœurs d’unité centrale disponibles dans un cluster managé. |
| Quantité totale de mémoire disponible dans un cluster géré | Nœuds | kube_node_status_allocatable_memory_bytes | Quantité totale de mémoire disponible dans un cluster managé. |
| États pour les différentes conditions de nœud | Nœuds | kube_node_status_condition | États pour les différentes conditions de nœud |
| Utilisation UC en millicœurs | Nœuds (préversion) | node_cpu_usage_millicores | Mesure agrégée de l’utilisation du processeur sur le cluster en millicœurs. |
| Pourcentage d’utilisation du processeur | Nœuds (préversion) | node_cpu_usage_percentage | Utilisation du processeur moyenne agrégée, mesurée en pourcentage sur le cluster. |
| Mémoire RSS en octets | Nœuds (préversion) | node_memory_rss_bytes | Mémoire RSS du conteneur utilisée, en octets. |
| Mémoire RSS en pourcentage | Nœuds (préversion) | node_memory_rss_percentage | Mémoire RSS du conteneur utilisée, en pourcentage. |
| Plage de travail de la mémoire en octets | Nœuds (préversion) | node_memory_working_set_bytes | Mémoire de plage de travail du conteneur utilisée, en octets. |
| Mémoire de plage de travail en pourcentage | Nœuds (préversion) | node_memory_working_set_percentage | Mémoire de plage de travail du conteneur utilisée, en pourcentage. |
| Octets utilisés sur le disque | Nœuds (préversion) | node_disk_usage_bytes | Espace disque utilisé en octets par appareil. |
| Pourcentage d’utilisation du disque | Nœuds (préversion) | node_disk_usage_percentage | Espace disque utilisé en pourcentage par appareil. |
| Réseau en octets | Nœuds (préversion) | node_network_in_bytes | Octets reçus sur le réseau. |
| Octets de sortie réseau | Nœuds (préversion) | node_network_out_bytes | Octets réseau transmis. |
| Nombre de pods en état Prêt | Pods | kube_pod_status_ready | Nombre de pods en état *Prêt*. |
| Nombre de pods par phase | Pods | kube_pod_status_phase | Nombre de pods par phase. |

> [!IMPORTANT]
> Les métriques en préversion peuvent être mises à jour ou modifiées, y compris leurs noms et leurs descriptions, pendant la version préliminaire.

## <a name="next-steps"></a>Étapes suivantes

En plus des métriques de cluster pour AKS, vous pouvez également utiliser Azure Monitor avec votre cluster AKS. Pour plus d’informations sur l’utilisation d’Azure Monitor avec AKS, consultez [Azure Monitor pour les conteneurs][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/
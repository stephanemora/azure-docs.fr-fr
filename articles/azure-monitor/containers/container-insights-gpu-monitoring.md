---
title: Configurer la surveillance de GPU à l’aide de Container Insights | Microsoft Docs
description: Cet article explique comment configurer la surveillance des clusters Kubernetes constitués de nœuds équipés de GPU NVIDIA et AMD à l’aide de Container Insights.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 9f18628b2814305d8629d3216bb0a0d1bab092fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524529"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Configurer la surveillance de GPU avec Container Insights

Depuis sa version *ciprod03022019*, l’agent intégré de Container Insights prend désormais en charge la surveillance de l’utilisation de GPU (cartes graphiques) sur les nœuds de cluster Kubernetes compatibles GPU, et surveille les pods/conteneurs demandant et utilisant des ressources GPU.

## <a name="supported-gpu-vendors"></a>Fabricants de GPU compatibles

Container Insights prend en charge la surveillance des clusters de GPU des fabricants de GPU suivants :

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Container Insights démarre automatiquement la surveillance de l’utilisation de GPU sur les nœuds ainsi que celle des demandes GPU en pods et en charges de travail, en collectant les métriques suivantes toutes les 60 secondes et en les stockant dans la table **InsightMetrics**.

>[!NOTE]
>Après le provisionnement d’un cluster avec les nœuds GPU, assurez-vous que le [pilote GPU](../../aks/gpu-cluster.md) est installé, comme exigé par AKS pour exécuter des charges de travail GPU. Container Insights collecte les métriques de GPU via des pods de pilote GPU s’exécutant dans le nœud. 

|Nom de métrique |Dimension de la métrique (balises) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Pourcentage de temps durant lequel le GPU est resté occupé/en cours de traitement pour un conteneur au cours de la période d’échantillonnage passée (60 secondes). Le cycle d’utilisation est un nombre compris entre 1 et 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Chaque conteneur peut spécifier des limites pour un ou plusieurs GPU. Il n’est pas possible de demander ou de limiter une partie d’un GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Chaque conteneur peut demander un ou plusieurs GPU. Il n’est pas possible de demander ou de limiter une partie d’un GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantité de mémoire de GPU en octets à disposition pour un conteneur spécifique. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantité de mémoire de GPU en octets utilisée par un conteneur spécifique. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Nombre de GPU dans un nœud pouvant être utilisés par Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Nombre total de GPU présents dans un nœud. |

## <a name="gpu-performance-charts"></a>Graphiques de performances de GPU 

Container Insights inclut des graphiques préconfigurés pour les métriques listées dans le tableau précédent sous la forme d’un classeur GPU pour chaque cluster. Pour une description des classeurs disponibles dans Container insights, consultez [Classeurs dans Container Insights](container-insights-reports.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Utiliser des GPU pour les charges de travail nécessitant beaucoup de ressources système sur Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) pour savoir comment déployer un cluster AKS constitué de nœuds avec GPU.

- Apprenez-en davantage sur les [Tailles de machine virtuelle à GPU optimisé dans Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Consultez [Prise en charge des GPU dans Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) pour en savoir plus sur la prise en charge expérimentale de Kubernetes de la gestion de GPU sur un ou plusieurs nœuds d’un cluster.

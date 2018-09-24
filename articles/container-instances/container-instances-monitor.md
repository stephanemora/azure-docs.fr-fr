---
title: Surveiller les conteneurs dans Azure Container Instances
description: Informations supplémentaires sur la surveillance de la consommation des ressources de calcul comme l’UC et la mémoire par vos conteneurs dans Azure Container Instances.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: marsma
ms.openlocfilehash: 6107aee0a4d7497e90186e0ea4bda6cd2712820d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979605"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Surveiller les ressources des conteneurs dans Azure Container Instances

Azure Monitor fournit des indications sur les ressources de calcul utilisées par vos instances de conteneurs. Utilisez Azure Monitor pour suivre l’utilisation de l’UC et de la mémoire des groupes de conteneurs et de leurs conteneurs. Ces données d’utilisation des ressources vous permettent de déterminer les meilleurs paramétrages d’UC et de mémoire pour vos groupes de conteneurs.

Ce document décrit en détail la collecte des données d’utilisation de l’UC et de la mémoire pour les instances de conteneurs à l’aide du portail Azure et d’Azure CLI.

> [!IMPORTANT]
> À ce stade, les métriques d’utilisation de ressources sont uniquement disponibles pour les conteneurs Linux.
>

## <a name="available-metrics"></a>Métriques disponibles

Azure Monitor fournit des métriques sur l’utilisation de l’**UC** et de la **mémoire** pour Azure Container Instances. Ces deux métriques sont disponibles pour un groupe de conteneurs et des conteneurs individuels.

Les métriques d’UC sont exprimées en **millicores**. Un millicore représente 1/1 000e de cœur d’UC. Par conséquent, 500 millicores (ou 500 m) représentent 50 % d’utilisation d’un cœur d’UC.

Les métriques de mémoire sont exprimées en **octets**.

## <a name="get-metrics---azure-portal"></a>Obtenir des métriques : portail Azure

Lorsqu’un groupe de conteneurs est créé, les données Azure Monitor sont disponibles dans le portail Azure. Pour afficher les métriques d’un groupe de conteneurs, sélectionnez le groupe de ressources, puis le groupe de conteneurs. Ici, vous pouvez voir des graphiques créés au préalable pour l’utilisation de l’UC et de la mémoire.

![double graphique][dual-chart]

Si un groupe de conteneurs comporte plusieurs conteneurs, utilisez une [dimension][monitor-dimension] afin de présenter les métriques pour chaque conteneur. Pour créer un graphique avec des métriques de conteneur individuel, procédez comme suit :

1. Sélectionnez **Surveiller** dans le menu de navigation de gauche.
2. Sélectionnez un groupe de conteneurs et une métrique (UC ou mémoire).
3. Sélectionnez le bouton de dimension vert, puis sélectionnez **Nom du conteneur**.

![dimension][dimension]

## <a name="get-metrics---azure-cli"></a>Obtenir des métriques : Azure CLI

Les données d’utilisation de l’UC et de la mémoire des instances de conteneurs peuvent également être collectées à l’aide d’Azure CLI. Tout d’abord, obtenez l’ID du groupe de conteneurs à l’aide de la commande suivante. Remplacez `<resource-group>` par le nom de votre groupe de ressources et `<container-group>` par le nom de votre groupe de conteneurs.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilisez la commande suivante pour obtenir les métriques d’utilisation de l’**UC**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Et utilisez la commande suivante pour obtenir les métriques d’utilisation de la **mémoire**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

Lorsqu’il s’agit d’un groupe de conteneurs, il est possible d’ajouter la dimension `containerName` afin de renvoyer ces données pour chaque conteneur.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la surveillance Azure, consultez [Vue d’ensemble de surveillance Azure][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring/monitoring-data-collection.md#multi-dimensional-metrics

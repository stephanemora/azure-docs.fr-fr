---
title: Surveiller les instances de conteneur
description: Comment superviser la consommation des ressources de calcul comme le processeur et la mémoire par vos conteneurs dans Azure Container Instances
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 7f485efc5bdc29760f0b4278b746940c947777e3
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219226"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Surveiller les ressources des conteneurs dans Azure Container Instances

[Azure Monitor][azure-monitoring] fournit des indications sur les ressources de calcul utilisées par vos instances de conteneurs. Ces données d’utilisation des ressources vous permettent de déterminer les meilleurs paramétrages de ressource pour vos groupes de conteneurs. Azure Monitor fournit également des métriques qui effectuent le suivi de l’activité réseau dans vos instances de conteneur.

Ce document décrit en détail la collecte des métriques Azure Monitor pour les instances de conteneurs à l’aide du portail Azure et d’Azure CLI.

> [!IMPORTANT]
> Les métriques Azure Monitor dans Azure Container Instances sont en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="preview-limitations"></a>Limitations de la version préliminaire

À ce stade, les métriques Azure Monitor sont uniquement disponibles pour les conteneurs Linux.

## <a name="available-metrics"></a>Métriques disponibles

Azure Monitor fournit les [métriques suivantes pour Azure Container Instances][supported-metrics]. Ces métriques sont disponibles pour un groupe de conteneurs et des conteneurs individuels. Par défaut, les métriques sont agrégées en tant que moyennes.

- **Utilisation du processeur** mesurée en **millicœurs** 
  - Un millicœur représente 1/1000e de cœur de processeur, donc 500 millicœurs représentent une utilisation de 0,5 cœur de processeur.
- **Utilisation de la mémoire** en octets
- **Octets réseau reçus** par seconde
- **Octets réseau transmis** par seconde 

## <a name="get-metrics---azure-portal"></a>Obtenir des métriques : portail Azure

Lorsqu’un groupe de conteneurs est créé, les données Azure Monitor sont disponibles dans le portail Azure. Pour voir les métriques d’un groupe de conteneurs, accédez à la page **Vue d’ensemble** de ce groupe de conteneurs. Vous pouvez y voir des graphiques précréés pour chacune des métriques disponibles.

![double graphique][dual-chart]

Dans un groupe de conteneurs comportant plusieurs conteneurs, utilisez une [dimension][monitor-dimension] pour afficher les métriques par conteneur. Pour créer un graphique avec des métriques de conteneur individuel, procédez comme suit :

1. Dans la page **Vue d’ensemble**, sélectionnez un des graphiques de métriques, par exemple **UC**. 
1. Sélectionnez le bouton **Appliquer la division**, puis sélectionnez **Nom du conteneur**.

![Capture d’écran des métriques pour une instance de conteneur avec l’option Appliquer la division sélectionnée et le nom du conteneur sélectionné.][dimension]

## <a name="get-metrics---azure-cli"></a>Obtenir des métriques : Azure CLI

Les métriques des instances de conteneurs peuvent également être collectées à l’aide d’Azure CLI. Tout d’abord, obtenez l’ID du groupe de conteneurs à l’aide de la commande suivante. Remplacez `<resource-group>` par le nom de votre groupe de ressources et `<container-group>` par le nom de votre groupe de conteneurs.


```azurecli
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilisez la commande suivante pour obtenir les métriques d’utilisation de l’**UC**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Changez la valeur du paramètre `--metric` dans la commande pour obtenir d’autres [métriques prises en charge][supported-metrics]. Par exemple, utilisez la commande suivante pour obtenir les métriques d’utilisation de la **mémoire**. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Quand il s’agit d’un groupe de conteneurs, il est possible d’ajouter la dimension `containerName` afin de retourner les métriques par conteneur.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la supervision Azure, consultez [Vue d’ensemble de la supervision Azure][azure-monitoring].

Découvrez comment créer des [alertes de métrique][metric-alert] pour être averti quand une métrique pour Azure Container Instances franchit un seuil.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups

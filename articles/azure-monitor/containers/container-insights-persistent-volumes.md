---
title: Configurer la surveillance de volumes persistants avec Container Insights | Microsoft Docs
description: Cet article explique comment configurer la surveillance de clusters Kubernetes constitués de volumes persistants à l’aide de Container Insights.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 593ee36bf01a7f2979b7db9ef8b28e0f805077ad
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065653"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurer la surveillance de volumes persistants avec Container Insights

Depuis la version de l’agent *ciprod10052020*, l’agent intégré Container Insights prend en charge la surveillance de l’usage de volumes persistants. Avec la version d’agent *ciprod01112021*, l’agent prends en charge la surveillance de l’inventaire de volumes persistants, avec des informations sur l’état, la classe de stockage, le type, le mode d’accès, etc.
## <a name="pv-metrics"></a>Métriques de volume persistant

Container Insights lance automatiquement la supervision des volumes persistants en collectant les métriques suivantes toutes les 60 secondes et en les stockant dans la table **InsightMetrics**.

| Nom de métrique | Dimension de la métrique (balises) | Description de la métrique |
|-----|-----------|----------|
| `pvUsedBytes`| `podUID`, `podName`, `pvcName`, `pvcNamespace`, `capacityBytes`, `clusterId`, `clusterName`| Espace utilisé en octets pour un volume persistant spécifique avec une revendication utilisée par un pod spécifique. `capacityBytes` est plié en tant que dimension dans le champ Balises pour réduire le coût d’ingestion des données et simplifier les requêtes.|

En savoir plus sur la configuration des métriques de volume persistant collectées [ici](./container-insights-agent-config.md).

## <a name="pv-inventory"></a>Inventaire des volumes persistants

Container Insights lance automatiquement la supervision des volumes persistants en collectant les informations suivantes toutes les 60 secondes et en les stockant dans la table **KubePVInventory**.

|Données |source de données| Type de données| Champs|
|-----|-----------|----------|-------|
|Inventaire des volumes persistants dans un cluster Kubernetes |API Kube |`KubePVInventory` |    `PVName`, `PVCapacityBytes`, `PVCName`, `PVCNamespace`, `PVStatus`, `PVAccessModes`, `PVType`, `PVTypeInfo`, `PVStorageClassName`, `PVCreationTimestamp`, `TimeGenerated`, `ClusterId`, `ClusterName`, `_ResourceId` |

## <a name="monitor-persistent-volumes"></a>Superviser les volumes persistants

Container Insights inclut des graphiques préconfigurés pour cette métrique d’usage et les informations d’inventaire sous la forme d’un classeur pour chaque cluster. Vous pouvez également activer une alerte recommandée pour l’utilisation de volumes persistants, ainsi que pour interroger ces métriques dans Log Analytics.  

### <a name="workload-details-workbook"></a>Classeur avec les détails de la charge de travail

Vous pouvez rechercher des graphiques d’utilisation pour des charges de travail spécifiques dans l’onglet Volume persistant du **Classeur détails de la charge de travail** directement à partir d’un cluster AKS en sélectionnant Classeurs dans le volet de gauche, dans la liste déroulante **Afficher les classeurs** dans le volet Insights, ou à partir de l’onglet **Rapports (préversion)** dans le volet Insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Exemple de classeur pour les charges de travail de volume persistant Azure Monitor":::

### <a name="persistent-volume-details-workbook"></a>Classeur avec les détails du volume persistant

Vous pouvez obtenir une vue d’ensemble de l’inventaire des volumes persistants dans le classeur **Détails de volume persistant** directement à partir d’un cluster AKS en sélectionnant Classeurs dans le volet gauche. Vous pouvez également ouvrir ce classeur à partir de la liste déroulante **Afficher les classeurs** dans le volet Insights ou à partir de l’onglet **Rapports** dans le volet Insights.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Exemple de classeur avec les détail du volume persistant Azure Monitor":::

### <a name="persistent-volume-usage-recommended-alert"></a>Alerte d’utilisation de volume persistant recommandé
Vous pouvez activer une alerte recommandée pour vous avertir lorsque la moyenne de l’utilisation de volume persistant pour un pod est supérieure à 80 %. En savoir plus sur les alertes [ici](./container-insights-metric-alerts.md) et sur la façon de remplacer le seuil par défaut [ici](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les métriques de volume persistant collectées [ici](./container-insights-agent-config.md).
---
title: Configurer la surveillance de volumes persistants avec Container Insights | Microsoft Docs
description: Cet article explique comment configurer la surveillance de clusters Kubernetes constitués de volumes persistants à l’aide de Container Insights.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713726"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Configurer la surveillance de volumes persistants avec Container Insights

Depuis la version de l’agent *ciprod10052020*, l’agent intégré Container Insights prend en charge la surveillance de l’usage de volumes persistants.

## <a name="pv-metrics"></a>Métriques de volume persistant

Container Insights lance automatiquement la supervision des volumes persistants en collectant les métriques suivantes toutes les 60 secondes et en les stockant dans la table **InsightMetrics**.

|Nom de métrique |Dimension de la métrique (balises) |Description |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Espace utilisé en octets pour un volume persistant spécifique avec une revendication utilisée par un pod spécifique. `pvCapacityBytes` est plié en tant que dimension dans le champ Balises pour réduire le coût d’ingestion des données et simplifier les requêtes.|

## <a name="monitor-persistent-volumes"></a>Superviser les volumes persistants

Container Insights inclut des graphiques préconfigurés pour cette métrique dans un classeur pour chaque cluster. Vous pouvez trouver les graphiques dans l’onglet Volume persistant du classeur **Détails de la charge de travail** directement à partir d’un cluster AKS en sélectionnant Classeurs dans le volet gauche, puis dans la liste déroulante **Voir les classeurs** dans Insight. Vous pouvez également activer une alerte recommandée pour l’utilisation de volumes persistants, ainsi que pour interroger ces métriques dans Log Analytics.  

![Exemple de classeur pour les charges de travail de volume persistant Azure Monitor](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les métriques de volume persistant collectées [ici](./container-insights-agent-config.md).

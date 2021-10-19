---
title: Comment afficher les mesures - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Accéder aux mesures de base de données Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/05/2021
ms.openlocfilehash: b698a69631ba2ad8aba58f6de17d8cbee1cbafaf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621042"
---
# <a name="how-to-view-metrics-in-azure-database-for-postgresql---hyperscale-citus"></a>Comment afficher les mesures dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Les mesures de ressource sont disponibles pour tous les nœuds d’un groupe de serveurs Hyperscale (Citus), et dans l’agrégat sur les nœuds.

## <a name="view-metrics"></a>Afficher les mesures

Pour accéder aux mesures d’un groupe de serveurs Hyperscale (Citus), ouvrez **Mesures** sous **Surveillance** dans le portail Azure.

:::image type="content" source="media/howto-hyperscale-monitoring/metrics.png" alt-text="Écran des mesures":::

Choisissez une dimension et une agrégation (par exemple, **Pourcentage d’UC** et **Maximum**) pour afficher la mesure agrégée sur tous les nœuds. Pour obtenir une explication de chaque mesure, consultez [cette page](concepts-hyperscale-monitoring.md#list-of-metrics).

:::image type="content" source="media/howto-hyperscale-monitoring/dimensions.png" alt-text="Sélectionner une dimension":::

### <a name="view-metrics-per-node"></a>Afficher les mesures par nœud

L’affichage des mesures de chaque nœud séparément sur le même graphique est appelé « division ».
Pour l’activer, sélectionnez **Appliquer la division** :

:::image type="content" source="media/howto-hyperscale-monitoring/splitting.png" alt-text="Appliquer la division":::

Sélectionnez la valeur de division. Pour les nœuds Hyperscale (Citus), choisissez **Nom du serveur**.

:::image type="content" source="media/howto-hyperscale-monitoring/split-value.png" alt-text="Sélectionner la valeur de division":::

Les mesures sont tracées dans une ligne de couleur par nœud.

:::image type="content" source="media/howto-hyperscale-monitoring/split-chart.png" alt-text="Chartlines à plusieurs nœuds":::

## <a name="next-steps"></a>Étapes suivantes

* Examiner les [concepts de surveillance](concepts-hyperscale-monitoring.md) d’Hyperscale (Citus)

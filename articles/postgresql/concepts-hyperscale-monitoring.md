---
title: Surveiller et régler – Hyperscale (Citus) – Azure Database pour PostgreSQL
description: Cet article décrit les fonctionnalités de surveillance et de réglage dans Azure Database pour PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975514"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Surveiller et régler dans Azure Database pour PostgreSQL - Hyperscale (Citus)

La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Hyperscale (Citus) propose différentes options de surveillance pour fournir des informations sur le comportement des nœuds dans un groupe de serveurs.

## <a name="metrics"></a>Mesures

Hyperscale (Citus) fournit des mesures pour chaque nœud dans un groupe de serveurs. Les mesures permettent de mieux comprendre le comportement des ressources de prise en charge. Chaque métrique est émise selon une fréquence d’une minute et est conservée jusqu’à 30 jours dans l’historique.

Outre l’affichage des graphiques des mesures, vous pouvez configurer des alertes. Pour des instructions étape par étape, consultez [Configurer des alertes](howto-hyperscale-alert-on-metric.md).  Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques

Ces mesures sont disponibles pour les nœuds Hyperscale (Citus) :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|
|active_connections|Connexions actives|Count|Nombre de connexions actives sur le serveur.|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|iops|E/S par seconde|Count|Consultez la [définition iOPS](../virtual-machines/linux/premium-storage-performance.md#iops) et le [débit Hyperscale](concepts-hyperscale-configuration-options.md)|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|network_bytes_ingress|Network In|Octets|Entrée réseau entre connexions actives.|
|network_bytes_egress|Network Out|Octets|Sortie réseau entre connexions actives.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service peut inclure les fichiers de base de données, les journaux d’activité des transactions et les journaux d’activité du serveur.|

Azure ne fournit aucune mesure agrégée pour le cluster dans son ensemble, mais les mesures de plusieurs nœuds peuvent être placées sur le même graphique.

## <a name="next-steps"></a>Étapes suivantes

- Consultez le [guide pratique pour configurer des alertes](howto-hyperscale-alert-on-metric.md) pour savoir comment créer une alerte sur une métrique.

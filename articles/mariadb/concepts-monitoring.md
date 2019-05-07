---
title: Supervision dans Azure Database for MariaDB
description: Cet article décrit les métriques de surveillance et d’alerte pour Azure Database pour MariaDB, notamment les statistiques sur l’UC, le stockage et la connexion.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: babe2ac55953940370daa0731463ed6ed8988502
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "64925919"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Supervision dans Azure Database for MariaDB
La surveillance des données relatives à vos serveurs vous aide à résoudre les problèmes et à optimiser votre charge de travail. Azure Database pour MariaDB propose diverses métriques qui fournissent des insights sur le comportement de votre serveur.

## <a name="metrics"></a>Mesures
Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. Vous pouvez configurer des alertes basées sur les métriques. Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Liste des métriques
Ces métriques sont disponibles pour Azure Database pour MariaDB :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service peut inclure les fichiers de base de données, les journaux d’activité des transactions et les journaux d’activité du serveur.|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Pourcentage de stockage du journal du serveur utilisé par rapport au stockage du journal du serveur maximal défini pour le serveur.|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Quantité de stockage du journal du serveur en cours d’utilisation.|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Stockage du journal du serveur maximal défini pour ce serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Connexions actives|Nombre|Nombre de connexions actives sur le serveur.|
|connections_failed|Connexions ayant échoué|Nombre|Nombre de connexions au serveur ayant échoué.|
|network_bytes_egress|Network Out|Octets|Sortie réseau entre connexions actives.|
|network_bytes_ingress|Network In|Octets|Entrée réseau entre connexions actives.|

## <a name="server-logs"></a>Journaux d’activité du serveur
Vous pouvez activer la journalisation des requêtes lentes sur votre serveur. Ces journaux sont également disponibles via les journaux de Diagnostic Azure dans Azure Monitor journaux, Event Hubs et compte de stockage. Pour en savoir plus sur la journalisation, consultez la page  [journaux d’activité serveur](concepts-server-logs.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Consultez le [guide pratique pour configurer des alertes](howto-alert-metric.md) pour savoir comment créer une alerte sur une métrique.

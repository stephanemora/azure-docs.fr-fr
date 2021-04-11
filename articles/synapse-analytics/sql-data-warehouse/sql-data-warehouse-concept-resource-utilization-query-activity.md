---
title: Facilité de gestion et supervision - Activité des requêtes, utilisation des ressources
description: Découvrez les capacités disponibles pour gérer et superviser Azure Synapse Analytics. Utilisez le portail Azure et les vues de gestion dynamique (DMV, Dynamic Management Views) pour comprendre l’activité des requêtes et l’utilisation des ressources de votre entrepôt de données.
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7f093860e9962db055a7551f5cdb608ecad9b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585670"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Supervision de l’utilisation des ressources et de l’activité des requêtes dans Azure Synapse Analytics

Azure Synapse Analytics fournit une expérience de supervision enrichie dans le Portail Azure pour intégrer des insights concernant la charge de travail de votre entrepôt de données. Le portail Azure est l’outil recommandé pour superviser votre entrepôt de données car il offre des périodes de conservation configurables, des alertes, des suggestions, ainsi que des graphiques et des tableaux de bord personnalisables pour les métriques et les journaux d’activité. Le portail permet également une intégration à d’autres services de supervision Azure, comme Azure Monitor (journaux d’activité) avec Log Analytics, pour fournir une expérience de supervision globale non seulement pour votre entrepôt de données, mais également pour l’ensemble de votre plateforme d’analytique Azure afin de bénéficier d’une expérience de supervision intégrée. Cette documentation décrit les capacités d’analyse disponibles pour optimiser et gérer votre plateforme d’analytique avec Synapse SQL.

## <a name="resource-utilization"></a>Utilisation des ressources

Les mesures suivantes sont disponibles dans le Portail Azure pour Synapse SQL. Ces métriques sont exposées via [Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Nom de métrique             | Description                                                  | Type d’agrégation |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Pourcentage UC          | Utilisation du processeur dans tous les nœuds pour l’entrepôt de données      | Moy, Min, Max    |
| Pourcentage E/S des données      | Utilisation d’E/S dans tous les nœuds pour l’entrepôt de données       | Moy, Min, Max    |
| Pourcentage de mémoire       | Utilisation de la mémoire (SQL Server) dans tous les nœuds pour l’entrepôt de données | Moy, Min, Max   |
| Requêtes actives          | Nombre de requêtes actives en cours d’exécution sur le système             | SUM              |
| Requêtes en file d’attente          | Nombre de requêtes en attente pour démarrer l’exécution          | SUM              |
| Connexions réussies  | Nombre de connexions réussies à la base de données | Somme, Nombre       |
| Connexions ayant échoué      | Nombre de connexions non réussies à la base de données | Somme, Nombre       |
| Bloqué par le pare-feu     | Nombre de connexions à l’entrepôt de données qui ont été bloquées     | Somme, Nombre       |
| Limite DWU               | Objectif de niveau de service de l’entrepôt de données                | Moy, Min, Max    |
| Pourcentage DWU          | Maximum entre le pourcentage UC et le pourcentage E/S des données        | Moy, Min, Max    |
| DWU utilisé                | Limite DWU * Pourcentage DWU                                   | Moy, Min, Max    |
| Pourcentage d’accès au cache    | (accès au cache/échecs d’accès au cache) * 100, où la valeur « accès au cache » correspond à la somme de tous les accès aux segments de columnstore dans le cache de disque SSD local et la valeur « échecs d’accès au cache » correspond aux échecs des segments de columnstore dans le cache de disque SSD local cumulés dans tous les nœuds | Moy, Min, Max    |
| Pourcentage de cache utilisé   | (cache utilisé/capacité du cache) * 100, ou la valeur « cache utilisé » correspond à la somme de tous les octets présents dans le cache de disque SSD local pour tous les nœuds et la valeur « capacité du cache » correspond à la somme de la capacité de stockage du disque SSD local dans tous les nœuds | Moy, Min, Max    |
| Pourcentage de tempdb locale | Utilisation de la tempdb locale sur tous les nœuds de calcul. Des valeurs sont émises toutes les cinq minutes | Moy, Min, Max    |

Points importants à prendre en compte lors de l’affichage des métriques et de la définition des alertes :

- La mesure DWU utilisée n’est qu’une **représentation de haut niveau de l’utilisation** dans le pool SQL et n’est pas censée être un indicateur complet de l’utilisation. Pour déterminer s’il faut monter ou diminuer la puissance, prenez en compte tous les facteurs qui peuvent être concernés par la DWU tels que la concurrence, la mémoire, la tempdb et la capacité du cache adaptatif. Nous vous recommandons d’[exécuter votre charge de travail à différents paramètres DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) pour déterminer ce qui fonctionne le mieux pour atteindre vos objectifs stratégiques.
- Les connexions ayant échoué et réussies sont signalées pour un entrepôt de données particulier, pas pour le serveur lui-même.
- Le pourcentage de mémoire reflète l’utilisation même si l’entrepôt de données est dans un état inactif. Il ne reflète pas la consommation de la mémoire par la charge de travail active. Utilisez et suivez cette métrique ainsi que d’autres (tempdb, cache gen2) pour prendre une décision holistique quant à la nécessité d’effectuer une mise à l’échelle pour accroître la capacité de cache en vue d’améliorer les performances de la charge de travail en fonction de vos besoins.

## <a name="query-activity"></a>Activité des requêtes

Pour une expérience programmatique lors de l’analyse de Synapse SQL via T-SQL, le service fournit un ensemble de vues de gestion dynamique (DMV). Ces vues sont utiles quand vous dépannez et identifiez activement les goulots d’étranglement des performances de votre charge de travail.

Pour voir la liste des vues DMV qui s’appliquent à SQL Synapse, consultez cette [documentation](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Journalisation des métriques et des diagnostics 

Les métriques et les journaux peuvent être exportés vers Azure Monitor, en particulier le composant des [journaux Azure Monitor](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), et sont accessibles par programmation par le biais de [requêtes de journal](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). La latence de journal pour Synapse SQL est de 10 à 15 minutes environ. Pour plus d’informations sur les facteurs qui impactent la latence, consultez la documentation suivante.

## <a name="next-steps"></a>Étapes suivantes

Les guides pratiques suivants décrivent les scénarios et cas d’usage courants de monitoring et de gestion d’un entrepôt de données :

- [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](sql-data-warehouse-manage-monitor.md)
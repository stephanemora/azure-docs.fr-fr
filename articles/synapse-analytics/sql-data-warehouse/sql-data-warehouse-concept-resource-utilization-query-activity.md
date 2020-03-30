---
title: Facilité de gestion et supervision - Activité des requêtes, utilisation des ressources
description: Découvrez les capacités disponibles pour gérer et superviser Azure Synapse Analytics. Utilisez le portail Azure et les vues de gestion dynamique (DMV, Dynamic Management Views) pour comprendre l’activité des requêtes et l’utilisation des ressources de votre entrepôt de données.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350669"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Supervision de l’utilisation des ressources et de l’activité des requêtes dans Azure Synapse Analytics
Azure Synapse Analytics fournit une expérience de supervision enrichie dans le Portail Azure pour intégrer des insights concernant la charge de travail de votre entrepôt de données. Le portail Azure est l’outil recommandé pour superviser votre entrepôt de données car il offre des périodes de conservation configurables, des alertes, des suggestions, ainsi que des graphiques et des tableaux de bord personnalisables pour les métriques et les journaux d’activité. Le portail permet également une intégration à d’autres services de supervision Azure, comme Azure Monitor (journaux d’activité) avec Log Analytics, pour fournir une expérience de supervision globale non seulement pour votre entrepôt de données, mais également pour l’ensemble de votre plateforme d’analytique Azure afin de bénéficier d’une expérience de supervision intégrée. Cette documentation décrit les capacités de supervision disponibles pour optimiser et gérer votre plateforme d’analytique avec SQL Analytics. 

## <a name="resource-utilization"></a>Utilisation des ressources 
Les métriques suivantes sont disponibles dans le Portail Azure pour SQL Analytics. Ces métriques sont exposées via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).


| Nom de métrique             | Description                                                  | Type d’agrégation |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Pourcentage UC          | Utilisation du processeur dans tous les nœuds pour l’entrepôt de données      | Moy, Min, Max    |
| Pourcentage E/S des données      | Utilisation d’E/S dans tous les nœuds pour l’entrepôt de données       | Moy, Min, Max    |
| Pourcentage de mémoire       | Utilisation de la mémoire (SQL Server) dans tous les nœuds pour l’entrepôt de données | Moy, Min, Max   |
| Requêtes actives          | Nombre de requêtes actives en cours d’exécution sur le système             | SUM              |
| Requêtes en file d’attente          | Nombre de requêtes en attente pour démarrer l’exécution          | SUM              |
| Connexions réussies  | Nombre de connexions réussies aux données                 | Somme, Nombre       |
| Connexions ayant échoué      | Nombre de connexions à l’entrepôt de données ayant échoué           | Somme, Nombre       |
| Bloqué par le pare-feu     | Nombre de connexions à l’entrepôt de données qui ont été bloquées     | Somme, Nombre       |
| Limite DWU               | Objectif de niveau de service de l’entrepôt de données                | Moy, Min, Max    |
| Pourcentage DWU          | Maximum entre le pourcentage UC et le pourcentage E/S des données        | Moy, Min, Max    |
| DWU utilisé                | Limite DWU * Pourcentage DWU                                   | Moy, Min, Max    |
| Pourcentage d’accès au cache    | (accès au cache/échecs d’accès au cache) * 100, où la valeur « accès au cache » correspond à la somme de tous les accès aux segments de columnstore dans le cache de disque SSD local et la valeur « échecs d’accès au cache » correspond aux échecs des segments de columnstore dans le cache de disque SSD local cumulés dans tous les nœuds | Moy, Min, Max    |
| Pourcentage de cache utilisé   | (cache utilisé/capacité du cache) * 100, ou la valeur « cache utilisé » correspond à la somme de tous les octets présents dans le cache de disque SSD local pour tous les nœuds et la valeur « capacité du cache » correspond à la somme de la capacité de stockage du disque SSD local dans tous les nœuds | Moy, Min, Max    |
| Pourcentage de tempdb locale | Utilisation de la tempdb locale sur tous les nœuds de calcul. Des valeurs sont émises toutes les cinq minutes | Moy, Min, Max    |
| Taille de stockage des données | Taille totale des données chargées dans la base de données. Cela comprend les données résidant dans les tables CCI et non-CCI, où la taille des tables non-CCI est mesurée par la taille totale du fichier de base de données. | SUM |
| Taille de la récupération d’urgence | Taille totale de la sauvegarde géographique effectuée toutes les 24 heures | SUM |
| Taille du stockage des captures instantanées | Taille totale des captures instantanées prises pour fournir des points de restauration de base de données. Cela comprend les captures instantanées automatisées et définies par l’utilisateur. | SUM |

Points importants à prendre en compte lors de l’affichage des métriques et de la définition des alertes :

- La mesure DWU utilisée n’est qu’une **représentation de haut niveau de l’utilisation** dans le pool SQL et n’est pas censée être un indicateur complet de l’utilisation. Pour déterminer s’il faut monter ou diminuer la puissance, prenez en compte tous les facteurs qui peuvent être concernés par la DWU tels que la concurrence, la mémoire, la tempdb et la capacité du cache adaptatif. Nous vous recommandons d’[exécuter votre charge de travail à différents paramètres DWU](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) pour déterminer ce qui fonctionne le mieux pour atteindre vos objectifs stratégiques.
- Les connexions ayant échoué et réussies sont signalées pour un entrepôt de données particulier, pas pour le serveur logique
- Le pourcentage de mémoire reflète l’utilisation même si l’entrepôt de données est dans un état inactif. Il ne reflète pas la consommation de la mémoire par la charge de travail active. Utilisez et suivez cette métrique ainsi que d’autres (tempdb, cache gen2) pour prendre une décision holistique quant à la nécessité d’effectuer une mise à l’échelle pour accroître la capacité de cache en vue d’améliorer les performances de la charge de travail en fonction de vos besoins.


## <a name="query-activity"></a>Activité des requêtes
Pour une expérience programmatique lors de la supervision de SQL Analytics via T-SQL, le service fournit un ensemble de vues de gestion dynamique (DMV). Ces vues sont utiles quand vous dépannez et identifiez activement les goulots d’étranglement des performances de votre charge de travail.

Pour afficher la liste des vues DMV fournies par SQL Analytics, reportez-vous à cette [documentation](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Journalisation des métriques et des diagnostics
Les métriques et les journaux peuvent être exportés vers Azure Monitor, en particulier le composant des [journaux Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), et sont accessibles par programmation par le biais de [requêtes de journal](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata). La latence de journal pour SQL Analytics est d’environ 10 à 15 minutes. Pour plus d’informations sur les facteurs qui impactent la latence, consultez la documentation suivante.


## <a name="next-steps"></a>Étapes suivantes
Les guides pratiques suivants décrivent des scénarios courants et des cas d’utilisation pour la supervision et la gestion de votre entrepôt de données :

- [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

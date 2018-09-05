---
title: Facilité de gestion et supervision d’Azure SQL Data Warehouse - activité des requêtes, utilisation des ressources | Microsoft Docs
description: Découvrez les fonctionnalités qui sont disponibles pour gérer et superviser Azure SQL Data Warehouse. Utilisez le portail Azure et les vues de gestion dynamique (DMV, Dynamic Management Views) pour comprendre l’activité des requêtes et l’utilisation des ressources de votre entrepôt de données.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c783045d242725ee19dfe7e0baee13625d986312
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246492"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Supervision de l’utilisation des ressources et de l’activité des requêtes dans Azure SQL Data Warehouse
Azure SQL Data Warehouse fournit une expérience de supervision enrichie dans le portail Azure pour intégrer des insights à la charge de travail de votre entrepôt de données. Le portail Azure est l’outil recommandé pour superviser votre entrepôt de données car il offre des périodes de conservation configurables, des alertes, des recommandations, ainsi que des graphiques et des tableaux de bord personnalisables pour les métriques et les journaux. Le portail permet également une intégration à d’autres services de supervision Azure, comme Operations Management Suite (OMS)/Log Analytics et Azure Monitor, pour fournir une expérience de supervision globale non seulement pour votre entrepôt de données, mais également pour l’ensemble de votre plateforme d’analytique Azure afin de bénéficier d’une expérience de supervision intégrée. Cette documentation décrit les fonctionnalités de supervision disponibles pour optimiser et gérer votre plateforme d’analytique avec SQL Data Warehouse. 

## <a name="resource-utilization"></a>Utilisation des ressources 
Les métriques suivantes sont disponibles dans le portail Azure.

| Nom de métrique                           | Description     | Type d’agrégation |
| --------------------------------------- | ---------------- | --------------------------------------- |
| Pourcentage UC                          | Utilisation du processeur dans tous les nœuds pour l’entrepôt de données | Maximale      |
| Pourcentage E/S des données                      | Utilisation d’E/S dans tous les nœuds pour l’entrepôt de données | Maximale   |
| Connexions réussies                  | Nombre de connexions réussies aux données | Total            |
| Connexions ayant échoué                      | Nombre de connexions à l’entrepôt de données ayant échoué | Total            |
| Bloqué par le pare-feu                     | Nombre de connexions à l’entrepôt de données qui ont été bloquées | Total            |
| Limite DWU                              | Objectif de niveau de service de l’entrepôt de données | Maximale   |
| Pourcentage DWU                          | Maximum entre le pourcentage UC et le pourcentage E/S des données | Maximale   |
| DWU utilisé                                | Limite DWU * Pourcentage DWU | Maximale   |
| Pourcentage d’accès au cache | (accès au cache/échecs d’accès au cache) * 100, où la valeur « accès au cache » correspond à la somme de tous les accès aux segments de columnstore dans le cache de disque SSD local et la valeur « échecs d’accès au cache » correspond aux échecs des segments de columnstore dans le cache de disque SSD local cumulés dans tous les nœuds | Maximale |
| Pourcentage de cache utilisé | (cache utilisé/capacité du cache) * 100, ou la valeur « cache utilisé » correspond à la somme de tous les octets présents dans le cache de disque SSD local pour tous les nœuds et la valeur « capacité du cache » correspond à la somme de la capacité de stockage du disque SSD local dans tous les nœuds | Maximale |

## <a name="query-activity"></a>Activité des requêtes
Pour une expérience de programmation lors de la supervision de SQL Data Warehouse T-SQL, le service fournit un ensemble de vues de gestion dynamique (DMV). Ces vues sont utiles quand vous dépannez et identifiez activement les goulots d’étranglement des performances de votre charge de travail.

Pour afficher la liste des vues DMV fournies par SQL Data Warehouse, reportez-vous à cette [documentation](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Journalisation des métriques et des diagnostics
Les métriques et les journaux peuvent être exportés vers [Operations Management Suite](https://azure.microsoft.com/resources/videos/operations-management-suite-oms-overview/) (OMS), en particulier le composant [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), et sont accessibles par programmation via la [recherche dans les journaux](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata).


> [!NOTE]
> Depuis août 2018, les journaux sont en cours d’implémentation pour SQL Data Warehouse

## <a name="next-steps"></a>Étapes suivantes
Les guides pratiques suivants décrivent des scénarios courants et des cas d’utilisation pour la supervision et la gestion de votre entrepôt de données :

- [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)


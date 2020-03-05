---
title: Requêtes de journal Azure Monitor | Microsoft Docs
description: Références à des ressources permettant d’apprendre à écrire des requêtes de journal dans Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: fc943ac3cf82d22a58d0ba3390ad8d9bbee6a4c9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669384"
---
# <a name="azure-monitor-log-queries"></a>Requêtes de journal Azure Monitor
Les journaux d’activité Azure Monitor reposent sur Azure Data Explorer ; les requêtes de journal Azure Monitor utilisent une version du même langage de requête Kusto. La [documentation du langage de requête Kusto](/azure/kusto/query), qui rassemble toutes les informations sur ce langage, doit constituer votre principale référence lorsque vous écrivez des requêtes de journal Azure Monitor. Cette page comporte des liens vers d’autres ressources permettant d’apprendre à écrire des requêtes et de découvrir les différences avec l’implémentation Azure Monitor du langage.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Prise en main

- [Bien démarrer avec l’analytique des journaux d’activité Azure Monitor](get-started-portal.md) est une leçon d’écriture de requêtes et d’utilisation des résultats sur le Portail Azure.
- [Bien démarrer avec les requêtes de journal Azure Monitor](get-started-queries.md) est une leçon d’écriture de requêtes à l’aide de données de journal Azure Monitor.

## <a name="concepts"></a>Concepts
- [Analyser les données de journal dans Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) donne une vue d’ensemble synthétique des requêtes de journal et décrit la structure des données de journal Azure Monitor.
- [Afficher et analyser les données de journal dans Azure Monitor](../../azure-monitor/log-query/portals.md) décrit les portails permettant de créer et d’exécuter des requêtes de journal.

## <a name="reference"></a>Informations de référence

- [Référence du langage de requête](/azure/kusto/query) représente la ressource complète disponible sur le langage de requête Kusto.
- [Différences propres au langage de requête de journal Azure Monitor](data-explorer-difference.md) décrit les différences existant entre les versions du langage de requête Kusto.
- [Propriétés standard dans les enregistrements de journaux Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) décrit les propriétés communes à toutes les données de journal Azure Monitor.
- [Effectuer des requêtes de journal inter-ressources dans Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) explique comment écrire des requêtes de journal qui utilisent des données provenant de plusieurs espaces de travail Log Analytics et applications Application Insights.


## <a name="examples"></a>Exemples

- [Exemples de requête de journal Azure Monitor](examples.md) donne des exemples de requêtes utilisant des données de journal Azure Monitor.



## <a name="lessons"></a>Leçons

- [Utiliser les chaînes dans les requêtes de journal Azure Monitor](string-operations.md) explique comment utiliser des données de type chaîne.
- [Utiliser les valeurs Date/Heure dans les requêtes de journal Azure Monitor](datetime-operations.md) explique comment utiliser des données de type date et heure. 
- [Agrégations dans les requêtes de journal Azure Monitor](aggregations.md) et [Agrégations avancées dans les requêtes de journal Azure Monitor](advanced-aggregations.md) expliquent comment agréger et synthétiser les données.
- [Jointures dans les requêtes de journal Azure Monitor](joins.md) explique comment joindre des données provenant de plusieurs tables.
- [Utiliser JSON et les structures de données dans des requêtes de journal Azure Monitor](json-data-structures.md) explique comment analyser des données JSON.
- [Écrire des requêtes de journal avancées dans Azure Monitor](advanced-query-writing.md) décrit les stratégies de création de requêtes complexes et de réutilisation du code.
- [Créer des graphiques et des diagrammes à partir de requêtes de journal Azure Monitor](charts.md) explique comment visualiser les données issues d’une requête de journal.

## <a name="cheatsheets"></a>Aide-mémoires

-  [De SQL à la requête de journal Azure Monitor](sql-cheatsheet.md) accompagne les utilisateurs qui connaissent déjà SQL.
-  [De Splunk à la requête de journal Azure Monitor](splunk-cheatsheet.md) accompagne les utilisateurs qui connaissent déjà Splunk.
 
## <a name="next-steps"></a>Étapes suivantes

- Accédez à la [documentation de référence complète du langage de requête Kusto](/azure/kusto/query/).

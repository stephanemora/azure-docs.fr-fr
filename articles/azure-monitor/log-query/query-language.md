---
title: Requêtes de journal Azure Monitor | Microsoft Docs
description: Références à des ressources permettant d’apprendre à écrire des requêtes de journal dans Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: f2c4939436db5ae4c862cb311cc66a162ac716e3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989244"
---
# <a name="azure-monitor-log-queries"></a>Requêtes de journal Azure Monitor
Les journaux Azure Monitor reposent sur Azure Data Explorer ; les requêtes de journal Azure Monitor utilisent une version du même langage de requête. La [documentation du langage de requête Azure Data Explorer](/azure/kusto/query), qui rassemble toutes les informations sur le langage, devra être votre principale ressource pour écrire des requêtes de journal Azure Monitor. Cette page comporte des liens vers d’autres ressources permettant d’apprendre à écrire des requêtes et de découvrir les différences avec l’implémentation Azure Monitor du langage.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Prise en main

- [Bien démarrer avec Log Analytics Azure Monitor](get-started-portal.md) est une leçon d’écriture de requêtes et d’utilisation des résultats sur le Portail Azure.
- [Bien démarrer avec les requêtes de journal Azure Monitor](get-started-queries.md) est une leçon d’écriture de requêtes à l’aide de données de journal Azure Monitor.

## <a name="concepts"></a>Concepts
- [Analyser les données de journal dans Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) donne une vue d’ensemble synthétique des requêtes de journal et décrit la structure des données de journal Azure Monitor.
- [Afficher et analyser les données de journal dans Azure Monitor](../../azure-monitor/log-query/portals.md) décrit les portails permettant de créer et d’exécuter des requêtes de journal.

## <a name="reference"></a>Informations de référence

- [Référence du langage de requête](/azure/kusto/query) est la référence complète du langage de requête Data Explorer.
- [Différences du langage de requête de journal Azure Monitor](data-explorer-difference.md) décrit les différences entre les versions du langage de requête Data Explorer.
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
-  [De Splunk à la requête de journal Azure Monitor](sql-cheatsheet.md) accompagne les utilisateurs qui connaissent déjà Splunk.
 
## <a name="next-steps"></a>Étapes suivantes

- Accédez à la [documentation de référence complète sur le langage de requête Data Explorer](/azure/kusto/query/).
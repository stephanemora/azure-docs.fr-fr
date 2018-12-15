---
title: Langage de requête Log Analytics Azure Monitor | Microsoft Docs
description: Références à des ressources permettant d’apprendre à écrire des requêtes dans Log Analytics.
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
ms.date: 10/29/2018
ms.author: bwren
ms.openlocfilehash: 32e64ce7772d562ea34a0d74afbd737be27d247d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968866"
---
# <a name="log-analytics-query-language"></a>Langage de requête Log Analytics
Log Analytics assure la collecte et l’analyse de journaux pour Azure Monitor. Il repose sur Azure Data Explorer et utilise une version du même langage de requête. La [documentation du langage de requête Azure Data Explorer](/azure/kusto/query), qui rassemble toutes les informations sur le langage, devra être votre principale ressource pour écrire des requêtes Log Analytics. Cette page comporte des liens vers d’autres ressources permettant d’apprendre à écrire des requêtes et de découvrir les différences avec l’implémentation Log Analytics du langage.

## <a name="getting-started"></a>Prise en main

- [Bien démarrer avec Log Analytics sur le Portail Azure](get-started-portal.md) est une leçon d’écriture de requêtes et d’utilisation des résultats sur le Portail Azure.
-  [Bien démarrer avec les requêtes dans Log Analytics](get-started-queries.md) est une leçon d’écriture de requêtes à l’aide de données Log Analytics.

## <a name="concepts"></a>Concepts
- [Analyser les données Log Analytics dans Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) donne une vue d’ensemble synthétique des requêtes de journal et décrit la structure des données Log Analytics.
- [Afficher et analyser les données dans Log Analytics](../../azure-monitor/log-query/portals.md) décrit les portails permettant de créer et d’exécuter des requêtes Log Analytics.

## <a name="reference"></a>Informations de référence

- [Référence du langage de requête](/azure/kusto/query) est la référence complète du langage de requête Data Explorer.
- [Différences du langage de requête Log Analytics](data-explorer-difference.md) décrit les différences entre les versions du langage de requête Data Explorer.
- [Propriétés standard dans les enregistrements Log Analytics](../../azure-monitor/platform/log-standard-properties.md) décrit les propriétés communes à toutes les données Log Analytics.
- [Effectuer des recherches inter-ressources dans les journaux dans Log Analytics](../../azure-monitor/log-query/cross-workspace-query.md) explique comment écrire des requêtes qui utilisent des données provenant de plusieurs espaces de travail Log Analytics et applications Application Insights.


## <a name="examples"></a>Exemples

- [Exemples de requêtes Log Analytics](examples.md) donne des exemples de requêtes utilisant des données Log Analytics.



## <a name="lessons"></a>Leçons

- [Utiliser les chaînes dans les requêtes Log Analytics](string-operations.md) explique comment utiliser des données de type chaîne.
- [Utiliser les valeurs Date/Heure dans les requêtes Log Analytics](datetime-operations.md) explique comment utiliser des données de type date et heure. 
- [Agrégations dans les requêtes Log Analytics](aggregations.md) et [Agrégations avancées dans les requêtes Log Analytics](advanced-aggregations.md) expliquent comment agréger et synthétiser les données.
- [Jointures dans les requêtes Log Analytics](joins.md) explique comment joindre des données provenant de plusieurs tables.
- [Utiliser JSON et les structures de données dans des requêtes Log Analytics](json-data-structures.md) explique comment analyser des données JSON.
- [Écrire des requêtes avancées dans Log Analytics](advanced-query-writing.md) décrit les stratégies de création de requêtes complexes et de réutilisation du code.
- [Créer des graphes et des diagrammes à partir de requêtes Log Analytics](charts.md) explique comment visualiser les données à partir d’une requête.

## <a name="cheatsheets"></a>Aide-mémoires

-  [Aide-mémoire du passage de SQL au langage de requête Log Analytics](sql-cheatsheet.md) accompagne les utilisateurs qui connaissent déjà SQL.
-  [Aide-mémoire du passage de Splunk au langage de requête Log Analytics](sql-cheatsheet.md) accompagne les utilisateurs qui connaissent déjà Splunk.
 
## <a name="next-steps"></a>Étapes suivantes

- Accédez à la [documentation de référence complète sur le langage de requête Data Explorer](/azure/kusto/query/).
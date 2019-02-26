---
title: Analytics, le puissant outil de recherche et de requête d’Azure Application Insights | Microsoft Docs
description: 'Présentation d’Analytics, le puissant outil de recherche d’Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268598"
---
# <a name="analytics-in-application-insights"></a>Analytics dans Application Insights
Analytics est le puissant outil de recherche et de requête d[’Application Insights](app-insights-overview.md). Analytics étant un outil web, aucune installation n’est nécessaire.
Si vous avez déjà configuré Application Insights pour l’une de vos applications, vous pouvez alors analyser les données de votre application en ouvrant Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application.

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/analytics/001.png)

Vous pouvez également utiliser l[’interface Analytics](https://go.microsoft.com/fwlink/?linkid=859557), un environnement de démonstration gratuit incluant de nombreux exemples de données.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Relation aux journaux Azure Monitor
L'analytique d'Application Insights repose sur [Azure Data Explorer](/azure/data-explorer), comme les journaux d'Azure Monitor, et utilise également le [langage de requête Kusto](/azure/kusto/query). Il utilise le même [portail Log Analytics](../log-query/get-started-portal.md) que les journaux d’Azure Monitor, même si ses données sont stockées dans une partition distincte.

Vous ne pouvez pas accéder directement aux données d’un espace de travail Log Analytics à partir d’analyses Application Insights. Vous ne pouvez pas non plus accéder directement à des données d’application à partir de Log Analytics. Pour interroger les deux jeux de données ensemble, écrivez un [requête dans Log Analytics](../log-query/log-query-overview.md) et utilisez l’expression [app()](../log-query/app-expression.md) pour accéder aux données d’application.


## <a name="query-data-in-analytics"></a>Interrogation de données dans Analytics
Une requête classique commence par un nom de table suivi d’une série *d’opérateurs* séparés par des `|`.
Par exemple, nous allons rechercher le nombre de demandes que notre application a reçu en provenance de différents pays, au cours des 3 dernières heures :
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Commençons par le nom de la table, *requests*, et ajoutons les éléments redirigés nécessaires.  Tout d’abord, nous définissons un filtre de temps pour consulter uniquement les enregistrements des 3 dernières heures.
Nous comptons ensuite le nombre d’enregistrements par pays (ces données se trouvent dans la colonne *client_CountryOrRegion*). Pour finir, nous affichons les résultats dans un graphique à secteurs.
<br>

![Résultats de la requête](./media/analytics/030.png)

Le langage possède de nombreuses fonctionnalités attrayantes :

* [Filtrer](/azure/kusto/query/whereoperator) vos données de télémétrie d’application brutes sur tous les champs, y compris les propriétés et métriques personnalisées.
* [Joindre](/azure/kusto/query/joinoperator) plusieurs tables : mettez en corrélation les demandes avec les affichages de page, les appels de dépendance, les exceptions et les suivis du journal.
* [Agrégations](/azure/kusto/query/summarizeoperator)statistiques puissantes.
* Visualisations immédiates et puissantes.
* Il existe une [API REST](https://dev.applicationinsights.io/) que vous pouvez utiliser pour exécuter des requêtes par programme, par exemple à partir de PowerShell.

La [référence complète du langage](https://go.microsoft.com/fwlink/?linkid=856079) détaille chaque commande prise en charge et se met à jour régulièrement.

## <a name="next-steps"></a>Étapes suivantes
* Prise en main du [portail Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Prise en main de l[’écriture de requêtes](https://go.microsoft.com/fwlink/?linkid=856078)
* Consultez l[’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics) pour obtenir la traduction des idiomes courants.
* Testez la version d’évaluation d’Analytics sur votre [interface](https://analytics.applicationinsights.io/demo) si votre application n’envoie pas encore de données à Application Insights.
* Regardez la [vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).

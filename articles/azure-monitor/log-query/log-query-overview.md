---
title: Vue d’ensemble des requêtes de journal dans Azure Monitor | Microsoft Docs
description: Repond aux questions fréquentes liées aux requêtes de journal et vous montre comment commencer à les utiliser.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 6cd2a28ddbe970385ff44deec2158c257937982e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82591650"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Vue d’ensemble des requêtes de journal dans Azure Monitor
Les requêtes de journal vous aident à tirer pleinement parti de la valeur des données collectées dans les [journaux Azure Monitor](../platform/data-platform-logs.md). Un puissant langage de requête vous permet de joindre des données provenant de plusieurs tables, d’agréger des jeux de données volumineux et d’effectuer des opérations complexes avec un minimum de code. Vous pouvez répondre à pratiquement n’importe quelle question et effectuer n’importe quelle analyse tant que les données de soutien ont été collectées et que vous comprenez comment construire la bonne requête.

Certaines fonctionnalités d’Azure Monitor telles que les [insights](../insights/insights-overview.md) et les [solutions](../insights/solutions-inventory.md) traitent les données de journal sans vous exposer à des requêtes sous-jacentes. Pour tirer pleinement parti des autres fonctionnalités d’Azure Monitor, vous devez comprendre comment les requêtes sont construites et comment vous pouvez les utiliser pour analyser les données dans les journaux d’Azure Monitor de manière interactive.

Utilisez cet article comme point de départ pour l’apprentissage des requêtes de journal dans Azure Monitor. Il répond aux questions courantes et fournit des liens vers d’autres documents offrant davantage de détails et de leçons.

## <a name="how-can-i-learn-how-to-write-queries"></a>Comment puis-je apprendre à écrire des requêtes ?
Si vous souhaitez vous lancer directement dans les choses, vous pouvez démarrer avec les didacticiels suivants :

- [Bien démarrer avec Log Analytics dans Azure Monitor](get-started-portal.md).
- [Bien démarrer avec les requêtes de journal dans Azure Monitor](get-started-queries.md).

Une fois que vous avez intégré les notions de base, passez en revue les leçons avec vos propres données ou celles de notre environnement de démonstration en commençant par : 

- [Utilisation de chaînes dans des requêtes de journal Azure Monitor](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Quel langage les requêtes de journal utilisent-elles ?
Azure Monitor Logs repose sur [Azure Data Explorer](/azure/data-explorer), et les requêtes de journal sont écrites à l’aide du même langage de requête Kusto (KQL). Il s’agit d’un langage riche conçu pour être facile à lire et écrire, et vous pourrez commencer à l’utiliser avec un minimum de conseils.

Consultez la [documentation de KQL pour Azure Data Explorer](/azure/kusto/query) pour obtenir une documentation complète sur KQL et des informations de référence sur les différentes fonctions disponibles.<br>
Consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](get-started-queries.md) pour un bref aperçu du langage avec des données des journaux d’Azure Monitor.
Consultez [Différences propres au langage de requête de journal d'Azure Monitor](data-explorer-difference.md) pour découvrir les différences mineures dans la version de KQL utilisée par Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Quelles sont les données disponibles pour les requêtes de journal ?
Toutes les données collectées dans Azure Monitor Logs sont disponibles pour récupération et analyse par les requêtes de journal. Différentes sources de données écriront leurs données dans différentes tables, mais vous pouvez inclure plusieurs tables dans une seule requête pour analyser les données entre plusieurs sources. Lorsque vous générez une requête, vous commencez par déterminer les tables comportant les données que vous recherchez ; vous devez donc avoir au moins une connaissance élémentaire de la structure des données dans Azure Monitor Logs.

Consultez [Sources d’Azure Monitor Logs](../platform/data-platform-logs.md#sources-of-azure-monitor-logs) pour une liste des différentes sources de données qui remplissent Azure Monitor Logs.<br>
Consultez [Structure d’Azure Monitor Logs](logs-structure.md) pour une explication de la façon dont les données sont structurées.

## <a name="what-does-a-log-query-look-like"></a>À quoi ressemble une requête de journal ?
Une requête peut être aussi simple qu’un seul nom de table pour récupérer tous les enregistrements de cette table :

```Kusto
Syslog
```

Ou vous pouvez filtrer des enregistrements particuliers, les synthétiser et visualiser les résultats dans un graphique :

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Pour une analyse plus complexe, vous pouvez récupérer des données provenant de plusieurs tables à l’aide d’une jointure pour analyser les résultats ensemble.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Même si vous n’êtes pas familiarisé avec KQL, vous devriez au moins pouvoir déterminer la logique de base utilisée par ces requêtes. Elles commencent par le nom d’une table, et vous y ajoutez plusieurs commandes pour filtrer et traiter ces données. Une requête peut utiliser n’importe quel nombre de commandes, et vous pourrez écrire des requêtes plus complexes après vous être familiarisé avec les différentes commandes KQL disponibles.

Consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](get-started-queries.md) pour obtenir un didacticiel sur les requêtes de journal qui présente le langage et ses fonctions communes.<br>


## <a name="what-is-log-analytics"></a>Présentation de Log Analytics
Log Analytics est le principal outil dans le portail Azure pour l’écriture de requêtes de journal et l’analyse interactive de leurs résultats. Même si une requête de journal est utilisée ailleurs dans Azure Monitor, vous allez généralement commencer par écrire et tester la requête dans Log Analytics.

Vous pouvez démarrer Log Analytics à partir de plusieurs endroits dans le portail Azure. L’étendue des données disponibles pour Log Analytics est déterminée par la façon dont vous le démarrez. Consultez [Étendue de requête](scope.md) pour plus d’informations.

- Sélectionnez **Journaux** dans le menu **Azure Monitor** ou **Espaces de travail Log Analytics**.
- Sélectionnez **Journaux** à partir de la page **Vue d’ensemble** d’une application Application Insights.
- Sélectionnez **Journaux** dans le menu d’une ressource Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Consultez [Bien démarrer avec Log Analytics dans Azure Monitor](get-started-portal.md) pour un didacticiel pas à pas de Log Analytics qui présente plusieurs de ses fonctionnalités.

## <a name="where-else-are-log-queries-used"></a>Où les requêtes de journal sont-elles utilisées ?
En plus de travailler de manière interactive avec les requêtes de journal et leurs résultats dans Log Analytics, les zones dans Azure Monitor où vous allez utiliser les requêtes sont les suivantes :

- **Règles d’alerte.** Les [règles d’alerte](../platform/alerts-overview.md) identifient de façon proactive les problèmes à partir des données dans votre espace de travail.  Chaque règle d’alerte est basée sur une recherche dans les journaux qui est exécutée automatiquement à intervalles réguliers.  Les résultats sont inspectés pour déterminer si une alerte doit être créée.
- **Tableaux de bord.** Vous pouvez épingler les résultats de n’importe quelle requête dans un [tableau de bord Azure](../learn/tutorial-logs-dashboards.md) afin de visualiser les données de journal et les métriques ensemble et de partager ces informations avec d’autres utilisateurs Azure si vous le souhaitez.
- **Vues.**  Vous pouvez créer des visualisations de données à inclure dans les tableaux de bord utilisateur avec le [Concepteur de vues](../platform/view-designer.md).  Les requêtes dans les journaux fournissent les données utilisées par les [vignettes](../platform/view-designer-tiles.md) et les [composants de visualisation](../platform/view-designer-parts.md) dans chaque vue.  
- **Exportation.**  Lorsque vous importez des données de journal d'Azure Monitor vers Excel ou [Power BI](../platform/powerbi.md), vous créez une requête de journal pour définir les données à exporter.
- **PowerShell.** Vous pouvez exécuter un script PowerShell à partir d’une ligne de commande ou d’un runbook Azure Automation qui utilise [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) pour récupérer des données de journal à partir d’Azure Monitor.  Cette applet de commande nécessite une requête pour déterminer les données à récupérer.
- **API Journaux d’activité Azure Monitor.**  L’[API Journaux d’activité Azure Monitor](https://dev.loganalytics.io) permet à tout client d’API REST de récupérer des données de journal d’activité à partir de l’espace de travail.  La demande API comprend une requête qui est exécutée sur Azure Monitor pour déterminer les données à récupérer.


## <a name="next-steps"></a>Étapes suivantes
- Suivez un [didacticiel sur l’utilisation de Log Analytics dans le portail Azure](get-started-portal.md).
- Suive un [didacticiel sur l’écriture de requêtes](get-started-queries.md).

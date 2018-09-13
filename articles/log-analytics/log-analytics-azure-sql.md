---
title: Solution Azure SQL Analytics dans Log Analytics | Microsoft Docs
description: La solution Azure SQL Analytics vous permet de gérer vos instances Azure SQL Database.
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: 82845f475857f9a911febd496e86eb2a60f69c25
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782224"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Surveiller les instances Azure SQL Database avec Azure SQL Analytics (préversion)

![Symbole Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics est une solution cloud pour superviser à grande échelle les performances des bases de données SQL Azure, des pools élastiques et des instances gérées dans plusieurs abonnements. Cette solution collecte et visualise des métriques importantes de performances Azure SQL Database avec une intelligence intégrée pour résoudre les problèmes.

En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes de surveillance personnalisées. La solution vous aide à identifier les problèmes sur chacune des couches de votre pile applicative. Elle utilise des métriques de diagnostic Azure avec des vues Log Analytics pour présenter les données de l’ensemble de vos bases de données SQL Azure, de vos pools élastiques et de vos bases de données dans des instances gérées dans un espace de travail Log Analytics unique. Log Analytics prend en charge la collecte, la mise en correspondance et l’affichage des données structurées et non structurées.

Actuellement, cette solution en préversion prend en charge jusqu’à 200 000 bases de données SQL Azure et 5 000 pools élastiques SQL par espace de travail.

Pour une présentation pratique sur l'utilisation de la solution Azure SQL Analytics et des scénarios d'utilisation standard, regardez la vidéo incorporée :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Sources connectées

Azure SQL Analytics est une solution de supervision uniquement dans le cloud qui prend en charge le streaming de données de télémétrie de diagnostic pour les bases de données SQL Azure, les pools élastiques et les instances gérées. Comme elle n’utilise pas d’agent pour se connecter au service Log Analytics, la solution ne prend pas en charge la supervision des serveurs ou machines virtuelles SQL. Consultez le tableau de compatibilité ci-dessous.

| Source connectée | Pris en charge | Description |
| --- | --- | --- |
| **[Azure Diagnostics](log-analytics-azure-storage.md)** | **Oui** | Les métriques Azure et les données des journaux sont envoyées à Log Analytics directement par Azure. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | Log Analytics ne lit pas les données d’un compte de stockage. |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | Les agents directs Windows ne sont pas utilisés par la solution. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | Les agents directs Linux ne sont pas utilisés par la solution. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne valorise aucune connexion directe entre l’agent SCOM et Log Analytics. |

## <a name="configuration"></a>Configuration

Exécutez la procédure suivante afin d’ajouter la solution Azure SQL Analytics dans votre espace de travail.

1. Ajoutez la solution Azure SQL Analytics à votre espace de travail à partir de la [marketplace Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Dans le portail Azure, cliquez sur **+ Créer une ressource**, puis recherchez **Azure SQL Analytics**.  
    ![Surveillance et gestion](./media/log-analytics-azure-sql/monitoring-management.png)
3. Sélectionnez **Azure SQL Analytics (préversion)** dans la liste.
4. Dans la zone **Azure SQL Analytics (version préliminaire)**, cliquez sur **Créer**.  
    ![Créer](./media/log-analytics-azure-sql/portal-create.png)
5. Dans la zone **Créer une nouvelle solution**, créez une solution ou sélectionnez un espace de travail existant auquel vous souhaitez ajouter la solution, puis cliquez sur **Créer**.  
    ![ajouter à l’espace de travail](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Configurer des instances Azure SQL Database et des pools élastiques pour diffuser en continu des données de télémétrie de diagnostic

Une fois que vous avez créé la solution Azure SQL Analytics dans votre espace de travail, afin de surveiller les performances des instances Azure SQL Database et/ou des pools élastiques, vous devez **configurer chaque** ressource Azure SQL Database et de pool élastique à surveiller pour diffuser ses données de télémétrie de diagnostic vers la solution.

- Activez les diagnostics Azure pour vos instances Azure SQL Database et vos pools élastiques et [configurez-les pour l’envoi de leurs données vers Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Pour configurer plusieurs abonnements Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell). Fournissez l’ID de ressource d’espace de travail en tant que paramètre lors de l’exécution du script pour transmettre les données des ressources d’un abonnement Azure vers un espace de travail d’un autre abonnement Azure.

**Exemple**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Utilisation de la solution

Lorsque vous ajoutez la solution à votre espace de travail, la vignette Azure SQL Analytics est également ajoutée ; elle apparaît dans la Vue d’ensemble. La vignette indique le nombre d’instances Azure SQL Database et de pools élastiques Azure SQL auxquels la solution est associée.

![Vignette Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Affichage des données Azure SQL Analytics

Cliquez sur la vignette **Azure SQL Analytics** afin d’ouvrir le tableau de bord Azure SQL Analytics. Ce tableau de bord propose un aperçu de toutes les bases de données surveillées selon différentes perspectives. Pour que ces différentes perspectives fonctionnent, vous devez activer les métriques ou les journaux appropriés sur les ressources SQL, celles dont la diffusion doit s’effectuer en continu sur l’espace de travail Azure Log Analytics.

![Vue d’ensemble d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

En sélectionnant l’une des vignettes, vous ouvrez le rapport détaillé d’une perspective en particulier. Une fois la perspective sélectionnée, le rapport détaillé est ouvert.

![Délais d’expiration d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Chaque perspective fournit des récapitulatifs sur l’abonnement, le serveur, le pool de bases de données élastique et la base de données. En outre, sur la droite, chaque perspective présente une vision spécifique au rapport. Pour approfondir davantage, sélectionnez l’abonnement, le serveur, le pool ou la base de données dans la liste.

| Perspective | Description |
| --- | --- |
| Ressource par type | Perspective comptabilisant toutes les ressources surveillées. Ce zoom fournit un récapitulatif des métriques de DTU et Go. |
| Insights | Permet une exploration hiérarchique dans Intelligent Insights. Apprenez-en davantage sur l’intelligence artificielle intégrée. |
| Errors | Permet une exploration hiérarchique des erreurs SQL qui se sont produites dans les bases de données. |
| Délais d’expiration | Permet une exploration hiérarchique des délais d’expiration SQL qui se sont produits dans les bases de données. |
| Blocages | Permet une exploration hiérarchique des blocages SQL qui se sont produits dans les bases de données. |
| Attentes de la base de données | Permet une exploration hiérarchique des statistiques d’attente SQL au niveau des bases de données. Inclut des résumés du temps d’attente total et de la durée d’attente par type d’attente. |
| Durée de la requête | Permet une exploration hiérarchique des statistiques d’exécution de la requête, par exemple la durée de la requête, l’utilisation du processeur, l’utilisation des E/S de données, l’utilisation des E/S du journal. |
| Attentes de requête | Permet une exploration hiérarchique des statistiques d’attente de requête par catégorie d’attente. |

### <a name="intelligent-insights-report"></a>Rapport Intelligent Insights

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vous permet de suivre les performances de votre base de données. Toutes les analyses Intelligent Insights collectées peut être visualisées et sont accessibles par le biais de la perspective Insights.

![Insights Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rapports de base de données et de pool de bases de données élastique

Les pools élastiques et les bases de données ont leurs propres rapports qui affichent toutes les données collectées pour la ressource, dans le temps imparti.

![Base de données Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool de base de données élastique Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Rapports de requête

En vous aidant de la durée de la requête et des attentes de requête, vous pouvez mettre en corrélation les performances des requêtes dans le rapport de requête. Ce rapport compare les performances des requêtes entre les différentes bases de données. Il identifie également les bases de données qui effectuent la requête sélectionnée correctement et celles qui le font lentement.

![Requêtes Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analyser les données et créer des alertes

Vous pouvez facilement [créer des alertes](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) avec les données provenant de ressources Azure SQL Database. Voici quelques requêtes utiles de [recherche dans les journaux](log-analytics-log-searches.md) que vous pouvez utiliser pour créer des alertes :

*UC élevée sur Azure SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - La condition préalable à la configuration de cette alerte est que les bases de données surveillées transmettent en continu les mesures de diagnostic (option « Toutes les mesures ») à la solution.
> - Remplacez la valeur MetricName cpu_percent par dtu_consumption_percent pour obtenir des résultats de DTU élevés à la place.

*UC élevée sur des pools élastiques SQL Database*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - La condition préalable à la configuration de cette alerte est que les bases de données surveillées transmettent en continu les mesures de diagnostic (option « Toutes les mesures ») à la solution.
> - Remplacez la valeur MetricName cpu_percent par dtu_consumption_percent pour obtenir des résultats de DTU élevés à la place.

*Stockage Azure SQL Database supérieur à 95 % en moyenne dans la dernière heure*

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - La condition préalable à la configuration de cette alerte est que les bases de données surveillées transmettent en continu les mesures de diagnostic (option « Toutes les mesures ») à la solution.
> - Cette requête nécessite une règle d’alerte pour être configurée de manière à déclencher une alerte lorsqu’il y a des résultats (résultats > 0) à la requête, indiquant que la condition existe sur certaines bases de données. La sortie est une liste de ressources de base de données qui se situent au-dessus de storage_threshold dans le time_range défini.
> - La sortie est une liste de ressources de base de données qui se situent au-dessus de storage_threshold dans le time_range défini.

*Alerte sur Intelligent Insights*

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - La condition préalable à la configuration de cette alerte est que les bases de données surveillées transmettent en continu le journal de diagnostic SQLInsights à la solution.
> - Cette requête nécessite la configuration d’une règle d’alerte pour une exécution à la même fréquence qu’alert_run_interval afin d’éviter des résultats en double. La règle doit être configurée pour déclencher l’alerte lorsqu’il y a des résultats (résultats > 0) à la requête.
> - Personnalisez l’alert_run_interval afin de spécifier l’intervalle de temps pour vérifier si la condition est remplie sur les bases de données configurées pour transmettre en continu le journal à la solution.
> - Personnalisez l’insights_string pour capturer la sortie du texte d’analyse de cause racine Insights. Ce texte est identique à celui affiché dans l’interface utilisateur de la solution que vous pouvez utiliser à partir des insights existants. Vous pouvez également utiliser la requête ci-dessous pour voir le texte de tous les éléments Insights générés sur votre abonnement. Utilisez la sortie de la requête pour collecter les chaînes distinctes et configurer des alertes sur Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active" 
| distinct rootCauseAnalysis_s
```

## <a name="next-steps"></a>Étapes suivantes

- Utilisez les [Recherches de journal](log-analytics-log-searches.md) dans Log Analytics pour afficher les données détaillées sur Azure SQL.
- [Créer un tableau de bord personnalisé](log-analytics-dashboards.md) comportant les données Azure SQL.
- [Create and manage alert rules in Log Analytics with the OMS portal](log-analytics-alerts.md) (Créer et gérer des règles d’alerte dans Log Analytics dans Log Analytics à l’aide du portail OMS).

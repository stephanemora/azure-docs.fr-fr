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
ms.component: ''
ms.openlocfilehash: d16f9add2cd31eb5a8db650798c241c3dcf2610f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379302"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Superviser Azure SQL Database avec Azure SQL Analytics (préversion)

![Symbole Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure SQL Analytics est une solution cloud permettant de superviser à grande échelle les performances des bases de données SQL Azure, des pools élastiques et des bases de données Managed Instance dans plusieurs abonnements. Cette solution collecte et visualise des métriques importantes de performances Azure SQL Database avec une intelligence intégrée pour résoudre les problèmes.

En vous appuyant sur les mesures collectées avec la solution, vous êtes en mesure de créer des règles et des alertes de surveillance personnalisées. La solution vous aide à identifier les problèmes sur chacune des couches de votre pile applicative. Elle utilise des métriques de diagnostic Azure avec des vues Log Analytics pour présenter les données de l’ensemble de vos bases de données SQL Azure, de vos pools élastiques et de vos bases de données dans des instances gérées dans un espace de travail Log Analytics unique. Log Analytics prend en charge la collecte, la mise en correspondance et l’affichage des données structurées et non structurées.

Pour une présentation pratique sur l'utilisation de la solution Azure SQL Analytics et des scénarios d'utilisation standard, regardez la vidéo incorporée :


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Sources connectées

Azure SQL Analytics est une solution de supervision uniquement dans le cloud qui prend en charge la diffusion en continu de données de télémétrie de diagnostic pour Azure SQL Database, les bases de données Managed Instance et les pools élastiques.

Étant donné que cette solution n’utilise pas d’agents pour se connecter au service Log Analytics, elle ne prend pas en charge la supervision d’un serveur SQL Server hébergé localement ou dans des machines virtuelles. Consultez le tableau de compatibilité ci-dessous.

| Source connectée | Pris en charge | Description |
| --- | --- | --- |
| **[Azure Diagnostics](log-analytics-azure-storage.md)** | **Oui** | Les métriques Azure et les données des journaux sont envoyées à Log Analytics directement par Azure. |
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | Log Analytics ne lit pas les données d’un compte de stockage. |
| [Agents Windows](log-analytics-windows-agent.md) | Non  | Les agents directs Windows ne sont pas utilisés par la solution. |
| [Agents Linux](log-analytics-linux-agents.md) | Non  | Les agents directs Linux ne sont pas utilisés par la solution. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Non  | La solution ne valorise aucune connexion directe entre l’agent SCOM et Log Analytics. |

## <a name="configuration"></a>Configuration

Exécutez la procédure ci-après pour ajouter la solution Azure SQL Analytics à votre tableau de bord Azure.

1. Ajoutez la solution Azure SQL Analytics à votre espace de travail à partir de la [marketplace Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Dans le portail Azure, cliquez sur **+ Créer une ressource**, puis recherchez **Azure SQL Analytics**.  
    ![Surveillance et gestion](./media/log-analytics-azure-sql/monitoring-management.png)
3. Sélectionnez **Azure SQL Analytics (préversion)** dans la liste.
4. Dans la zone **Azure SQL Analytics (version préliminaire)**, cliquez sur **Créer**.  
    ![Créer](./media/log-analytics-azure-sql/portal-create.png)
5. Dans la zone **Créer une nouvelle solution**, créez une solution ou sélectionnez un espace de travail existant auquel vous souhaitez ajouter la solution, puis cliquez sur **Créer**.

    ![Ajout à l’espace de travail](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurer des instances Azure SQL Database, des pools élastiques et des instances Managed Instance pour la diffusion en continu des données de télémétrie de diagnostic

Une fois que vous avez créé la solution Azure SQL Analytics dans votre espace de travail, afin de superviser les performances des bases de données SQL Azure, des bases de données Managed Instance et des pools élastiques, vous devez **configurer chacune** des ressources à superviser pour la diffusion en continu de leurs données de télémétrie de diagnostic vers la solution.

- Activez Azure Diagnostics pour vos instances Azure SQL Database, vos bases de données Managed Instance et vos pools élastiques afin de [diffuser en continu les données de télémétrie de diagnostic vers Azure SQL Analytics](../sql-database/sql-database-metrics-diag-logging.md).

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

Lorsque vous ajoutez la solution à votre espace de travail, la vignette Azure SQL Analytics est également ajoutée ; elle apparaît dans la Vue d’ensemble. La vignette indique le nombre de bases de données SQL Azure, de pools élastiques, d’instances Managed Instance et de bases de données dans ces instances en provenance desquelles la solution reçoit des données de télémétrie de diagnostic.

![Vignette Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

La solution offre deux vues distinctes : l’une pour la supervision des instances Azure SQL Database et des pools élastiques, et l’autre pour la supervision des instances Managed Instance et de leurs bases de données.

Pour visualiser le tableau de bord de supervision Azure SQL Analytics pour les instances Azure SQL Database et les pools élastiques, cliquez sur la partie supérieure de la vignette. Pour visualiser le tableau de bord de supervision Azure SQL Analytics pour les instances Managed Instance et leurs bases de données, cliquez sur la partie inférieure de la vignette.

### <a name="viewing-azure-sql-analytics-data"></a>Affichage des données Azure SQL Analytics

Ce tableau de bord propose un aperçu de toutes les bases de données surveillées selon différentes perspectives. Pour que ces différentes perspectives fonctionnent, vous devez activer les métriques ou les journaux appropriés sur les ressources SQL, celles dont la diffusion doit s’effectuer en continu sur l’espace de travail Azure Log Analytics.

Notez que si certains métriques ou journaux ne sont pas diffusés en continu dans Azure Log Analytics, les informations de supervision n’apparaissent pas dans les vignettes de la solution.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Vue des instances Azure SQL Database et des pools élastiques

Lorsque la vignette Azure SQL Analytics pour les instances Azure SQL Database et les pools élastiques est sélectionnée, le tableau de bord de supervision s’affiche.

![Vue d’ensemble d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

En sélectionnant l’une des vignettes, vous ouvrez le rapport détaillé d’une perspective en particulier. Une fois la perspective sélectionnée, le rapport détaillé est ouvert.

![Délais d’expiration d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Chaque perspective de cette vue fournit des récapitulatifs au niveau abonnement, serveur, pool élastique et base de données. En outre, sur la droite, chaque perspective présente une vision spécifique au rapport. Pour approfondir davantage, sélectionnez l’abonnement, le serveur, le pool ou la base de données dans la liste.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Vue des instances Managed Instance et de leurs bases de données

Lorsque la vignette Azure SQL Analytics pour les instances Managed Instance et leurs bases de données est sélectionnée, le tableau de bord de supervision s’affiche.

![Vue d’ensemble d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview-mi.png)

En sélectionnant l’une des vignettes, vous ouvrez le rapport détaillé d’une perspective en particulier. Une fois la perspective sélectionnée, le rapport détaillé est ouvert.

La sélection de la vue des instances Managed Instance présente les détails concernant l’utilisation des instances Managed Instance, les bases de données qu’elles contiennent et les données de télémétrie relatives aux requêtes exécutées sur les instances.

![Délais d’expiration d’Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspectives

Le tableau ci-après présente les perspectives prises en charge pour les deux versions du tableau de bord, l’une pour les bases de données SQL Azure et les pools élastiques, et l’autre pour les instances Managed Instance.

| Perspective | Description | Prise en charge pour les instances SQL Database et les pools élastiques | Prise en charge pour les instances Managed Instance |
| --- | ------- | ----- | ----- |
| Ressource par type | Perspective comptabilisant toutes les ressources surveillées. | Oui | Oui | 
| Insights | Permet une exploration hiérarchique des performances dans Intelligent Insights. | Oui | Oui |
| Errors | Permet une exploration hiérarchique des erreurs SQL qui se sont produites dans les bases de données. | Oui | Oui |
| Délais d’expiration | Permet une exploration hiérarchique des délais d’expiration SQL qui se sont produits dans les bases de données. | Oui | Non  |
| Blocages | Permet une exploration hiérarchique des blocages SQL qui se sont produits dans les bases de données. | Oui | Non  |
| Attentes de la base de données | Permet une exploration hiérarchique des statistiques d’attente SQL au niveau des bases de données. Inclut des résumés du temps d’attente total et de la durée d’attente par type d’attente. |Oui | Oui |
| Durée de la requête | Permet une exploration hiérarchique des statistiques d’exécution de la requête, par exemple la durée de la requête, l’utilisation du processeur, l’utilisation des E/S de données, l’utilisation des E/S du journal. | Oui | Oui |
| Attentes de requête | Permet une exploration hiérarchique des statistiques d’attente de requête par catégorie d’attente. | Oui | Oui |

### <a name="intelligent-insights-report"></a>Rapport Intelligent Insights

Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) vous permet de suivre les performances des bases de données Azure SQL Database et Managed Instance. Toutes les analyses Intelligent Insights collectées peut être visualisées et sont accessibles par le biais de la perspective Insights.

![Insights Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rapports de base de données et de pool élastique

Les pools élastiques et les bases de données SQL disposent de leurs propres rapports qui affichent toutes les données collectées pour la ressource dans le temps imparti.

![Base de données Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool élastique SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Rapports de requête

En vous aidant de la durée de la requête et des attentes de requête, vous pouvez mettre en corrélation les performances des requêtes dans le rapport de requête. Ce rapport compare les performances des requêtes entre les différentes bases de données. Il identifie également les bases de données qui effectuent la requête sélectionnée correctement et celles qui le font lentement.

![Requêtes Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="pricing"></a>Tarifs

La solution est gratuite, mais la consommation de la télémétrie de diagnostics qui dépasse les unités gratuites d’ingestion de données allouées chaque mois est facturée. Consultez [Tarifs de Log Analytics](https://azure.microsoft.com/en-us/pricing/details/monitor). Les unités gratuites d’ingestion de données fournies permettent de superviser gratuitement plusieurs bases de données chaque mois. Notez que les bases de données plus actives avec des charges de travail plus lourdes ingèrent plus de données que les bases de données inactives. Vous pouvez facilement suivre votre consommation d’ingestion de données dans la solution en sélectionnant l’espace de travail OMS dans le menu de navigation d’Azure SQL Analytics, puis en sélectionnant Utilisation et estimation des coûts.

### <a name="analyze-data-and-create-alerts"></a>Analyser les données et créer des alertes

### <a name="creating-alerts-for-azure-sql-database"></a>Création d’alertes pour Azure SQL Database

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

*UC élevée sur des pools élastiques Azure SQL Database*

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

### <a name="creating-alerts-for-managed-instance"></a>Création d’alertes pour Managed Instance

*Stockage Managed Instance supérieur à 90 %

```
let storage_percentage_treshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_treshold
```

> [!NOTE]
> - La condition préalable à la configuration de cette alerte est que la diffusion en continu du journal ResourceUsageStats vers la solution soit activée pour l’instance Managed Instance supervisée.
> - Cette requête nécessite qu’une règle d’alerte soit configurée de manière à déclencher une alerte lorsqu’il existe des résultats (résultats > 0) pour la requête, indiquant que la condition existe sur l’instance Managed Instance. La sortie correspond au pourcentage de consommation du stockage sur l’instance Managed Instance.

## <a name="next-steps"></a>Étapes suivantes

- Utilisez les [Recherches dans les journaux](log-analytics-log-searches.md) dans Log Analytics pour afficher les données SQL Azure détaillées.
- [Créer un tableau de bord personnalisé](log-analytics-dashboards.md) comportant les données Azure SQL.
- [Créer des alertes](log-analytics-alerts.md) quand des événements SQL Azure spécifiques se produisent.

---
title: Superviser une charge de travail dans le portail Azure | Microsoft Docs
description: Superviser Azure SQL Data Warehouse à l’aide du portail Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748905"
---
# <a name="monitor-workload---azure-portal"></a>Superviser une charge de travail dans le portail Azure

Cet article explique comment utiliser le portail Azure pour superviser votre charge de travail. Il s’intéresse à la configuration des journaux Azure Monitor pour examiner les tendances de l’exécution des requêtes et de la charge de travail à l’aide de l’analytique des journaux pour [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Azure SQL Data Warehouse : Nous allons collecter des journaux pour un entrepôt de données SQL. Si vous n’avez pas d’entrepôt de données SQL provisionné, consultez les instructions dans [Créer un entrepôt de données SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Accéder au panneau de navigation des espaces de travail Log Analytics et créer un espace de travail 

![Espaces de travail Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Ajouter un espace de travail Log Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Ajouter un espace de travail Log Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Pour plus d’informations sur les espaces de travail, consultez cette [documentation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Activer les journaux de diagnostic 

Configurez les paramètres de diagnostic pour émettre des journaux à partir de votre entrepôt de données SQL. Les journaux se composent de vues de télémétrie de votre entrepôt de données équivalentes aux vues de gestion dynamique (DMV) de résolution des problèmes de performance les plus fréquentes pour SQL Data Warehouse. À l’heure actuelle, les vues suivantes sont prises en charge :

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Activation des journaux de diagnostic](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Les journaux peuvent être émis dans Stockage Azure, Stream Analytics ou Log Analytics. Pour ce tutoriel, sélectionnez Log Analytics.

![Spécifier des journaux](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Exécuter des requêtes sur Log Analytics

Accédez à votre espace de travail Log Analytics pour effectuer les étapes suivantes :

- Analyser les journaux à l’aide de requêtes et enregistrer les requêtes pour les réutiliser
- Enregistrer des requêtes pour les réutiliser
- Créer des alertes de journal
- Épingler des résultats de requête sur un tableau de bord

Pour plus d’informations sur les fonctionnalités des requêtes de journal, consultez cette [documentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Éditeur d’espace de travail Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Requêtes d’espace de travail Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exemples de requêtes de journal



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez installé et configuré des journaux Azure monitor, [personnalisez les tableaux de bord Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) pour les partager avec votre équipe.

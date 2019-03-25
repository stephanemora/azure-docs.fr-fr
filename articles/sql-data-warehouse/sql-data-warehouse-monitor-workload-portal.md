---
title: Charge de travail analyse - portail Azure | Microsoft Docs
description: Surveiller Azure SQL Data Warehouse à l’aide du portail Azure
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
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405781"
---
# <a name="monitor-workload---azure-portal"></a>Charge de travail analyse - portail Azure

Cet article décrit comment utiliser le portail Azure pour surveiller votre charge de travail. Cela inclut la configuration des journaux d’Azure Monitor pour examiner les tendances de demande d’exécution et de la charge de travail à l’aide d’analytique de journal pour [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Conditions préalables

- Abonnement Azure : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Azure SQL Data Warehouse : Nous collecte des journaux pour un entrepôt de données SQL. Si vous n’avez pas configuré un entrepôt de données SQL, consultez les instructions dans [créer un entrepôt de données SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Créer un espace de travail Log Analytics

Accédez au panneau de navigation pour les espaces de travail Analytique de journal et créer un espace de travail 

![Espaces de travail Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Ajouter l’espace de travail Analytique](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Ajouter l’espace de travail Analytique](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Pour plus d’informations sur les espaces de travail, visitez le [documentation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Activer les journaux de Diagnostic 

Configurer les paramètres de diagnostic pour émettre des journaux à partir de votre entrepôt de données SQL. Journaux se composent des vues de données de télémétrie de votre entrepôt de données équivalente à la plus couramment utilisés problèmes de performances vues de gestion dynamique de SQL Data Warehouse. Actuellement, les vues suivantes sont prises en charge :

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Activation des journaux de diagnostic](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Journaux peuvent être émis vers le stockage Azure, Stream Analytique ou Analytique de journal. Pour ce didacticiel, sélectionnez Analytique de journal.

![Spécifier les journaux](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Exécuter des requêtes sur l’Analytique de journal

Accédez à votre espace de travail Analytique de journal dans lequel vous pouvez procédez comme suit :

- Analyser les journaux à l’aide de requêtes de journal et enregistrer des requêtes pour une réutilisation
- Enregistrer des requêtes pour une réutilisation
- Créer des alertes de journal
- Résultats de la requête épingler au tableau de bord

Pour plus d’informations sur les fonctionnalités de requêtes de journal, visitez le [documentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Éditeur d’espace de travail Analytique de journal](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Enregistrer des requêtes d’espace de travail Analytique](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

Maintenant que vous avez configuré et configuré des journaux Azure monitor, [personnaliser les tableaux de bord Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) partager au sein de votre équipe.

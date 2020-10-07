---
title: Emplacements des données de supervision dans Azure Monitor | Microsoft Docs
description: Décrit les différents emplacements où les données de supervision sont stockées dans Azure, notamment la plateforme de données Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: bb7e92a676115d784bd19714178b3bd442798e26
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607236"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Emplacements des données de supervision dans Azure Monitor

Azure Monitor s’appuie sur une [plateforme de données](data-platform.md) de [journaux](data-platform-logs.md) et [métriques](data-platform-metrics.md), comme cela est décrit dans [Plateforme de données Azure Monitor](data-platform.md). Toutefois, les données de supervision provenant des ressources Azure peuvent être écrites dans d’autres emplacements, avant qu’elles soient copiées dans Azure Monitor ou pour prendre en charge des scénarios supplémentaires. 

## <a name="monitoring-data-locations"></a>Emplacements des données de supervision

Le tableau suivant identifie les différents emplacements où les données de supervsion dans Azure sont envoyées et les différentes méthodes pour y accéder.

| Emplacement | Description | Méthodes d’accès |
|:---|:---|:---|:--|
| Métriques Azure Monitor | Base de données de série chronologique qui est optimisée pour l’analyse des données horodatées. | [Metrics Explorer](metrics-getting-started.md)<br>[API de métriques Azure Monitor](/rest/api/monitor/metrics) |
| Journaux Azure Monitor    | Espace de travail Log Analytics basé sur Azure Data Explorer, qui fournit un puissant moteur d’analyse et un riche langage de requête. | [Log Analytics](../log-query/log-query-overview.md)<br>[API Log Analytics](https://dev.loganalytics.io/)<br>[API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Journal d’activité | Les données du journal d’activité sont particulièrement utiles quand elles sont envoyées aux journaux Azure Monitor pour être analysées avec d’autres données, mais elles sont également collectées par elles-mêmes et peuvent être consultées directement dans le portail Azure. | [Azure portal](./activity-log.md#view-the-activity-log)<br>[API Événements Azure Monitor](/rest/api/monitor/eventcategories) |
| Stockage Azure | Certaines sources de données écrivent directement dans le stockage Azure et nécessitent une configuration pour déplacer les données dans les journaux. Vous pouvez également envoyer des données vers le stockage Azure pour leur archivage et leur intégration avec des systèmes externes.  | [Analyse du stockage](/rest/api/storageservices/storage-analytics)<br>[Explorateur de serveurs](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorateur Stockage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) |
| Event Hubs | Envoyez des données vers Azure Event Hubs pour les diffuser en streaming vers d’autres emplacements. | [Capture pour le stockage](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor pour machines virtuelles | Azure Monitor pour machines virtuelles stocke les données d’intégrité de la charge de travail dans un emplacement personnalisé qui est utilisé par son expérience de supervision dans le portail Azure. | [Azure portal](../insights/vminsights-overview.md)<br>[API REST de supervision de charge de travail](/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API REST Azure Resource Health](/rest/api/resourcehealth/)  |
| Alertes | Alertes créées par Azure Monitor. | [Azure portal](alerts-managing-alert-instances.md)<br>[API REST de gestion d’alertes](/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Étapes suivantes

- Consultez les différentes sources de [supervision des données collectées par Azure Monitor](data-sources.md).
- Découvrez les [types de données de supervision collectées par Azure Monitor](data-platform.md) et la manière de consulter et d’analyser ces données.

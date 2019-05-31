---
title: Surveillance des emplacements de données dans Azure Monitor | Microsoft Docs
description: Décrit les différents emplacements où les données d’analyse sont stockée dans Azure, y compris la plate-forme de données Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416914"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Surveillance des emplacements de données dans Azure Monitor

Azure Monitor est basée sur un [plate-forme de données](data-platform.md) de [journaux](data-platform-logs.md) et [métriques](data-platform-metrics.md) comme décrit dans [plateforme de données Azure Monitor](data-platform.md). Analyse des données de ressources Azure de peut-être être écrites dans d’autres emplacements, avant qu’ils sont copiés dans Azure Monitor ou pour prendre en charge des scénarios supplémentaires. 

## <a name="monitoring-data-locations"></a>Surveillance des emplacements de données

Le tableau suivant identifie les différents emplacements où les données d’analyse dans Azure sont envoyées et les différentes méthodes pour y accéder.

| Lieu | Description | Méthodes d’accès |
|:---|:---|:---|:--|
| Métriques Azure Monitor | Base de données de série chronologique qui est optimisé pour l’analyse des données horodatées. | [Metrics Explorer](metrics-getting-started.md)<br>[API des métriques Azure Monitor](/rest/api/monitor/metrics) |
| Journaux Azure Monitor    | Ouvrez une session espace de travail Analytique qui repose sur l’Explorateur de données Azure qui fournit un langage de requête de moteur et riche de puissantes analyses. | [Log Analytics](../log-query/portals.md)<br>[API d’Analytique de journal](https://dev.loganalytics.io/)<br>[API application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Journal d’activité | Données du journal d’activité sont particulièrement utiles quand envoyées à des journaux Azure Monitor pour les analyser avec d’autres données, mais il est également collecté sur son propre donc elle peut être affichée directement dans le portail Azure. | [Portail Azure](activity-log-view.md#azure-portal)<br>[API Événements Azure Monitor](/rest/api/monitor/eventcategories) |
| Stockage Azure | Certaines sources de données écrit directement dans le stockage Azure et nécessitent une configuration pour déplacer des données dans les journaux. Vous pouvez également envoyer des données vers le stockage Azure pour l’archivage et l’intégration avec les systèmes externes.  | [Analyse du stockage](/rest/api/storageservices/storage-analytics)<br>[Explorateur de serveurs](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Explorateur Stockage](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Envoyer des données vers Azure Event Hubs pour diffuser en continu vers d’autres emplacements. | [Capture pour le stockage](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor pour machines virtuelles | Azure Monitor pour les machines virtuelles stocke les données de contrôle d’intégrité de la charge de travail dans un emplacement personnalisé qui est utilisé par son expérience de surveillance dans le portail Azure. | [Portail Azure](../insights/vminsights-overview.md)<br>[API REST monitor de charge de travail](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[API REST Azure Resource health](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alertes | Alertes créées par Azure Monitor. | [Portail Azure](alerts-managing-alert-instances.md)<br>[API REST de gestion des alertes](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Étapes suivantes

- Consultez les différentes sources de [analyse les données collectées par Azure Monitor](data-sources.md).
- En savoir plus sur la [types de données d’analyse collectées par Azure Monitor](data-platform.md) et comment afficher et analyser ces données.

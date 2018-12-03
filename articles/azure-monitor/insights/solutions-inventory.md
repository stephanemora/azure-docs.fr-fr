---
title: Détails sur la collecte de données pour les solutions de gestion dans Azure | Microsoft Docs
description: Les solutions de gestion dans Azure représentent une collection de règles logiques, de visualisation et d'acquisition des données qui fournissent des mesures cernant un domaine problématique en particulier.  Cet article fournit la liste des solutions de gestion de Microsoft et des détails sur leur méthode et leur fréquence de collecte de données.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 7f0334bdd1922c6ec410ca912ccfbf639e5c770f
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634436"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Détails sur la collecte de données pour les solutions de gestion dans Azure
Cet article fournit la liste des [solutions de gestion](solutions.md) disponibles auprès de Microsoft avec des liens vers leur documentation détaillée.  Il fournit également des informations sur leur méthode et leur fréquence de collecte de données dans Log Analytics.  Vous pouvez utiliser les informations de cet article pour identifier les différentes solutions disponibles et comprendre leurs exigences en termes de flux de données et de connexion. 

## <a name="list-of-management-solutions"></a>Liste des solutions de gestion

Le tableau suivant répertorie les [solutions de gestion](solutions.md) dans Azure fournies par Microsoft. Une entrée dans la colonne signifie que la solution collecte des données dans Log Analytics à l’aide de cette méthode.  Si une solution n’a aucune colonne sélectionnée, elle écrit directement dans Log Analytics depuis un autre service Azure. Pour plus d’informations, suivez le lien de chacune d’elles afin d’accéder à leur documentation détaillée.

Les explications des colonnes sont les suivantes :

- **Microsoft Monitoring Agent** : agent utilisé sous Windows et Linux pour exécuter le pack d’administration à partir de SCOM et les solutions de gestion à partir d’Azure. Dans cette configuration, l’agent est connecté directement à Log Analytics, sans être connecté à un groupe d’administration Operations Manager. 
- **Operations Manager** : agent identique à Microsoft Monitoring Agent. Dans cette configuration, il est [connecté à un groupe d’administration Operations Manager](../../log-analytics/log-analytics-om-agents.md) qui est connecté à Log Analytics. 
-  **Azure Storage** : solution collectant les données à partir d’un compte de stockage Azure. 
- **Operations Manager requis ?** : un groupe d’administration Operations Manager connecté est requis pour la collecte de données par la solution de gestion. 
- **Données de l’agent Operations Manager envoyées via le groupe d’administration** : si l’agent est [connecté à un groupe d’administration SCOM](../../log-analytics/log-analytics-om-agents.md), les données sont envoyées depuis le serveur d’administration à Log Analytics. Dans ce cas, l’agent n’a pas besoin de se connecter directement à Log Analytics. Si cette case n’est pas cochée, les données sont envoyées directement par l’agent à Log Analytics, même si l’agent est connecté à un groupe d’administration SCOM. Il doit pouvoir communiquer avec Log Analytics via la [passerelle Log Analytics](../../azure-monitor/platform/gateway.md).
- **Fréquence de collecte** : spécifie la fréquence de collecte des données par la solution de gestion. 



| **Solution de gestion** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../../log-analytics/log-analytics-activity.md) | Azure | | | | | | sur notification |
| [Évaluation d'AD](../../azure-monitor/insights/ad-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 jours |
| [État de la réplication AD](../../azure-monitor/insights/ad-replication-status.md) | Windows |&#8226; |&#8226; | | |&#8226; |5 jours |
| [Agent Health](solution-agenthealth.md) | Windows et Linux | &#8226; | &#8226; | | | &#8226; | 1 minute |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |à l'arrivée |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minute |
| [Alert Management](../../azure-monitor/platform/alert-management-solution.md) (Operations Manager) | Windows | |&#8226; | |&#8226; |&#8226; |3 minutes |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights Connector (préversion)](../../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | sur notification |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) |  Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Azure Application Gateway Analytics](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | sur notification |
| **Solution de gestion** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| [Azure Network Security Group Analytics (déconseillé)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | sur notification |
| [Azure SQL Analytics (préversion)](../../log-analytics/log-analytics-azure-sql.md) |  Windows | | | | | | 1 minute |
| [Sauvegarde](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | sur notification |
| [Capacity and Performance (préversion)](../../azure-monitor/insights/capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |à l'arrivée |
| [Suivi des modifications](../../automation/automation-change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |Toutes les heures |
| [Suivi des modifications](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |Toutes les heures |
| [Containers](../../log-analytics/log-analytics-containers.md) | Windows et Linux | &#8226; | &#8226; |  |  |  | 3 minutes |
| [Analytique Key Vault](../../azure-monitor/insights/azure-key-vault.md) | Windows | | | | | |sur notification |
| [Malware Assessment](../../security-center/security-center-install-endpoint-protection.md) | Windows |&#8226; |&#8226; | | |&#8226; |Toutes les heures |
| [Analyseur de performances réseau](../../azure-monitor/insights/network-performance-monitor.md) |  Windows | &#8226; | &#8226; |  |  |  | Établissements de liaisons TCP toutes les 5 secondes, données envoyées toutes les 3 minutes |
| [Office 365 Analytics (préversion)](solution-office-365.md) | Windows | | | | | |sur notification |
| **Solution de gestion** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| [Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) | Windows | | |&#8226; | | |5 minutes |
| [Service Map](../../azure-monitor/insights/service-map.md) | Windows et Linux | &#8226; | &#8226; |  |  |  | 15 secondes |
| [Évaluation de SQL](../../azure-monitor/insights/sql-assessment.md) | Windows |&#8226; |&#8226; | | |&#8226; |7 jours |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) | Windows |&#8226; | | | | |à l'arrivée |
| [System Center Operations Manager Assessment (préversion)](../../azure-monitor/insights/scom-assessment.md) |  Windows | &#8226; | &#8226; |  |  | &#8226; | sept jours |
| [Gestion des mises à jour](../../automation/automation-update-management.md) |  Windows |&#8226; |&#8226; | | |&#8226; |au moins 2 fois par jour et 15 minutes après l'installation d'une mise à jour |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) |  Windows | &#8226; |  |  |  |  | 2 jours |
| [VMware Monitoring (déconseillé)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutes |
| [Wire Data 2.0 (préversion)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2 / 8.1 ou ultérieur) |&#8226; |&#8226; | | | | 1 minute |




## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer des requêtes](../../log-analytics/log-analytics-queries.md) pour analyser les données collectées par les solutions de gestion.

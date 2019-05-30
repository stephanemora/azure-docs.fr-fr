---
title: Inventaire de la surveillance des solutions dans Azure | Microsoft Docs
description: Les solutions de monitoring d’Azure Monitor représentent une collection de règles logiques, de visualisation et d'acquisition de données qui fournissent des métriques reposant sur un domaine problématique en particulier.  Cet article fournit une liste de solutions de Microsoft et des détails sur leur méthode et leur fréquence de collecte de données de surveillance.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234032"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Données d’inventaire et détails de la collecte pour la surveillance des solutions dans Azure
[Surveillance des solutions](solutions.md) tirer parti des services dans Azure pour fournir des informations supplémentaires sur l’opération d’une application particulière ou d’un service. En règle générale, les solutions de monitoring collectent des données de journal et fournissent des requêtes et des vues permettant d’analyser ces données. Vous pouvez ajouter des solutions de monitoring à Azure Monitor pour toutes les applications et tous les services que vous utilisez. Ces solutions sont généralement disponibles gratuitement, mais elles collectent des données susceptibles d’occasionner des frais d’utilisation.

Cet article inclut une liste de [solutions de surveillance](solutions.md) disponibles auprès de Microsoft avec des liens vers leur documentation détaillée.  Il fournit également des informations sur leur méthode et leur fréquence de collecte de données dans Azure Monitor.  Vous pouvez utiliser les informations dans cet article pour identifier les différentes solutions disponibles et à comprendre les exigences de flux et de connexion de données pour différentes solutions de surveillance.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Liste des solutions de surveillance

Le tableau suivant répertorie les [solutions de surveillance](solutions.md) dans Azure fournies par Microsoft. Une entrée dans la colonne signifie que la solution collecte des données dans Azure Monitor à l’aide de cette méthode.  Si une solution n’a aucune colonne sélectionnée, elle écrit directement dans Azure Monitor depuis un autre service Azure. Pour plus d’informations, suivez le lien de chacune d’elles afin d’accéder à leur documentation détaillée.

Les explications des colonnes sont les suivantes :

- **Agent de surveillance Microsoft** - Agent permettant d’exécuter le pack d’administration à partir de SCOM sur Windows et Linux et surveillance des solutions à partir d’Azure. Dans cette configuration, l’agent est connecté directement à Azure Monitor, sans être connecté à un groupe d’administration Operations Manager. 
- **Operations Manager** : agent identique à Microsoft Monitoring Agent. Dans cette configuration, il est [connecté à un groupe d’administration Operations Manager](../platform/om-agents.md) qui est connecté à Azure Monitor. 
-  **Azure Storage** : solution collectant les données à partir d’un compte de stockage Azure. 
- **Operations Manager requis ?** -Un groupe d’administration Operations Manager connecté est requis pour la collecte de données par la solution de surveillance. 
- **Données de l’agent Operations Manager envoyées via le groupe d’administration** : si l’agent est [connecté à un groupe d’administration SCOM](../platform/om-agents.md), les données sont envoyées depuis le serveur d’administration à Azure Monitor. Dans ce cas, l’agent n’a pas besoin de se connecter directement à Azure Monitor. Si cette case n’est pas cochée, les données sont envoyées directement par l’agent à Azure Monitor, même si l’agent est connecté à un groupe d’administration SCOM. Il doit pouvoir communiquer avec Azure Monitor via la [passerelle Log Analytics](../platform/gateway.md).
- **Fréquence de collecte** -spécifie la fréquence que les données collectées par la solution de surveillance. 



| **Solution de surveillance** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analyse Log Analytics des activités](../platform/activity-log-collect.md) | Azure | | | | | | sur notification |
| [Évaluation d'AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 jours |
| [État de la réplication AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 jours |
| [Agent Health](solution-agenthealth.md) | Windows et Linux | &#8226; | &#8226; | | | &#8226; | 1 minute |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |à l'arrivée |
| [Alert Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minute |
| [Alert Management](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minutes |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights Connector (déconseillé)](../platform/app-insights-connector.md) | Azure | | | |  |  | sur notification |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | sur notification |
| **Solution de surveillance** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| [Azure Network Security Group Analytics (déconseillé)](azure-networking-analytics.md) | Azure |  |  |  |  |  | sur notification |
| [Azure SQL Analytics (préversion)](azure-sql.md) | Windows | | | | | | 1 minute |
| [Sauvegarde](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | sur notification |
| [Capacity and Performance (préversion)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |à l'arrivée |
| [Suivi des modifications](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Varie](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Suivi des modifications](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Varie](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows et Linux | &#8226; | &#8226; |  |  |  | 3 minutes |
| [Analytique Key Vault](azure-key-vault.md) |Windows | | | | | |sur notification |
| [Malware Assessment](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Toutes les heures |
| [Analyseur de performances réseau](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Établissements de liaisons TCP toutes les 5 secondes, données envoyées toutes les 3 minutes |
| [Office 365 Analytics (préversion)](solution-office-365.md) |Windows | | | | | |sur notification |
| **Solution de surveillance** | **Plateforme** | **Microsoft Monitoring Agent** | **Agent Operations Manager** | **Azure Storage** | **Operations Manager requis ?** | **Données de l’agent Operations Manager envoyées via un groupe d’administration** | **Fréquence de collecte** |
| [Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minutes |
| [Service Map](service-map.md) | Windows et Linux | &#8226; | &#8226; |  |  |  | 15 secondes |
| [Évaluation de SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 jours |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |à l'arrivée |
| [System Center Operations Manager Assessment (préversion)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sept jours |
| [Gestion des mises à jour](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |au moins 2 fois par jour et 15 minutes après l'installation d'une mise à jour |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 jours |
| [VMware Monitoring (déconseillé)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minutes |
| [Wire Data 2.0 (préversion)](wire-data.md) |Windows (2012 R2 / 8.1 ou ultérieur) |&#8226; |&#8226; | | | | 1 minute |




## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [installation et utilisation de solutions de surveillance](solutions.md).
* Apprenez à [créer des requêtes](../log-query/log-query-overview.md) pour analyser les données collectées par les solutions de monitoring.

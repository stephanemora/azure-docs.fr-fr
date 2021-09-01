---
title: Nouveautés dans la documentation Azure Monitor
description: Nouveautés dans la documentation Azure Monitor
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 1db9662322c6e4d8ba16eb29b13689a44ad1278b
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563711"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Nouveautés dans la documentation Azure Monitor

Cet article répertorie les modifications importantes apportées à la documentation Azure Monitor.

## <a name="july-2021"></a>Juillet 2021

### <a name="general"></a>Général

**Articles mis à jour**

- [Questions fréquemment posées sur Azure Monitor](faq.yml)
- [Déployer les fonctionnalités Azure Monitor à la bonne échelle à l’aide d’Azure Policy](deploy-scale.md)

### <a name="agents"></a>Agents

**Nouveaux articles**

- [Migration à partir de l’agent Log Analytics](agents/azure-monitor-agent-migration.md)

**Articles mis à jour**

- [Vue d’ensemble de l’agent Azure Monitor](agents/azure-monitor-agent-overview.md)

### <a name="alerts"></a>Alertes

**Articles mis à jour**

- [Définitions de schéma d’alerte courant](alerts/alerts-common-schema-definitions.md)
- [Créer une alerte de journal avec un modèle Resource Manager](alerts/alerts-log-create-templates.md)
- [Exemples de modèle Resource Manager pour les règles d’alerte de journal dans Azure Monitor](alerts/resource-manager-alerts-log.md)

### <a name="application-insights"></a>Application Insights

**Nouveaux articles**

- [Test standard](app/availability-standard-tests.md)

**Articles mis à jour**

- [Utiliser Azure Application Insights pour comprendre comment les clients utilisent votre application](app/tutorial-users.md)
- [Cohortes Application Insights](app/usage-cohorts.md)
- [Découvrir comment les clients utilisent votre application avec les entonnoirs Application Insights](app/usage-funnels.md)
- [Analyse de l’impact avec Application Insights](app/usage-impact.md)
- [Analyse de l'utilisation avec Application Insights](app/usage-overview.md)
- [Analyse de la rétention utilisateur des applications web avec Azure Application Insights](app/usage-retention.md)
- [Analyse des utilisateurs, des sessions et des événements dans Application Insights](app/usage-segmentation.md)
- [Résolution des problèmes d’Application Insights Agent (anciennement appelé Status Monitor v2)](app/status-monitor-v2-troubleshoot.md)
- [Superviser la disponibilité avec des tests Ping d’URL](app/monitor-web-app-availability.md)

### <a name="containers"></a>Conteneurs

**Nouveaux articles**

- [Comment interroger des journaux à partir de Container Insights](containers/container-insights-log-query.md)
- [Superviser Azure Kubernetes Service (AKS) avec Azure Monitor](../aks/monitor-aks.md)

**Articles mis à jour**

- [Création d’alertes de journal à partir de Container Insights](containers/container-insights-log-alerts.md)

### <a name="essentials"></a>Essentials

**Articles mis à jour**

- [Mesures prises en charge avec Azure Monitor](essentials/metrics-supported.md)
- [Catégories prises en charge pour les journaux de ressources Azure](essentials/resource-logs-categories.md)

### <a name="insights"></a>Insights

**Articles mis à jour**

- [Effectuer le monitoring de hubs Surface Hub avec Azure Monitor pour suivre leur intégrité](insights/surface-hubs.md)

### <a name="logs"></a>Journaux d’activité

**Articles mis à jour**

- [Clusters dédiés pour les journaux Azure Monitor](logs/logs-dedicated-clusters.md)
- [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor (préversion)](logs/logs-data-export.md)

### <a name="virtual-machines"></a>Machines Virtuelles

**Articles mis à jour**

- [Superviser les machines virtuelles avec Azure Monitor : Configurer la supervision](vm/monitor-virtual-machine-configure.md)
- [Superviser les machines virtuelles avec Azure Monitor : Supervision de la sécurité](vm/monitor-virtual-machine-security.md)
- [Superviser des machines virtuelles avec Azure Monitor : Charges de travail](vm/monitor-virtual-machine-workloads.md)
- [Superviser les machines virtuelles avec Azure Monitor](vm/monitor-virtual-machine.md)
- [Superviser les machines virtuelles avec Azure Monitor : Alertes](vm/monitor-virtual-machine-alerts.md)
- [Superviser les machines virtuelles avec Azure Monitor : Analyser les données de supervision](vm/monitor-virtual-machine-analyze.md)

### <a name="visualizations"></a>Visualisations

**Articles mis à jour**

- [Visualisation des données à partir d’Azure Monitor](visualizations.md)
## <a name="june-2021"></a>Juin 2021
### <a name="agents"></a>Agents

**Articles mis à jour**

- [Vue d’ensemble de l’agent Azure Monitor](agents/azure-monitor-agent-overview.md)
- [Vue d’ensemble des agents Azure Monitor](agents/agents-overview.md)
- [Configurer la collecte de données pour l’agent Azure Monitor (version préliminaire)](agents/data-collection-rule-azure-monitor-agent.md)

### <a name="alerts"></a>Alertes

**Nouveaux articles**

- [Migration de la détection intelligente Azure Monitor Application Insights vers les alertes (préversion)](alerts/alerts-smart-detections-migration.md)

**Articles mis à jour**

- [Créer des alertes de métrique de journaux d’activité dans Azure Monitor](alerts/alerts-metric-logs.md)
- [Alertes de journal de résolution de problèmes dans Azure Monitor](alerts/alerts-troubleshoot-log.md)

### <a name="application-insights"></a>Application Insights

**Nouveaux articles**

- [Authentification Azure AD pour Application Insights (préversion)](app/azure-ad-authentication.md)
- [Démarrage rapide : surveiller une application ASP.NET Core avec Azure Monitor Application Insights](app/dotnet-quickstart.md)

**Articles mis à jour**

- [Intégration d’éléments de travail](app/work-item-integration.md)
- [Authentification Azure AD pour Application Insights (préversion)](app/azure-ad-authentication.md)
- [Annotations de version pour Application Insights](app/annotations.md)
- [Chaînes de connexion](app/sdk-connection-string.md)
- [Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java](app/java-standalone-telemetry-processors.md)
- [Adresses IP utilisées par Azure Monitor](app/ip-addresses.md)
- [Supervision des applications Java sans code avec Azure Monitor Application Insights](app/java-in-process-agent.md)
- [Ajout de l’argument JVM – Azure Monitor Application Insights pour Java](app/java-standalone-arguments.md)
- [Application Insights pour applications ASP.NET Core](app/asp-net-core.md)
- [Exemples de processeur de télémétrie - Azure Monitor Application Insights pour Java](app/java-standalone-telemetry-processors-examples.md)
- [Pack de détection de la sécurité des applications (préversion)](app/proactive-application-security-detection-pack.md)
- [Détection intelligente dans Application Insights](app/proactive-diagnostics.md)
- [Élévation anormale du volume des exceptions (préversion)](app/proactive-exception-volume.md)
- [Détection intelligente des anomalies de performances](app/proactive-performance-diagnostics.md)
- [Détection des fuites de mémoire (version préliminaire)](app/proactive-potential-memory-leak.md)
- [Dégradation du rapport entre les niveaux de gravité des suivis (préversion)](app/proactive-trace-severity.md)

### <a name="containers"></a>Containers

**Articles mis à jour**

- [Comment interroger des journaux à partir de Container Insights](containers/container-insights-log-query.md)

### <a name="essentials"></a>Essentials

**Articles mis à jour**

- [Catégories prises en charge pour les journaux de ressources Azure](essentials/resource-logs-categories.md)
- [Exemples de modèle Resource Manager pour les paramètres de diagnostic dans Azure Monitor](essentials/resource-manager-diagnostic-settings.md)


### <a name="insights"></a>Insights

**Articles mis à jour**

- [Activer SQL Insights (préversion)](insights/sql-insights-enable.md)

### <a name="logs"></a>Journaux d’activité

**Articles mis à jour**

- [Tutoriel Log Analytics](logs/log-analytics-tutorial.md)
- [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md)
- [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](logs/private-link-security.md)
- [Clusters dédiés pour les journaux Azure Monitor](logs/logs-dedicated-clusters.md)
- [Surveiller l’intégrité d’un espace de travail Log Analytics dans Azure Monitor](logs/monitor-workspace.md)

### <a name="virtual-machines"></a>Machines Virtuelles

**Nouveaux articles**

- [Supervision des machines virtuelles avec Azure Monitor - Alertes](vm/monitor-virtual-machine-alerts.md)
- [Supervision des machines virtuelles avec Azure Monitor : Analyser les données de supervision](vm/monitor-virtual-machine-analyze.md)
- [Superviser les machines virtuelles avec Azure Monitor : Configurer la supervision](vm/monitor-virtual-machine-configure.md)
- [Superviser les machines virtuelles avec Azure Monitor : Supervision de la sécurité](vm/monitor-virtual-machine-security.md)
- [Superviser des machines virtuelles avec Azure Monitor : Charges de travail](vm/monitor-virtual-machine-workloads.md)
- [Analyse des machines virtuelles avec Azure Monitor](vm/monitor-virtual-machine.md)

**Articles mis à jour**

- [Résoudre des problèmes d’intégrité de l’invité de VM Insights (préversion)](vm/vminsights-health-troubleshoot.md)
- [Créer des rapports interactifs VM Insights avec des classeurs](vm/vminsights-workbooks.md)


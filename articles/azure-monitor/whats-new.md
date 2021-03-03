---
title: Nouveautés dans la documentation Azure Monitor
description: Chaque mois, la documentation Azure Monitor fait l’objet de mises à jour importantes.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: 45cbd0fc2a3a6a059274f33e25b577fde1f4a4ab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585539"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Nouveautés dans la documentation Azure Monitor

Cet article liste les nouveaux articles Azure Monitor et ceux qui ont fait l’objet d’une mise à jour importante. Il sera actualisé la première semaine de chaque mois pour inclure les mises à jour d’articles du mois précédent.

## <a name="january-2021"></a>Janvier 2021 

### <a name="general"></a>Général 
- [Forum aux questions Azure Monitor](faq.md) - Ajout d’une entrée sur les informations des appareils pour Application Insights.
### <a name="agents"></a>Agents  
- [Collecte d’événements ETW (Suivi d’événements pour Windows) pour l’analyse des journaux Azure Monitor](platform/data-sources-event-tracing-windows.md) - Nouvel article.
- [Règles de collecte des données dans Azure Monitor (préversion)](platform/data-collection-rule-overview.md) - Ajout de liens vers des exemples PowerShell et CLI.

### <a name="alerts"></a>Alertes  
- [Configurer Azure pour connecter les outils ITSM en utilisant l’exportation sécurisée](platform/itsm-connector-secure-webhook-connections-azure-configuration.md) - Nouvel article.
- [Erreurs d’état du connecteur dans le tableau de bord ITSMC](platform/itsmc-dashboard-errors.md) - Nouvel article.
- [Examiner les erreurs en utilisant le tableau de bord ITSMC](platform/itsmc-dashboard.md) - Nouvel article.
- [Résolution des problèmes liés aux alertes de métrique dans Azure](platform/alerts-troubleshoot-metric.md) - Ajout de sections pour les seuils dynamiques.
- [Résoudre les problèmes liés au connecteur de gestion des services informatiques](platform/itsmc-troubleshoot-overview.md) - Nouvel article.

### <a name="application-insights"></a>Application Insights
- [Corrélation de la télémétrie d’Azure Application Insights](app/correlation.md) -Ajout de la corrélation des traces quand un module en appelle un autre dans OpenCensus Python.
- [Application Insights pour les pages web](app/javascript.md)- Nouvel article.
- [Plug-in Click Analytics Auto-collection pour le SDK JavaScript d’Application Insights](app/javascript-click-analytics-plugin.md) - Nouvel article.
- [Superviser vos applications sans modification du code - Instrumentation automatique pour Azure Monitor Application Insights](app/codeless-overview.md) - Ajout d’une colonne Python.
- [Plug-in React pour le SDK JavaScript Application Insights](app/javascript-react-plugin.md) - Nouvel article.
- [Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java](app/java-standalone-telemetry-processors.md) - Réécriture.
- [Analyse de l’utilisation avec Azure Application Insights](app/usage-overview.md) - Nouvel article.
- [Utiliser l’analyse des changements d’application dans Azure Monitor pour rechercher les problèmes liés aux applications web](app/change-analysis.md) - Ajout de messages d’erreur.


### <a name="insights"></a>Insights    
- [Azure Monitor pour Azure Data Explorer (préversion)](insights/data-explorer.md) - Nouvel article.

### <a name="logs"></a>Journaux d’activité    
- [Azure Monitor - clé gérée par le client](platform/customer-managed-keys.md) - Introduction de l’identité managée affectée à l’utilisateur.
- [Clusters dédiés aux journaux Azure Monitor](log-query/logs-dedicated-clusters.md) - Code de réponse mis à jour.
- [Requête interservice - Azure Monitor et Azure Data Explorer (préversion)](platform/azure-monitor-troubleshooting-logs.md) - Nouvel article.

### <a name="metrics"></a>Mesures
- [Explication de l’agrégation et de l’affichage des métriques Azure Monitor](platform/metrics-aggregation-explained.md) - Nouvel article.

### <a name="platform-logs"></a>Journaux de plateforme
- [Services et catégories pris en charge par les journaux de ressources Azure Monitor](platform/resource-logs-categories.md) - Nouvel article.

### <a name="visualizations"></a>Visualisations
- [Sources de données des classeurs Azure Monitor](platform/workbooks-data-sources.md) - Ajout de l’analyse des fusions et des modifications.


## <a name="december-2020"></a>Décembre 2020

### <a name="general"></a>Général
- [Clé gérée par le client dans Azure Monitor](logs/customer-managed-keys.md) : ajout de messages d’erreur.
- [Partenaires s’intègrant à Azure Monitor](/partners.md) : ajout d’une section sur l’intégration d’Event Hub.

### <a name="agents"></a>Agents
- [Requête inter-ressources d’Azure Data Explorer à l’aide d’Azure Monitor](logs/azure-monitor-data-explorer-proxy.md) : nouvel article.
- [Vue d’ensemble des agents de supervision Azure](agents/agents-overview.md) : ajout de la prise en charge d’Oracle 8.

### <a name="alerts"></a>Alertes
- [Résolution des problèmes liés aux alertes de métrique dans Azure](alerts/alerts-troubleshoot-metric.md) : ajout de la résolution des problèmes liés aux seuils dynamiques.
- [Connecteur de gestion des services informatiques dans Log Analytics](alerts/itsmc-definition.md) : nouvel article.
- [Vue d’ensemble du connecteur de gestion des services informatiques](alerts/itsmc-overview.md) : informations sur la résolution des problèmes restructurées.
- [Connecter Cherwell avec le connecteur de gestion des services informatiques](alerts/itsmc-connections-cherwell.md) : nouvel article.
- [Connecter Provance avec le connecteur de gestion des services informatiques](alerts/itsmc-connections-provance.md) : nouvel article.
- [Connecter SCSM avec le connecteur de gestion des services informatiques](alerts/itsmc-connections-scsm.md) : nouvel article.
- [Connecter ServiceNow avec le connecteur de gestion des services informatiques](alerts/itsmc-connections-servicenow.md) : nouvel article.
- [Comment corriger manuellement les problèmes de synchronisation liés à ServiceNow](alerts/itsmc-resync-servicenow.md) : restructuration des informations de résolution des problèmes.




### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pour les applications web JavaScript](app/javascript.md) : ajout de la configuration des chaînes de connexion.
- [Métriques standard Azure Application Insights](app/standard-metrics.md) : nouvel article.
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) : informations supplémentaires sur l’envoi de données de télémétrie personnalisées à partir de votre application.
- [Exportation continue des données de télémétrie à partir d’Application Insights](app/export-telemetry.md) : ajout d’une fonctionnalité d’exportation basée sur des paramètres de diagnostic.
- [Activer le Débogueur de capture instantanée pour les applications .NET et .NET Core dans Azure Functions](app/snapshot-debugger-function-app.md) : nouvel article.
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) : ajout d’adresses IP pour Azure Government.
- [Résoudre les problèmes liés à Azure Application Insights Profiler](app/profiler-troubleshooting.md) : ajout d’informations sur la page d’état de l’extension de site des services de diagnostic.
- [Résoudre les problèmes de vos tests de disponibilité Azure Application Insights](app/troubleshoot-availability.md) : mises à jour de la résolution des problèmes pour les tests ping.
- [Résolution des problèmes dans Azure Monitor Application Insights pour Java](app/java-standalone-troubleshoot.md) : nouvel article.

### <a name="containers"></a>Containers
- [Rapports dans Azure Monitor pour les conteneurs](insights/container-insights-reports.md) : nouvel article.

### <a name="logs"></a>Journaux d’activité
- [Clusters dédiés aux journaux Azure Monitor](logs/logs-dedicated-clusters.md) : ajout de méthodes et de commandes automatisées de dissociation, de suppression et de dépannage.
- [Vue d’ensemble des requêtes interservices entre Azure Monitor et Azure Data Explorer (préversion)](logs/azure-data-explorer-monitor-cross-service-query.md) : nouvel article.
- [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor (préversion)](logs/logs-data-export.md) : ajout de modèles ARM.

### <a name="metrics"></a>Mesures
- [Fonctionnalités avancées d’Azure Metrics Explorer](essentials/metrics-charts.md) : ajout d’informations sur le sélecteur d’étendue des ressources.
- [Affichage de plusieurs ressources dans Metrics Explorer](essentials/metrics-dynamic-scope.md) : nouvel article.

### <a name="networks"></a>Réseaux
- [Solution Azure Networking Analytics dans Azure Monitor](insights/azure-networking-analytics.md) : ajout d’informations sur le workbook Network Insights.

### <a name="virtual-machines"></a>Machines Virtuelles
- [Activer Azure Monitor pour un environnement hybride](vm/vminsights-enable-hybrid.md) : nouvelle version de l’agent de dépendances.


### <a name="visualizations"></a>Visualisations
- [Visualisations d’arborescences de workbook Azure Monitor](visualize/workbooks-map-visualizations.md) : nouvel article.
- [Azure Monitor Workbooks - BYOS (apportez votre propre stockage)](visualize/workbooks-bring-your-own-storage.md) : nouvel article.


## <a name="november-2020"></a>Novembre 2020

### <a name="general"></a>Général
- [Limites du service Azure Monitor](service-limits.md) – Mis à jour pour la prise en charge d’Azure Arc.

### <a name="agents"></a>Agents
- [Vue d’ensemble des agents de supervision Azure](agents/agents-overview.md) – Mis à jour pour la prise en charge d’Azure Arc.
- [Installer l’agent Azure Monitor](agents/azure-monitor-agent-install.md) – Nouvel article.
- [Vue d’ensemble de l’agent Azure Monitor](agents/azure-monitor-agent-overview.md) – Mis à jour pour la prise en charge d’Azure Arc.
- [Exemples de modèles Resource Manager pour les agents](agents/resource-manager-agent.md) – Mis à jour pour la prise en charge d’Azure Arc.

### <a name="alerts"></a>Alertes
- [Créer et gérer des groupes d’action sur le portail Azure](alerts/action-groups.md) – Ajout d’adresses IP sources pour les webhooks.

### <a name="application-insights"></a>Application Insights
- [Supervision des applications Java sans code avec Azure Monitor Application Insights](app/java-in-process-agent.md) – Ajout d’un exemple de configuration.
- [Plug-in React pour le SDK JavaScript Application Insights](app/javascript-react-plugin.md) – Ajout de la section sur l’utilisation de hooks React.
- [Mise à niveau à partir du SDK Application Insights Java 2.x](app/java-standalone-upgrade-from-2x.md) – Nouvel article.
- [Notes de publication pour Microsoft.ApplicationInsights.SnapshotCollector](app/snapshot-collector-release-notes.md) – Nouvel article.

### <a name="autoscale"></a>Mise à l’échelle automatique
- [Bien démarrer avec la mise à l’échelle automatique dans Azure](autoscale/autoscale-get-started.md) – Ajout d’une section sur le déplacement de la mise à l’échelle automatique vers une autre région.

### <a name="data-collection"></a>Collecte de données
- [Configurer la collecte de données pour l’agent Azure Monitor (préversion)](agents/data-collection-rule-azure-monitor-agent.md) – Mis à jour pour la prise en charge d’Azure Arc.
- [Règles de collecte de données dans Azure Monitor (préversion)](agents/data-collection-rule-overview.md) – Mis à jour pour la prise en charge d’Azure Arc.
- [Exemples de modèles Resource Manager pour les règles de collecte de données](agents/resource-manager-data-collection-rules.md) – Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions
- [Connecter Azure aux outils ITSM en utilisant l’exportation sécurisée](alerts/it-service-management-connector-secure-webhook-connections.md) – Ajout de la section sur la connexion à ServiceNow.

### <a name="logs"></a>Journaux d’activité
- [Intégrer Log Analytics et Excel](logs/log-excel.md) – Nouvel article.
- [Sécurité des données Log Analytics](logs/data-security.md) – Ajout de la section sur les fonctionnalités de sécurité supplémentaires.
- [Intégration de Log Analytics avec Power BI](logs/log-powerbi.md) – Nouvel article.
- [Colonnes standard dans les enregistrements de journaux Azure Monitor](logs/log-standard-columns.md) – Ajout de la colonne _SubscriptionId.

Articles nouveaux et mis à jour par rapport à la restructuration du contenu des requêtes de journal.

- [Tutoriel Log Analytics](logs/log-analytics-tutorial.md)
- [Requêtes de journal dans Azure Monitor](logs/log-query-overview.md)
- [Présentation de Log Analytics dans Azure Monitor](logs/log-analytics-overview.md)
- [Exemples de requêtes pour Azure Data Explorer et Azure Monitor](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [Tutoriel : Utiliser des requêtes Kusto dans Azure Data Explorer et Azure Monitor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>Machines virtuelles

- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md) - Ajout des régions prises en charge.

Nouveaux articles pour l’intégrité des invités Azure Monitor pour machines virtuelles (préversion)

- [Intégrité des invités Azure Monitor pour machines virtuelles (préversion)](vm/vminsights-health-overview.md)
- [Alertes d’Intégrité des invités Azure Monitor pour machines virtuelles (préversion)](vm/vminsights-health-alerts.md)
- [Configurer la supervision d’Intégrité des invités Azure Monitor pour machines virtuelles (préversion)](vm/vminsights-health-configure.md)
- [Configurer la supervision de l’intégrité des invités Azure Monitor pour machines virtuelles en utilisant des règles de collecte des données (préversion)](vm/vminsights-health-configure-dcr.md)
- [Activer l’intégrité des invités d’Azure Monitor pour machines virtuelles (préversion)](vm/vminsights-health-enable.md)
- [Résoudre les problèmes d’Intégrité des invités Azure Monitor pour machines virtuelles (préversion)](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>Octobre 2020

### <a name="general"></a>Général
- [Mise hors service de l’API Azure Monitor](logs/operationalinsights-api-retirement.md) - Nouvel article.

### <a name="agents"></a>Agents
- [Quels sont les éléments supervisés par Azure Monitor ?](monitor-reference.md) - Section ajoutée sur les agents.

### <a name="alerts"></a>Alertes
- [Créer et gérer des groupes d’action sur le portail Azure](alerts/action-groups.md) - Section ajoutée sur les étiquettes de service.
- [Exemples de modèle Resource Manager pour les règles d’alerte de métrique dans Azure Monitor](alerts/resource-manager-alerts-metric.md) - Ajout du paramètre content-match et des emplacements de test.
- [Résolution des problèmes liés aux alertes de métrique dans Azure Monitor](alerts/alerts-troubleshoot-metric.md) - Bonnes pratiques concernant la configuration des règles.

### <a name="application-insights"></a>Application Insights
- [Plug-in Angular pour le Kit de développement logiciel (SDK) JavaScript Application Insights](app/javascript-angular-plugin.md) - Nouvel article.
- [Azure Application Insights pour les applications ASP.NET Core](app/asp-net-core.md) - Ajout de la FAQ sur les journaux ILogger.
- [Configurer la supervision pour ASP.NET avec Azure Application Insights](app/asp-net.md) - Article réécrit.
- [Métriques basées sur le journal et pré-agrégées dans Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) - Ajout des tables avec métriques pré-agrégées.
- [Superviser la disponibilité et la réactivité d’un site web](app/monitor-web-app-availability.md) - Section ajoutée sur les étiquettes de remplissage des régions.
- [Superviser les applications Java partout - Azure Monitor Application Insights](app/java-standalone-config.md) - Ajout d’un exemple de configuration.
- [Superviser les applications Java partout - Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) - Nouvel article.
- [Utiliser l’analyse des changements d’application dans Azure Monitor pour rechercher les problèmes liés aux applications web](app/change-analysis.md) - Ajout de sections sur les machines virtuelles et le journal d’activité.
  
### <a name="autoscale"></a>Mise à l’échelle automatique
- [Bien démarrer avec la mise à l’échelle automatique dans Azure](autoscale/autoscale-get-started.md) - Ajout d’une section sur le déplacement de la mise à l’échelle automatique vers une autre région.

### <a name="containers"></a>Containers
- [Configurer la supervision de volumes persistants avec Azure Monitor pour conteneurs](containers/container-insights-persistent-volumes.md) - Nouvel article.
- [Guide pratique pour la gestion de l’agent Azure Monitor pour conteneurs](containers/container-insights-manage-agent.md) - Ajout de la prise en charge des clusters Kubernetes où Azure Arc est activé.
- [Alertes sur les métriques d’Azure Monitor pour conteneurs](containers/container-insights-metric-alerts.md) - Ajout de la prise en charge des clusters Kubernetes où Azure Arc est activé.

### <a name="insights-and-solutions"></a>Insights et solutions
- [Connecteur de gestion des services informatiques - Exportation sécurisée dans Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md) - Section ajoutée sur ServiceNow.

### <a name="logs"></a>Journaux d’activité
- [Archiver des données de l’espace de travail Log Analytics dans le Stockage Azure à l’aide d’une application logique](logs/logs-export-logic-app.md) - Nouvel article.
- [Exportation des données de l’espace de travail Log Analytics dans Azure Monitor (préversion)](logs/logs-data-export.md) - Ajout d’un exemple de corps de requête REST pour le hub d’événements.
- [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md) - Ajout d’informations sur la relation entre les journaux Azure Monitor et la facturation Azure Security Center. Requête ajoutée pour le nombre de nœuds si vous utilisez le niveau tarifaire Par nœud. 
- [Surveiller l’intégrité d’un espace de travail Log Analytics dans Azure Monitor](logs/monitor-workspace.md) - Nouvel article.
- [Interroger des données dans Azure Monitor avec Azure Data Explorer (préversion)](logs/azure-data-explorer-monitor-proxy.md) - Nouvel article.
- [Interroger les données exportées dans Azure Monitor avec Azure Data Explorer (préversion)](logs/azure-data-explorer-query-storage.md) - Nouvel article.

### <a name="networks"></a>Réseaux
- [Azure Monitor pour réseaux (préversion)](insights/network-insights-overview.md) - Section ajoutée sur la résolution des problèmes. Ajout d’une section sur la connectivité.

### <a name="platform-logs"></a>Journaux de plateforme
- [Schéma d’événements du journal d’activité Azure](essentials/activity-log-schema.md) - Ajout d’une description pour les niveaux de gravité.

### <a name="virtual-machines"></a>Machines virtuelles
- [Analyse des modifications dans Azure Monitor pour machines virtuelles](vm/vminsights-change-analysis.md) - Nouvel article.
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md) - Ajout des régions prises en charge.
- [Mise à jour d’Azure Monitor pour conteneurs afin d’activer les métriques](containers/container-insights-update-metrics.md) - Ajout de la prise en charge des clusters Kubernetes où Azure Arc est activé.



## <a name="september-2020"></a>Septembre 2020

### <a name="general"></a>Général
- [Questions fréquentes (FAQ) sur Azure Monitor](faq.md) : ajout de la section sur OpenTelemetry.

### <a name="agents"></a>Agents
- [Vue d’ensemble de l’agent Azure Monitor](agents/azure-monitor-agent-overview.md) : ajout de facteurs de décision pour basculer vers le nouvel agent.
- [Vue d’ensemble des agents de supervision Azure](agents/agents-overview.md) : ajout de la prise en charge de Windows 10.

### <a name="alerts"></a>Alertes
- [Créer une alerte de journal à l’aide d’un modèle Resource Manager](alerts/alerts-log-create-templates.md) : nouvel article.
- [Résolution des problèmes liés aux alertes de métrique Azure](alerts/alerts-troubleshoot-metric.md) : ajout de la section sur l’exportation d’un modèle ARM pour une règle d’alerte de métrique.

### <a name="application-insights"></a>Application Insights
- [Créer une ressource Azure Monitor Application Insights basée sur un espace de travail](app/create-workspace-resource.md) : suppression de la mention « préversion ».
- [Conservation et stockage des données dans Azure Application Insights](app/data-retention-privacy.md) : ajout d’informations détaillées sur la nouvelle prise en charge de la protection contre la perte de données sur Mac et Linux.
- [Compteurs d’événements dans Application Insights](app/eventcounters.md) : ajout d’une remarque sur les compteurs collectés par défaut.
- [Métriques basées sur le journal et pré-agrégées dans Azure Application Insights](app/pre-aggregated-metrics-log-metrics.md) : suppression de la mention « préversion ».
- [Migrer une ressource Azure Monitor Application Insights classique vers une ressource basée sur un espace de travail](app/convert-classic-resource.md) : nouvel article.
- [Superviser les applications Java sur tout environnement - Azure Monitor Application Insights](app/java-in-process-agent.md) : mise à jour pour la nouvelle préversion de l’agent.
- [Configurer des analyses d’application web pour ASP.NET avec Azure Application Insights](app/asp-net.md) : réécriture de l’article.
- [Canaux de télémétrie dans Azure Application Insights](app/telemetry-channels.md) : ajout d’informations détaillées sur la nouvelle prise en charge de la protection contre la perte de données sur Mac et Linux.
- [Dépannage du débogueur de capture instantanée d’Azure Application Insights](app/snapshot-debugger-troubleshoot.md) : ajout de la section SSL à Résolution des problèmes du débogueur de capture instantanée.
- [Utiliser l’analyse des changements d’application dans Azure Monitor pour rechercher les problèmes liés aux applications web](app/change-analysis.md) : ajout de machines virtuelles et de journaux d’activité.


### <a name="containers"></a>Conteneurs
- [Configurer un cluster Kubernetes compatible Azure Arc avec Azure Monitor pour conteneurs](containers/container-insights-enable-arc-enabled-clusters.md) : ajout de conseils pour l’activation de la supervision à l’aide du principal de service.
- [Déploiement et métriques HPA avec Azure Monitor pour conteneurs](containers/container-insights-deployment-hpa-metrics.md) - Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions
- [Azure Monitor pour Azure Cache pour Redis](insights/redis-cache-insights-overview.md) : suppression de la mention « préversion ».
- [Azure Monitor pour réseaux (préversion)](insights/network-insights-overview.md) : ajout des sections Connectivité et Trafic.
- [Connecteur de gestion des services informatiques – Exportation sécurisée dans Azure Monitor](alerts/it-service-management-connector-secure-webhook-connections.md) : nouvel article.
- [Connecteur de gestion des services informatiques dans Azure Monitor](alerts/itsmc-connections.md) : remarque sur les intégrations ITSM de Cherwell et Provance.
- [Superviser Key Vault avec Azure Monitor pour Key Vault](insights/key-vault-insights-overview.md) : suppression de la mention « préversion ».

### <a name="logs"></a>Journaux d’activité
- [Requêtes d’audit dans les requêtes de journal Azure Monitor](logs/query-audit.md) : nouvel article.
- [Clé gérée par le client dans Azure Monitor](logs/customer-managed-keys.md) : ajout de Customer Lockbox.
- [Clusters dédiés aux journaux Azure Monitor](logs/logs-dedicated-clusters.md) : nouvel article.
- [Conception de votre déploiement de journaux Azure Monitor](logs/design-logs-deployment.md) : mise à jour de la section Échelle et limite de débit du volume d’ingestion.
- [Étendue de requête de journal dans la fonctionnalité Log Analytics d’Azure Monitor](logs/scope.md) : mises à jour pour inclure les applications basées sur un espace de travail.
- [Journaux dans Azure Monitor](logs/data-platform-logs.md) : mises à jour pour inclure les applications basées sur un espace de travail.
- [Colonnes standard dans les enregistrements de journaux Azure Monitor ](logs/log-standard-columns.md) : mises à jour pour inclure les applications basées sur un espace de travail.
- [Limites du service Azure Monitor](service-limits.md) : mise à jour des limites pour la limitation des requêtes utilisateur.
- [Utilisation de comptes de stockage gérés par le client dans Azure Monitor Log Analytics](logs/private-storage.md) : réécriture de l’article.
- [Affichage et analyse de données dans Azure Log Analytics](./logs/data-platform-logs.md) : mises à jour pour inclure les applications basées sur un espace de travail.


### <a name="platform-logs"></a>Journaux de plateforme
- [Schéma d’événement du journal d’activité Azure - Azure Monitor](essentials/activity-log-schema.md) : ajout de niveaux de gravité.
- [Exemples de modèle Resource Manager pour les paramètres de diagnostic](essentials/resource-manager-diagnostic-settings.md) : ajout d’un exemple pour le compte de stockage Azure.

### <a name="visualizations"></a>Visualisations
- [Visualisations de graphiques de classeur Azure Monitor](visualize/workbooks-chart-visualizations.md) : nouvel article.
- [Renderer de barre composite des classeurs Azure Monitor](visualize/workbooks-composite-bar.md) : nouvel article.
- [Visualisations de graphiques de classeur Azure Monitor](visualize/workbooks-graph-visualizations.md) : nouvel article.
- [Visualisations de grille de classeur Azure Monitor](visualize/workbooks-grid-visualizations.md) : nouvel article.
- [Visualisations en alvéoles de classeur Azure Monitor](visualize/workbooks-honey-comb.md) : nouvel article.
- [Visualisations de textes de classeur Azure Monitor](visualize/workbooks-text-visualizations.md) : nouvel article.
- [Visualisations de vignette de classeur Azure Monitor](visualize/workbooks-tile-visualizations.md) : nouvel article.
- [Visualisations d’arborescences de classeur Azure Monitor](visualize/workbooks-tree-visualizations.md) : nouvel article.




## <a name="august-2020"></a>Août 2020

### <a name="general"></a>Général

- [Quels sont les éléments supervisés par Azure Monitor ?](monitor-reference.md) - Mis à jour pour inclure l’agent Azure Monitor.


### <a name="agents"></a>Agents
- [Vue d’ensemble de l’agent Azure Monitor](agents/azure-monitor-agent-overview.md) - Nouvel article.
- [Activer Azure Monitor pour un environnement hybride](vm/vminsights-enable-hybrid.md) - Mise à jour de la version de Dependency Agent.
- [Vue d’ensemble des agents de surveillance Azure](agents/agents-overview.md) - Ajout de l’agent Azure Monitor et unification du tableau des systèmes d’exploitation pris en charge.


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>Articles nouveaux et mis à jour par rapport à la restructuration du contenu de l’agent
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md)
- [Installer l’agent Log Analytics sur des ordinateurs Linux](agents/agent-linux.md)
- [Installer l’agent Log Analytics sur des ordinateurs Windows](agents/agent-windows.md)
- [Présentation de l’agent Log Analytics](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pour les applications web JavaScript](app/javascript.md) - Ajout d’une section clarifiant la corrélation entre le client et le serveur, et la configuration pour la corrélation CORS.
- [Créer une ressource Azure Monitor Application Insights basée sur un espace de travail](app/create-workspace-resource.md) - Ajout de fonctionnalités fournies par les applications basées sur un espace de travail.
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) – Mise à jour des adresses IP pour les flux de métriques temps réel.
- [Superviser les applications Java sur tout environnement - Azure Monitor Application Insights](app/java-in-process-agent.md) - Ajout d’un tableau pour les métriques personnalisées prises en charge.
- [Plug-in Native React pour le SDK JavaScript Application Insights](app/javascript-react-native-plugin.md) - Nouvel article.
- [Plug-in React pour le SDK JavaScript Application Insights](app/javascript-react-plugin.md) - Nouvel article.
- [Exemple de modèle Resource Manager pour la création d’applications Azure Function avec la supervision Application Insights](app/resource-manager-function-app.md) - Nouvel article.
- [Exemples de modèles Resource Manager pour la création d’applications web Azure App Services avec la supervision Application Insights](app/resource-manager-web-app.md) - Nouvel article.
- [Analyse de l’utilisation avec Azure Application Insights](app/usage-overview.md) - Ajout d’une vidéo.

### <a name="autoscale"></a>Mise à l’échelle automatique
- [Bien démarrer avec la mise à l’échelle automatique dans Azure](autoscale/autoscale-get-started.md) - Ajout d’une section sur le routage vers des instances saines pour App Service.

### <a name="data-collection"></a>Collecte des données
- [Configurer la collecte de données pour l’agent Azure Monitor (préversion)](agents/data-collection-rule-azure-monitor-agent.md) - Nouvel article.
- [Règles de collecte de données dans Azure Monitor (préversion)](agents/data-collection-rule-overview.md) - Nouvel article.


### <a name="containers"></a>Containers
- [Déploiement et métriques HPA avec Azure Monitor pour conteneurs](containers/container-insights-deployment-hpa-metrics.md) - Nouvel article.

### <a name="insights"></a>Insights
- [Solutions de monitoring dans Azure Monitor](insights/solutions.md) - Mise à jour par rapport à la nouvelle IU.
- [Solution Network Performance Monitor dans Azure](insights/network-performance-monitor.md) – Ajout des régions d’espace de travail prises en charge.


### <a name="logs"></a>Journaux d’activité
- [Questions fréquentes (FAQ) sur Azure Monitor](faq.md) - Ajout d’une entrée relative à la suppression de données d’un espace de travail. Entrée ajoutée pour les réponses 502 et 503.
  - [Conception de votre déploiement de journaux Azure Monitor](logs/design-logs-deployment.md) - Mises à jour de la section Limite de débit du volume d’ingestion.
- [Gérer l’utilisation et les coûts avec les journaux Azure Monitor](logs/manage-cost-storage.md) - Mise à jour des requêtes Usage pour un format de requête plus efficace.
- [Optimiser les requêtes de journal dans Azure Monitor](logs/query-optimization.md) - Ajout de valeurs spécifiques aux indicateurs de performance.
- [Exemples de modèle Resource Manager pour les paramètres de diagnostic](essentials/resource-manager-diagnostic-settings.md) - Ajout d’un exemple pour les journaux d’audit des requêtes de journal.


### <a name="platform-logs"></a>Journaux de plateforme
- [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](essentials/diagnostic-settings.md) - Ajout d’une exigence régionale pour les paramètres de diagnostic.

### <a name="visualizations"></a>Visualisations
- [Vue d’ensemble des classeurs Azure Monitor](visualize/workbooks-overview.md) - Ajout d’une vidéo.
- [Déplacer un modèle de classeur Azure vers une autre région](visualize/workbook-templates-move-region.md) - Nouvel article.
- [Déplacer un classeur Azure vers une autre région](visualize/workbooks-move-region.md) - Nouvel article.



## <a name="july-2020"></a>Juillet 2020

### <a name="general"></a>Général
- [Déployer Azure Monitor](deploy-scale.md) – Restructurer Azure Monitor pour du contenu d’intégration de machines virtuelles.
- [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](logs/private-link-security.md) – Ajout d’une section sur les limites.

### <a name="alerts"></a>Alertes
- [Règles d’action pour les alertes Azure Monitor](alerts/alerts-action-rules.md) – Ajout de traitements CLI.
- [Créer et gérer des groupes d’actions dans le portail Azure](alerts/action-groups.md) – Mise à jour pour refléter les modifications apportées à l’interface utilisateur.
- [Exemples de requête dans Azure Monitor Log Analytics](logs/example-queries.md) – Nouvel article.
- [Résoudre les problèmes d’alertes de journal dans Azure Monitor](alerts/alerts-troubleshoot-log.md) – Ajout d’une section sur le quota de règles d’alerte.
- [Résolution des problèmes d’alertes de métriques Azure](alerts/alerts-troubleshoot-metric.md) – Ajout d’une section sur les règles d’alerte de métrique personnalisée non encore émise.
- [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor.](alerts/alerts-metric-overview.md) – Ajout d’une recommandation pour la sélection de la granularité de l’agrégation.

### <a name="application-insights"></a>Application Insights
- [Notes de publication pour l’extension d’application web Azure – Application Insights](app/web-app-extension-release-notes.md) – Nouvel article.
- [Exemples de modèles Resource Manager pour des ressources Application Insights](app/resource-manager-app-resource.md) – Nouvel article.
- [Résoudre les problèmes liés à Application Insights Profiler](app/profiler-troubleshooting.md) – Ajout d’une remarque sur l’exécution du profileur pour les applications ASP.NET Core sur Azure App Service. 

### <a name="containers"></a>Containers
- [Journaliser les alertes d’Azure Monitor pour conteneurs](containers/container-insights-log-alerts.md) – Nouvel article.
- [Alertes métriques d’Azure Monitor pour conteneurs](containers/container-insights-metric-alerts.md) – Nouvel article.

### <a name="logs"></a>Journaux d’activité
- [Clé managée par le client Azure Monitor](logs/customer-managed-keys.md) – Ajout de message d’erreur et d’une section sur la configuration de CMK pour les requêtes.
- [API Collecte de données HTTP Azure Monitor](logs/data-collector-api.md) – Ajout d’une exemple Python 3.
- [Optimiser les requêtes de journal dans Azure Monitor](logs/query-optimization.md) – Ajout d’une section sur l’évitement d’analyses multiples de données lors de l’utilisation de sous-requêtes.
- [Tutoriel : Bien démarrer avec les requêtes Log Analytics](./logs/log-analytics-tutorial.md) – Ajout de vidéo.

### <a name="platform-logs"></a>Journaux de plateforme
- [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](essentials/diagnostic-settings.md) – Ajout de vidéo.
- [Exemples de modèles Resource Manager pour Azure Monitor](/resource-manager-samples.md) – Ajout d’exemple ARM utilisant le type de destination Journaux. 

### <a name="solutions"></a>Solutions
- [Solutions de surveillance dans Azure Monitor](insights/solutions.md) – Ajout de processus CLI.
- [Solution de gestion Office 365 dans Azure](insights/solution-office-365.md) – Changement de la date de suppression.

### <a name="virtual-machines"></a>Machines virtuelles

Articles nouveaux et mis à jour à partir de la restructuration d’Azure Monitor pour le contenu de machines virtuelles

- [Qu’est-ce qu’Azure Monitor pour machines virtuelles ?](vm/vminsights-overview.md)
- [Configurer l’espace de travail Log Analytics pour Azure Monitor pour machines virtuelles](vm/vminsights-configure-workspace.md)
- [Connecter des ordinateurs Linux à Azure Monitor](agents/agent-linux.md)
- [Activer Azure Monitor pour un environnement hybride](vm/vminsights-enable-hybrid.md)
- [Activer Azure Monitor pour une machine virtuelle ou un groupe de machines virtuelles identiques dans le portail Azure](vm/vminsights-enable-portal.md)
- [Activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy](./vm/vminsights-enable-policy.md)
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md)
- [Activer Azure Monitor pour machines virtuelles à l’aide de PowerShell](vm/vminsights-enable-powershell.md)
- [Activer Azure Monitor pour machines virtuelles à l’aide de modèles Resource Manager](vm/vminsights-enable-resource-manager.md)
- [Activer Azure Monitor pour machines virtuelles avec PowerShell ou des modèles](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>Visualisations
- [Mise à niveau de vos visualisations de tableau de bord Log Analytics](logs/dashboard-upgrade.md) – Taux d’actualisation mis à jour.
- [Visualisation des données d’Azure Monitor](visualizations.md) – Ajout de vidéo.


## <a name="june-2020"></a>Juin 2020

### <a name="general"></a>Général
- [Déployer Azure Monitor](deploy-scale.md) - Nouvel article.
- [Clé gérée par le client dans Azure Monitor](logs/customer-managed-keys.md) - Mise à jour de la propriété billingtype. Ajout de commandes PowerShell.

### <a name="agents"></a>Agents
- [Présentation de l’agent Log Analytics](agents/log-analytics-agent.md) - Ajout de l’exigence relative à Python 2.

### <a name="alerts"></a>Alertes
- [Guide pratique pour mettre à jour des règles d’alerte ou des règles d’action quand la ressource cible est déplacée vers une autre région Azure](alerts/alerts-resource-move.md) - Nouvel article.
- [Résolution des problèmes liés aux alertes de métrique Azure](alerts/alerts-troubleshoot-metric.md) - Nouvel article.
- [Résolution des problèmes liés aux alertes de journal d’activité dans Azure Monitor](alerts/alerts-troubleshoot-metric.md) - Nouvel article.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights pour les applications web JavaScript](app/javascript.md) - Mise à jour de la section sur le SDK JavaScript. Mise à jour de l’extrait de code pour signaler les échecs de chargement.
- [Configurer BYOS (Bring Your Own Storage) pour Profiler et le Débogueur de capture instantanée](app/profiler-bring-your-own-storage.md) - Nouvel article.
- [Suivi des requêtes entrantes dans Azure Application Insights avec OpenCensus Python](app/opencensus-python-request.md) - Mise à jour de la journalisation et de la configuration pour OpenCensus.
- [Superviser une application web ASP.NET en production avec Azure Application Insights](app/monitor-performance-live-website-now.md) - Mise à jour de la date de dépréciation pour Status Monitor v1.
- [Superviser les services Node.js avec Azure Application Insights](app/nodejs.md) - Plusieurs mises à jour, notamment la migration à partir de versions antérieures et la configuration du SDK.
- [Superviser les applications Python avec Azure Monitor (préversion)](app/opencensus-python.md) - Ajout d’une section sur la configuration des exportateurs Azure Monitor.
- [Superviser vos applications sans modification du code - Instrumentation automatique pour Azure Monitor Application Insights](app/codeless-overview.md) - Nouvel article.
- [Résolution des échecs de chargement du SDK pour les applications web JavaScript](app/javascript-sdk-load-failure.md) - Nouvel article.

### <a name="containers"></a>Conteneurs
- [Guide pratique pour arrêter la supervision de votre cluster Kubernetes hybride](containers/container-insights-optout-hybrid.md) - Ajout d’une section pour Kubernetes compatible avec Arc.
- [Configurer un cluster Kubernetes compatible Azure Arc avec Azure Monitor pour conteneurs](containers/container-insights-enable-arc-enabled-clusters.md) - Nouvel article.
- [Configurer Azure Red Hat OpenShift v4.x avec Azure Monitor pour conteneurs](containers/container-insights-azure-redhat4-setup.md) - Mise à jour des prérequis.
- [Configurer Azure Monitor pour conteneurs - Live Data (préversion)](containers/container-insights-livedata-setup.md) - Suppression de la remarque signalant que la fonctionnalité n’est pas disponible dans Azure US Government.

### <a name="insights"></a>Insights
- [FAQ - Solution Network Performance Monitor dans Azure](insights/network-performance-monitor-faq.md) - Ajout d’un FAQ pour ExpressRoute Monitor.

### <a name="logs"></a>Journaux
- [Supprimer et récupérer un espace de travail Azure Log Analytics](logs/delete-workspace.md) – Ajout d’une commande PowerShell. Mise à jour de la résolution des problèmes.
- [Gérer les espaces de travail Log Analytics dans Azure Monitor](logs/manage-access.md) – Ajout d’un exemple pour les tables non autorisées dans la section Azure RBAC.
- [Gérer l’utilisation et les coûts à l’aide des journaux d’activité Azure Monitor](logs/manage-cost-storage.md) - Détails supplémentaires sur le calcul de la taille des données. Mise à jour des alertes de volume de données de configuration. Détails sur les données de sécurité collectées par Azure Sentinel. Clarification sur la limite de données.
- [Utiliser Azure Monitor Logs avec Azure Logic Apps et Power Automate](logs/logicapp-flow-connector.md) - Ajout de limites de connecteur.

### <a name="metrics"></a>Métriques
- [Métriques prises en charge par Azure Monitor par type de ressource](essentials/metrics-supported.md) - Mise à jour des métriques SQL Server.


### <a name="platform-logs"></a>Journaux de plateforme

- [Exemples de modèle Resource Manager pour les paramètres de diagnostic](essentials/resource-manager-diagnostic-settings.md) - Correctif pour le paramètre de diagnostic du journal d’activité.
- [Envoyer le journal d’activité Azure à un espace de travail Log Analytics à l’aide du portail Azure](essentials/quick-collect-activity-log-portal.md) - Nouvel article.
- [Envoyer le journal d’activité Azure à un espace de travail Log Analytics à l’aide d’un modèle Azure Resource Manager](essentials/quick-collect-activity-log-arm.md) - Nouvel article.

Articles nouveaux et mis à jour suite à la restructuration et au regroupement du contenu sur les journaux de plateforme

- [Archiver des journaux de ressources Azure sur un compte de stockage](./essentials/resource-logs.md#send-to-azure-storage)
- [Schéma d’événement du journal d’activité Azure](essentials/activity-log-schema.md)
- [Journal d’activité Azure](essentials/activity-log.md)
- [Exemples d’interface CLI Azure Monitor](/cli-samples.md)
- [Exemples PowerShell Azure Monitor](/powershell-samples.md)
- [Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor](essentials/rest-api-walkthrough.md)
- [Services et schémas pris en charge pour les journaux de ressource Azure](./essentials/resource-logs-schema.md)
- [Journaux de ressources Azure](essentials/resource-logs.md)
- [Collecter et analyser les journaux d’activités Azure dans Azure Monitor](./essentials/activity-log.md)
- [Collecter les journaux de ressources Azure dans l’espace de travail Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Créer des paramètres de diagnostic pour envoyer des journaux et des métriques de plateforme à différentes destinations](essentials/diagnostic-settings.md)
- [Exporter le journal d’activité Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Vue d’ensemble des journaux de plateforme Azure](essentials/platform-logs-overview.md)
- [Diffuser en continu les journaux de plateforme Azure vers un Event Hub](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Afficher les événements du journal d’activité Azure dans Azure Monitor](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Machines virtuelles
- [Activer Azure Monitor pour machines virtuelles sur le portail Azure](./vm/vminsights-enable-portal.md) - Mise à jour de façon à inclure Azure Arc.
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md) - Mise à jour de façon à inclure Azure Arc.
- [Qu’est-ce qu’Azure Monitor pour machines virtuelles ?](vm/vminsights-overview.md) - Mise à jour de façon à inclure Azure Arc.


### <a name="visualizations"></a>Visualisations
- [Sources de données des classeurs Azure Monitor](visualize/workbooks-data-sources.md) - Ajout d’une section sur les alertes et les points de terminaison personnalisés.
- [Résolution des problèmes liés aux insights basés sur des classeurs Azure Monitor](insights/troubleshoot-workbooks.md) - Nouvel article.
- [Mise à niveau de vos visualisations de tableau de bord Log Analytics](logs/dashboard-upgrade.md) - Nouvel article.



## <a name="may-2020"></a>Mai 2020

### <a name="general"></a>Général

- [FAQ Azure Monitor](faq.md) - Ajout de la section sur les métriques.
- [Clé gérée par le client Azure Monitor](logs/customer-managed-keys.md) - Diverses modifications en vue de la disponibilité générale.
- [Définitions de stratégie intégrées pour Azure Monitor](./policy-reference.md) - Nouvel article.
- [Comptes de stockage appartenant au client pour l’ingestion des journaux](logs/private-storage.md) - Nouvel article.
- [Gérer l’utilisation et les coûts à l’aide des journaux Azure Monitor](logs/manage-cost-storage.md) - Ajout de la facturation proportionnelle pour les clusters.
- [Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Monitor](logs/private-link-security.md) - Nouvel article.


#### <a name="new-resource-manager-template-samples"></a>Nouveaux exemples de modèles Resource Manager 
- [Exemples de modèle Resource Manager pour Azure Monitor](/resource-manager-samples.md)
- [Exemples de modèle Resource Manager pour les groupes d’actions](alerts/resource-manager-action-groups.md)
- [Exemples de modèle Azure Resource Manager pour les agents](agents/resource-manager-agent.md)
- [Exemples de modèle Resource Manager destinés à Azure Monitor pour conteneurs](containers/resource-manager-container-insights.md)
- [Exemples de modèle Resource Manager destinés à Azure Monitor pour machines virtuelles](vm/resource-manager-vminsights.md)
- [Exemples de modèle Resource Manager pour les paramètres de diagnostic](essentials/resource-manager-diagnostic-settings.md)
- [Exemples de modèle Resource Manager pour les espaces de travail Log Analytics](logs/resource-manager-workspace.md)
- [Exemples de modèle Resource Manager pour les requêtes de journal](logs/resource-manager-log-queries.md)
- [Exemples de modèle Resource Manager pour les règles d’alerte de requête de journal](alerts/resource-manager-alerts-log.md)
- [Exemples de modèle Resource Manager pour les règles d’alerte de métrique](alerts/resource-manager-alerts-metric.md)
- [Exemples de modèle Resource Manager pour les classeurs](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>Agents
- [Installer et configurer l’extension Diagnostics Azure pour Windows (WAD)](agents/diagnostics-extension-windows-install.md) - Ajout d’informations sur la configuration de diagnostics.
- [Vue d’ensemble de l’agent Log Analytics](agents/log-analytics-agent.md) - Ajout de versions Linux prises en charge.

### <a name="application-insights"></a>Application Insights

- [Surveiller les applications qui s’exécutent sur Azure Functions avec Application Insights - Azure Monitor](app/monitor-functions.md) - Nouvel article.
- [Surveiller les services Node.js avec Azure Application Insights](app/nodejs.md) - Mises à jour générales, notamment nouvelle section sur la migration à partir de versions antérieures.
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) - Ajout d’adresses IP pour les webhooks et US Government.
- [Surveiller des applications sur Azure Kubernetes Service (AKS) avec Application Insights - Azure Monitor](app/kubernetes-codeless.md) - Nouvel article.
- [Résolution des problèmes liés à l’absence de données pour .NET](app/asp-net-troubleshoot-no-data.md) - Ajout de la section sur la collecte de journaux avec dotnet-trace.
- [Utiliser l’analyse des changements d’application dans Azure Monitor pour rechercher les problèmes liés aux applications web](app/change-analysis.md) - Plusieurs mises à jour dans la fonctionnalité Analyse des changements.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Articles nouveaux et mis à jour pour la préversion des applications basées sur un espace de travail
- [Schéma de ressource Azure Monitor Application Insights basé sur un espace de travail](app/apm-tables.md)
- [Créer une ressource Azure Monitor Application Insights basée sur un espace de travail](app/create-workspace-resource.md)
- [Expression app() dans les requêtes de journal Azure Monitor](logs/app-expression.md)
- [Étendue de requête de journal dans la fonctionnalité Log Analytics d’Azure Monitor](logs/scope.md)
- [Interroger des ressources avec Azure Monitor](logs/cross-workspace-query.md)
- [Propriétés standard dans les enregistrements de journaux Azure Monitor](./logs/log-standard-columns.md)
- [Structure des journaux Azure Monitor](./logs/data-platform-logs.md)





### <a name="containers"></a>Containers
- [Procédure d’activation d’Azure Monitor pour conteneurs](containers/container-insights-onboard.md) - Mise à jour de la table de configuration du pare-feu.
- [Mise à jour d’Azure Monitor pour conteneurs afin d’activer les métriques](containers/container-insights-update-metrics.md) - Mise à jour relative à l’utilisation d’identités managées pour la collecte des métriques.
- [Coût de supervision pour Azure Monitor pour conteneurs](containers/container-insights-cost.md) - Nouvel article.
- [Configurer Live Data avec Azure Monitor pour conteneurs (préversion)](containers/container-insights-livedata-setup.md) - Prise en charge de la nouvelle liaison de rôle de cluster.

### <a name="insights"></a>Insights
- [Azure Monitor pour Azure Cache pour Redis (préversion)](insights/redis-cache-insights-overview.md) - Nouvel article.
- [Surveiller Key Vault avec Azure Monitor pour Key Vault (préversion)](./insights/key-vault-insights-overview.md) - Nouvel article.

### <a name="logs"></a>Journaux d’activité
- [Créer et configurer Log Analytics avec PowerShell](logs/powershell-workspace-configuration.md) - Ajout de la section Résolution des problèmes.
- [Créer un espace de travail Log Analytics dans le portail Azure](logs/quick-create-workspace.md) - Ajout de la section Résolution des problèmes.
- [Créer un espace de travail Log Analytics à l’aide d’Azure CLI](logs/quick-create-workspace-cli.md) - Ajout de la section Résolution des problèmes.
- [Supprimer et récupérer l’espace de travail Azure Log Analytics](logs/delete-workspace.md) -Mise à jour des informations sur la récupération d’un espace de travail supprimé.
- [Fonctions dans les requêtes de journal Azure Monitor](logs/functions.md) - Suppression d’une note sur les fonctions qui ne contiennent pas d’autres fonctions.
- [Structure des journaux Azure Monitor](./logs/data-platform-logs.md) - Clarification des descriptions de propriétés pour la table Application Insights.
- [Utiliser Azure Monitor Logs avec Azure Logic Apps et Power Automate](logs/logicapp-flow-connector.md) - Ajout de la section Limites.
- [Utiliser PowerShell pour créer et configurer un espace de travail Log Analytics](logs/powershell-workspace-configuration.md) - Ajout de la section Résolution des problèmes.


### <a name="metrics"></a>Mesures
- [Métriques prises en charge par Azure Monitor par type de ressource](essentials/metrics-supported.md) - Clarification des métriques invitées et du routage de métriques. 

### <a name="solutions"></a>Solutions
- [Optimiser son environnement Active Directory avec Azure Monitor](insights/ad-assessment.md) - Ajout de Windows Server 2019 aux versions prises en charge.
- [Optimiser son environnement SQL Server avec Azure Monitor](insights/sql-assessment.md) – Ajout des versions prises en charge de SQL Server.


### <a name="virtual-machines"></a>Machines virtuelles
- [Vue d’ensemble de l’activation d’Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md) - Ajout des versions prises en charge d’Ubuntu Server. Ajout de régions prises en charge pour l’espace de travail Log Analytics.
- [Comment créer des graphiques de performances avec Azure Monitor pour machines virtuelles](vm/vminsights-performance.md) - Ajout de la section Limitations pour les métriques non disponibles.

### <a name="visualizations"></a>Visualisations
- [Classeurs Azure Monitor et modèles Azure Resource Manager](visualize/workbooks-automate.md) - Ajout de la mise à jour Resource Manager pour le déploiement d’un modèle de classeur.
- [Groupes dans des classeurs Azure Monitor](platform/workbooks-groups.md) - Nouvel article.
- [Classeurs Azure Monitor - Transformer des données JSON avec JSONPath](visualize/workbooks-jsonpath.md) - Nouvel article.


## <a name="april-2020"></a>Avril 2020

### <a name="general"></a>Général

- [Clé gérée par le client dans Azure Monitor](logs/customer-managed-keys.md) - Ajout d’une section sur les opérations asynchrones
- [Gérer les espaces de travail Log Analytics dans Azure Monitor](logs/manage-access.md) – Mises à jour apportées aux sections sur les journaux personnalisés.

### <a name="alerts"></a>Alertes

- [Règles d’action pour les alertes Azure Monitor](alerts/alerts-action-rules.md) - Ajout d’une vidéo.
- [Vue d’ensemble des alertes et de la supervision des notifications dans Azure](alerts/alerts-overview.md) - Ajout d’une vidéo.

### <a name="application-insights"></a>Application Insights

- [Cartographie d’application dans Azure Application Insights](app/app-map.md) - Ajout de la configuration des noms de rôles de cloud pour l’agent Java.
- [Informations de référence sur l’API de l’agent .NET d’Azure Application Insights](app/status-monitor-v2-api-reference.md) - Regroupement des informations de référence sur l’API.
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) - Mise à jour des adresses IP pour les API App Insights et Log Analytics, les webhooks de groupes d’actions, Azure US Government.
- [Superviser les applications Java en tout lieu](app/java-standalone-config.md) - Nouvel article.
- [Superviser les applications Java sur n’importe quel environnement](app/java-in-process-agent.md) - Nouvel article.
- [Superviser les applications Java qui s’exécutent dans n’importe quel environnement](app/java-standalone-arguments.md) - Nouvel article.
- [Superviser les applications Java s’exécutant localement](app/java-on-premises.md) - Nouvel article.
- [Supprimer Application Insights dans Visual Studio](app/remove-application-insights.md) - Nouvel article.
- [Échantillonnage de télémétrie dans Azure Application Insights](app/sampling.md) - Correction de l’échantillonnage à débit fixe en Python.

### <a name="containers"></a>Containers

- [Configurer Azure Red Hat OpenShift v4.x avec Azure Monitor pour conteneurs](containers/container-insights-azure-redhat4-setup.md) - Nouvel article.
- [Guide pratique pour corriger manuellement les problèmes de synchronisation de ServiceNow](alerts/itsmc-resync-servicenow.md) - Nouvel article.
- [Guide pratique pour arrêter la supervision de votre cluster Azure et Red Hat OpenShift v4](containers/container-insights-optout-openshift-v4.md) - Nouvel article.
- [Guide pratique pour arrêter la supervision de votre cluster Azure Red Hat OpenShift v3](containers/container-insights-optout-openshift-v3.md) - Nouvel article.
- [Guide pratique pour arrêter la supervision de votre cluster Kubernetes hybride](containers/container-insights-optout-hybrid.md) - Nouvel article.

### <a name="insights"></a>Insights

- [Superviser les coffres de clés Azure avec Azure Monitor pour les coffres de clés (préversion)](insights/key-vault-insights-overview.md) - Nouvel article.

### <a name="logs"></a>Journaux d’activité

- [Limites du service Azure Monitor](service-limits.md) - Ajout de la limitation des requêtes utilisateur.
- [Gérer l’utilisation et les coûts à l’aide des journaux Azure Monitor](logs/manage-cost-storage.md) - Ajout de la facturation pour les clusters de journaux. Ajout d’une requête Kusto pour permettre aux clients ayant un niveau tarifaire hérité Par nœud de déterminer s’ils doivent passer à un niveau de Réservation par capacité ou Par Go.

### <a name="metrics"></a>Mesures

- [Fonctionnalités avancées d’Azure Metrics Explorer](essentials/metrics-charts.md) - Ajout de la section sur l’agrégation.

### <a name="workbooks"></a>Workbooks

- [Classeurs Azure Monitor et modèles Azure Resource Manager](visualize/workbooks-automate.md) - Ajout du modèle Resource Manager ajouté pour le déploiement d’un modèle de classeur.

## <a name="march-2020"></a>Mars 2020

### <a name="general"></a>Général

- [Vue d’ensemble d’Azure Monitor](overview.md) - Ajout de la vidéo de présentation d’Azure Monitor.
- [Configuration de clés gérées par le client dans Azure Monitor](logs/customer-managed-keys.md) – Mises à jour générales.
- [Référence de données Azure Monitor](/azure/azure-monitor/reference/) - Nouveau site.

### <a name="alerts"></a>Alertes

- [Créer, afficher et gérer des alertes de journal d’activité dans Azure Monitor](alerts/alerts-activity-log.md) - Explication supplémentaire du modèle Resource Manager.
- [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor.](alerts/alerts-metric-overview.md) Mise à jour pour la prise en charge de Government.
- [Résolution des problèmes liés aux alertes et notifications Azure Monitor](alerts/alerts-troubleshoot.md) - Nouvel article.

### <a name="application-insights"></a>Application Insights

- [Automatiser Azure Application Insights avec PowerShell](app/powershell.md) | Ajout d’exemples ARMClient.
- [Exportation continue de télémétrie depuis Application Insights](app/export-telemetry.md) - Ajout d’une table avec les détails de la structure d’exportation.
- [Activer le Débogueur de capture instantanée pour les applications .NET dans Azure App Service](app/snapshot-debugger-appservice.md) - Ajout d’un exemple de modèle Resource Manager.
- [Gérer l’utilisation et les coûts pour Azure Application Insights](app/pricing.md)| Ajout d’informations sur l’alerte du plafond de données.
- [Superviser les applications Python avec Azure Monitor (préversion)](app/opencensus-python.md) - Ajout de métriques standard.
- [Prise en charge du mappage de source pour les applications JavaScript – Azure Monitor Application Insights](app/source-map-support.md) - Nouvel article.

### <a name="containers"></a>Containers

- [Questions fréquentes (FAQ) Azure Monitor](faq.md) - Mise à jour relative à Azure Monitor pour conteneurs.
- [Configurer la supervision de GPU avec Azure Monitor pour conteneurs](containers/container-insights-gpu-monitoring.md) - Nouvel article.

### <a name="insights"></a>Insights

- [Solution de gestion Office 365 dans Azure](insights/solution-office-365.md) - Mise à jour de la date de dépréciation.

### <a name="logs"></a>Journaux d’activité

- [Optimiser les requêtes de journal dans Azure Monitor](logs/query-optimization.md) - Ajout d’une condition d’UC pour l’analyse XML et JSON.
- [Supprimer et récupérer un espace de travail Azure Log Analytics](logs/delete-workspace.md) – Ajout de la résolution des problèmes.
- [Utiliser les journaux Azure Monitor avec Azure Logic Apps et Power Automate](logs/logicapp-flow-connector.md) - Mise à jour pour le nouveau connecteur Azure Monitor.

### <a name="metrics"></a>Mesures

- [Dépréciation des métriques de disque dans le portail Azure](essentials/portal-disk-metrics-deprecation.md) - Nouvel article.
- [Tutoriel - Créer un graphique de métriques dans Azure Monitor](essentials/tutorial-metrics-explorer.md) - Ajout d’une vidéo.

### <a name="platform-logs"></a>Journaux de plateforme

- [Collecter et analyser le journal d’activité Azure dans Azure Monitor](./essentials/activity-log.md) - Réécriture pour une meilleure explication de la collecte du journal d’activité avec des paramètres de diagnostic.

### <a name="virtual-machines"></a>Machines virtuelles

- [Superviser des machines virtuelles Azure avec Azure Monitor](vm/monitor-vm-azure.md) - Nouvel article.
- [Démarrage rapide : Superviser des machines virtuelles Azure avec Azure Monitor](vm/quick-monitor-azure-vm.md) - Mise à jour permettant d’ajouter Azure Monitor pour machines virtuelles.
- [Alertes Azure Monitor pour machines virtuelles](vm/vminsights-alerts.md) - Nouvel article.
- [Vue d’ensemble Activer Azure Monitor pour machines virtuelles](vm/vminsights-enable-overview.md) - Mise à jour des liens de téléchargement d’agent.

Mises à jour générales relatives à la disponibilité générale d’Azure Monitor pour machines virtuelles

- [Qu’est-ce qu’Azure Monitor pour machines virtuelles ?](vm/vminsights-overview.md)
- [Questions fréquentes sur Azure Monitor pour machines virtuelles (GA)](vm/vminsights-ga-release-faq.md) 
- [Activer Azure Monitor pour machines virtuelles à l’aide d’Azure Policy](./vm/vminsights-enable-policy.md) 
- [Créer des graphiques de performances avec Azure Monitor pour machines virtuelles](vm/vminsights-performance.md)
- [Interroger les journaux des requêtes à partir d’Azure Monitor pour machines virtuelles](vm/vminsights-log-search.md)
- [Visualiser les dépendances d’applications avec Azure Monitor pour machines virtuelles](vm/vminsights-maps.md) 

### <a name="visualizations"></a>Visualisations

- [Visualisation des données à partir d’Azure Monitor](visualizations.md) - Mise à jour permettant de noter la dépréciation planifiée du concepteur de vues.

## <a name="february-2020"></a>Février 2020

### <a name="agents"></a>Agents

Plusieurs mises à jour effectuées dans le cadre de la réécriture du contenu sur l’extension Diagnostics.

- [Vue d’ensemble des agents de surveillance Azure](agents/agents-overview.md) - Restructuration des tables pour clarifier les fonctionnalités spécifiques à chaque agent.
- [Vue d’ensemble de l’extension Diagnostics Azure](agents/diagnostics-extension-overview.md) - Réécriture complète.
- [Utilisation d’un Stockage Blob pour IIS et d’un Stockage Table pour les événements dans Azure Monitor](essentials/diagnostics-extension-logs.md) - Réécriture générale à des fins de mise à jour et de clarification.
- [Installer et configurer l’extension Diagnostics Azure pour Windows (WAD)](agents/diagnostics-extension-windows-install.md) - Nouvel article. 
- [Schéma d’extension de diagnostic Windows](agents/diagnostics-extension-schema-windows.md) - Réorganisation.
- [Envoyer des données provenant de l’extension Diagnostics Azure pour Windows à Azure Event Hubs](agents/diagnostics-extension-stream-event-hubs.md) - Réécriture et mise à jour complètes.
- [Stocker et afficher des données de diagnostic dans le stockage Azure](../cloud-services/diagnostics-extension-to-storage.md) - Réécriture et mise à jour complètes.
- [Extension de machine virtuelle Log Analytics pour Windows](../virtual-machines/extensions/oms-windows.md) - Clarification de la relation avec l’agent Log Analytics.
- [Extension de machine virtuelle Azure Monitor pour Linux](../virtual-machines/extensions/oms-linux.md) - Clarification de la relation avec l’agent Log Analytics.

### <a name="application-insights"></a>Application Insights

- [Chaînes de connexion dans Azure Application Insights](app/sdk-connection-string.md) - Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pour conteneurs

- [Intégrer Azure Active Directory dans Azure Kubernetes Service](../aks/azure-ad-integration-cli.md) – Ajout d’une note concernant la création d’une application cliente pour prendre en charge un cluster sur lequel Kubernetes RBAC est activé, afin de prendre en charge Azure Monitor pour les conteneurs.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles

- [Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale)](vm/vminsights-ga-release-faq.md) - Modification concernant le mode de stockage des données de performance.

#### <a name="office-365"></a>Office 365

- [Solution de gestion Office 365 dans Azure](insights/solution-office-365.md) - Mise à jour de la date de dépréciation.


### <a name="logs"></a>Journaux d’activité

- [Optimiser les requêtes de journal dans Azure Monitor](logs/query-optimization.md) - Nouvel article.
- [Gérer l’utilisation et les coûts pour les journaux Azure Monitor](logs/manage-cost-storage.md) - Amélioration des exemples de requête pour une meilleure compréhension de l’utilisation.

### <a name="metrics"></a>Mesures

- [Métriques de plateforme Azure Monitor exportables par le biais des paramètres de diagnostic](essentials/metrics-supported-export-diagnostic-settings.md) - Ajout d’une section portant sur la modification du comportement pour les valeurs Null et zéro.

### <a name="visualizations"></a>Visualisations

Plusieurs nouveaux articles d’aide sur le concepteur de vues, concernant les conversions en classeurs.

- [Guide de transition : du concepteur de vues Azure Monitor aux classeurs](visualize/view-designer-conversion-overview.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Options de conversion en classeurs](visualize/view-designer-conversion-options.md) - Nouvel article.
- [Concepteur de vues Azure Monitor pour les conversions de vignettes de classeurs](visualize/view-designer-conversion-tiles.md) - Nouvel article.
- [Récapitulatif et accès pour la conversion du Concepteur de vues Azure Monitor en classeurs](visualize/view-designer-conversion-access.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Tâches courantes liées à la conversion de vues en classeurs](visualize/view-designer-conversion-tasks.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Exemples de conversions en classeurs](visualize/view-designer-conversion-examples.md) - Nouvel article.

## <a name="january-2020"></a>Janvier 2020

### <a name="general"></a>Général

- [Quels sont les éléments supervisés par Azure Monitor ?](monitor-reference.md) – Nouvel article.

### <a name="agents"></a>Agents

- [Collecter des données de journal avec l’agent Log Analytics](agents/log-analytics-agent.md) – Mise à jour du tableau de la section Configuration requise du pare-feu réseau.

### <a name="alerts"></a>Alertes

- [Créer et gérer des groupes d’actions sur le Portail Azure](alerts/action-groups.md) – Suppression d’un paramètre qui n’est plus obligatoire pour les fonctions v2.
- [Créer une alerte de métrique avec un modèle Resource Manager](alerts/alerts-metric-create-templates.md) – Ajout d’un exemple pour le paramètre *ignoreDataBefore*.  Ajout de contraintes concernant les règles multicritères.
- [Utilisation de l’API REST d’alerte Log Analytics](alerts/api-alerts.md) – Exemple JSON corrigé.

### <a name="application-insights"></a>Application Insights

- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) – Mise à jour de la section Tests de disponibilité concernant la manière d’ajouter une règle de port d’entrée pour autoriser le trafic en utilisant des groupes de sécurité réseau Azure.
- [Résoudre les problèmes liés à Azure Application Insights Profiler](app/profiler-troubleshooting.md) – Mise à jour générale des solutions de dépannage.
- [Échantillonnage de la télémétrie dans Azure Application Insights](app/sampling.md) – Article mis à jour et restructuré sur la base des commentaires de clients de façon à améliorer la lisibilité.

### <a name="data-security"></a>Sécurité des données

- [Configuration de clés gérées par le client dans Azure Monitor](logs/customer-managed-keys.md) – Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pour conteneurs

- [Configurer la collecte de données de l’agent pour Azure Monitor pour les conteneurs](containers/container-insights-agent-config.md) – Ajout de détails concernant la mise à niveau de l’agent dans Azure Red Hat OpenShift et ajout d’informations supplémentaires pour distinguer les méthodes de mise à niveau de l’agent.
- [Créer des alertes pour des problèmes de performances dans Azure Monitor pour les conteneurs](./containers/container-insights-log-alerts.md) – Révision des informations et mise à jour de la procédure de création d’une alerte sur les données de performances stockées dans un espace de travail qui se déclenche en fonction du contexte de l’espace de travail.
- [Supervision Kubernetes avec Azure Monitor pour les conteneurs](containers/container-insights-analyze.md) – Mise à jour de l’article de présentation et de l’article d’analyse concernant la prise en charge des clusters Windows Kubernetes.
- [Configurer des clusters Azure Red Hat OpenShift avec Azure Monitor pour les conteneurs](containers/container-insights-azure-redhat-setup.md) – Ajout de détails concernant la mise à niveau de l’agent dans Azure Red Hat OpenShift et ajout d’informations supplémentaires pour distinguer les méthodes de mise à niveau de l’agent.
- [Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs](containers/container-insights-hybrid-setup.md) – Mise à jour pour prendre en compte l’ajout de la prise en charge de secure port:10250 avec cAdvisor de Kubelet.
- [Guide pratique pour la gestion de l’agent Azure Monitor pour les conteneurs](containers/container-insights-manage-agent.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.
- [Configurer la capture des métriques Prometheus avec Azure Monitor pour les conteneurs](containers/container-insights-prometheus-integration.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction des métriques avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.
- [Mise à jour d’Azure Monitor pour les conteneurs afin d’activer les métriques](containers/container-insights-update-metrics.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction des métriques avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles

- [Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale)](vm/vminsights-ga-release-faq.md) – Ajout d’informations concernant la mise à niveau de l’espace de travail et des agents vers une nouvelle version.

#### <a name="office-365"></a>Office 365

- [Solution de gestion Office 365 dans Azure (préversion)](insights/solution-office-365.md) – Ajout de détails et des questions fréquentes (FAQ) sur la migration vers la solution Office 365 dans Azure Sentinel. Suppression de la section relative à l’intégration.

### <a name="logs"></a>Journaux d’activité

- [Gérer les espaces de travail Log Analytics dans Azure Monitor](logs/manage-access.md) – Mises à jour apportées aux non-actions.
- [Gérer l’utilisation et les coûts des journaux Azure Monitor](logs/manage-cost-storage.md) – Ajout de précisions concernant le calcul du volume de données dans la section Modèle de tarification.
- [Utiliser des modèles Azure Resource Manager pour créer et configurer un espace de travail Log Analytics](./logs/resource-manager-workspace.md) – Modèle mis à jour avec les nouveaux niveaux tarifaires.

### <a name="platform-logs"></a>Journaux de plateforme

- [Collecter le journal d’activité Azure avec des paramètres de diagnostic – Azure Monitor](./essentials/activity-log.md) – Informations supplémentaires concernant les propriétés modifiées.
- [Exporter le journal d’activité Azure](./essentials/activity-log.md#legacy-collection-methods) – Mis à jour par rapport aux modifications apportées à l’interface utilisateur. 

## <a name="december-2019"></a>Décembre 2019

### <a name="agents"></a>Agents

- [Connecter des ordinateurs Linux à Azure Monitor](agents/agent-linux.md) – Nouvel article.

### <a name="alerts"></a>Alertes

- [Créer une alerte de métrique avec un modèle Resource Manager](alerts/alerts-metric-create-templates.md) – Ajout d’un exemple pour le métrique personnalisée.
- [Création d’alertes avec des seuils dynamiques dans Azure Monitor](alerts/alerts-dynamic-thresholds.md) – Ajout d’une section relative à l’interprétation des graphiques de seuils dynamiques.
- [Vue d’ensemble des alertes et de la supervision des notifications dans Azure](alerts/alerts-overview.md) – Mise à jour concernant la requête Resource Graph.
- [Ressources prises en charge pour les alertes de métrique dans Azure Monitor](alerts/alerts-metric-near-real-time.md) – Mise à jour concernant les métriques et les dimensions prises en charge.
- [Passer de l’API Alerte héritée de Log Analytics à la nouvelle API Alertes Azure](alerts/alerts-log-api-switch.md) – Ajout d’une remarque concernant la modification du nom de l’alerte.
- [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor.](alerts/alerts-metric-overview.md) – Ajout de types de ressources pris en charge pour une supervision à la bonne échelle.

### <a name="application-insights"></a>Application Insights

- [Application Insights pour les applications Service Worker (applications non HTTP)](app/worker-service.md) – Ajout du niveau de journalisation par défaut au code C#. Mise à jour de la version de référence du package.
- [Référence sur ApplicationInsights.config – Azure](app/configuration-with-applicationinsights-config.md) – Mise à jour de l’exemple de code.
- [Automatiser Azure Application Insights avec PowerShell](app/powershell.md) – Mise à jour du modèle Resource Manager.
- [Créer une ressource Azure Application Insights](app/create-new-resource.md) – Ajout d’une remarque concernant le nom global unique.
- [Diagnostiquer avec les flux de métriques temps réel – Azure Application Insights](app/live-stream.md) – Mise à concernant la version exigée du SDK ASP.NET Core .
- [Compteurs d’événements dans Application Insights](app/eventcounters.md) – Catégorie et tableau mis à jour pour customMetrics.
- [Explorer les journaux de suivi Java dans Azure Application Insights](app/java-trace-logs.md) – Ajout de la configuration du seuil de journalisation de l’agent.
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) – Mise à jour des adresses IP pour les flux de métriques temps réel.
- [Superviser les performances d’Azure App Service](app/azure-web-apps.md) – Ajout de la prise en charge d’ASP.NET Core 3.0. 
- [Superviser les applications avec Azure Monitor (préversion)](app/opencensus-python.md) – Ajout de précisions concernant le mappage du schéma Python OpenCensus au schéma Azure Monitor
- [Notes de publication d’Azure Application Insights](app/release-notes.md) – Ajout de remarques concernant les anciennes versions.
- [Canaux de télémétrie dans Azure Application Insights](app/telemetry-channels.md) – Mise à jour concernant l’abandon des données à la suite d’une perte de connexion prolongée.
- [Échantillonnage de la télémétrie dans Azure Application Insights](app/sampling.md) – Correction de l’extrait de code pour l’élément TelemetryInitializer personnalisé.
- [Résoudre les problèmes liés à Application Insights dans un projet web Java](app/java-troubleshoot.md) – Suppression de l’information concernant la non prise en charge de la collecte de dépendances dans JDK 9.

### <a name="insights-and-solutions"></a>Insights et solutions

- [Questions fréquentes sur Azure Monitor pour les conteneurs](./faq.md) – Ajout d’une question concernant les champs Image et Name.
- [Solution Azure SQL Analytics dans Azure Monitor](insights/azure-sql.md) – Mise à jour concernant la prise en charge de Attente de la base de données pour les instances Managed Instance.
- [Configurer Azure Monitor pour la collecte de données de l’agent de conteneurs](containers/container-insights-agent-config.md) – Ajout du paramètre pour enrich_container_logs.
- [Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs](containers/container-insights-hybrid-setup.md) – Ajout d’une section Résolution des problèmes.
- [Superviser l’état de la réplication Active Directory avec Azure Monitor](insights/ad-replication-status.md) – Mise à jour des prérequis .NET Microsoft.
- [Solution Network Performance Monitor dans Azure](insights/network-performance-monitor.md) – Ajout des régions prises en charge.
- [Optimiser son environnement Active Directory avec Azure Monitor](insights/ad-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Optimiser son environnement SQL Server avec Azure Monitor](insights/sql-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Optimiser son environnement System Center Operations Manager avec Azure Log Analytics](insights/scom-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Connexions prises en charge avec le connecteur de gestion des services informatiques dans Azure Log Analytics](alerts/itsmc-connections.md) – Ajout de New York à l’ID client et à la clé secrète client prérequis.

### <a name="logs"></a>Journaux d’activité

- [Supprimer et récupérer un espace de travail Azure Log Analytics](logs/delete-workspace.md) – Ajout de la méthode PowerShell.
- [Conception de votre déploiement de journaux Azure Monitor](logs/design-logs-deployment.md) – Augmentation du débit d’ingestion des données pour un espace de travail.

### <a name="metrics"></a>Mesures

- [Métriques de plateforme Azure Monitor exportables par le biais des paramètres de diagnostic](essentials/metrics-supported-export-diagnostic-settings.md) – Nouvel article.

### <a name="platform-logs"></a>Journaux de plateforme

Plusieurs articles ont été mis à jour après la restructuration du contenu relatif aux journaux de plateforme du fait de la nouvelle fonctionnalité de configuration du journal d’activité à l’aide des paramètres de diagnostic.

- [Archiver des journaux de ressources Azure sur un compte de stockage](./essentials/resource-logs.md#send-to-azure-storage)
- [Schéma d’événement du journal d’activité Azure](essentials/activity-log-schema.md)
- [Limites du service Azure Monitor](service-limits.md)
- [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics](./essentials/activity-log.md)
- [Collecter le journal d’activité Azure avec les paramètres de diagnostic (préversion) – Azure Monitor](./essentials/activity-log.md)
- [Collecter les journaux d’activité Azure dans un espace de travail Log Analytics auprès de locataire Azure](./essentials/activity-log.md)
- [Collecter les journaux de ressources Azure dans l’espace de travail Log Analytics](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Créer un paramètre de diagnostic dans Azure à l’aide d’un modèle Resource Manager](./essentials/resource-manager-diagnostic-settings.md)
- [Créer un paramètre de diagnostic pour collecter les journaux et les métriques dans Azure](essentials/diagnostic-settings.md)
- [Exporter le journal d’activité Azure](./essentials/activity-log.md#legacy-collection-methods)
- [Vue d’ensemble des journaux de plateforme Azure](essentials/platform-logs-overview.md)
- [Envoyer en streaming des données de supervision Azure vers un hub d’événements](essentials/stream-monitoring-data-event-hubs.md)
- [Diffuser en continu les journaux de plateforme Azure vers un Event Hub](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Guides de démarrages rapides et tutoriels

- [Créer un graphique de métriques dans Azure Monitor](essentials/tutorial-metrics-explorer.md) – Nouvel article.
- [Collecter des journaux de ressources à partir d’une ressource Azure et les analyser avec Azure Monitor](essentials/tutorial-resource-logs.md) – Nouvel article.
- [Superviser une ressource Azure avec Azure Monitor](essentials/quick-monitor-azure-resource.md) – Nouvel article.
   
## <a name="next-steps"></a>Étapes suivantes

- Pour contribuer à la documentation Azure Monitor, consultez le [guide du contributeur de documents](/contribute/).
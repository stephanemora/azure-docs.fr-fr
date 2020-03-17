---
title: Nouveautés dans la documentation Azure Monitor
description: Chaque mois, la documentation Azure Monitor fait l’objet de mises à jour importantes.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: b42acdf64612da6837bc67752f7a22169ddef7e2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673327"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Nouveautés dans la documentation Azure Monitor
Cet article liste les nouveaux articles Azure Monitor et ceux qui ont fait l’objet d’une mise à jour importante. Il sera actualisé la première semaine de chaque mois pour inclure les mises à jour d’articles du mois précédent.

## <a name="march-2020"></a>Mars 2020

### <a name="agents"></a>Agents
Plusieurs mises à jour effectuées dans le cadre de la réécriture du contenu sur l’extension Diagnostics.

- [Vue d’ensemble des agents de surveillance Azure](platform/agents-overview.md) - Restructuration des tables pour clarifier les fonctionnalités spécifiques à chaque agent.
- [Vue d’ensemble de l’extension Diagnostics Azure](platform/diagnostics-extension-overview.md) - Réécriture complète.
- [Utilisation d’un Stockage Blob pour IIS et d’un Stockage Table pour les événements dans Azure Monitor](platform/diagnostics-extension-logs.md) - Réécriture générale à des fins de mise à jour et de clarification.
- [Installer et configurer l’extension Diagnostics Azure pour Windows (WAD)](platform/diagnostics-extension-windows-install.md) - Nouvel article. 
- [Schéma d’extension de diagnostic Windows](platform/diagnostics-extension-schema-windows.md) - Réorganisation.
- [Envoyer des données provenant de l’extension Diagnostics Azure pour Windows à Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) - Réécriture et mise à jour complètes.
- [Stocker et afficher des données de diagnostic dans le stockage Azure](platform/diagnostics-extension-to-storage.md) - Réécriture et mise à jour complètes.
- [Extension de machine virtuelle Log Analytics pour Windows](../virtual-machines/extensions/oms-windows.md) - Clarification de la relation avec l’agent Log Analytics.
- [Extension de machine virtuelle Azure Monitor pour Linux](../virtual-machines/extensions/oms-linux.md) - Clarification de la relation avec l’agent Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Chaînes de connexion dans Azure Application Insights](app/sdk-connection-string.md) - Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pour conteneurs
- [Intégrer Azure Active Directory dans Azure Kubernetes Service](../aks/azure-ad-integration.md) - Ajout d’une note concernant la création d’une application cliente pour prendre en charge le cluster RBAC afin de prendre en charge Azure Monitor pour les conteneurs.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles
- [Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale)](insights/vminsights-ga-release-faq.md) - Modification concernant le mode de stockage des données de performance.

#### <a name="office-365"></a>Office 365
- [Solution de gestion Office 365 dans Azure](insights/solution-office-365.md) - Mise à jour de la date de dépréciation.


### <a name="logs"></a>Journaux d’activité
- [Optimiser les requêtes de journal dans Azure Monitor](log-query/query-optimization.md) - Nouvel article.
- [Gérer l’utilisation et les coûts pour les journaux Azure Monitor](platform/manage-cost-storage.md) - Amélioration des exemples de requête pour une meilleure compréhension de l’utilisation.

### <a name="metrics"></a>Mesures
- [Métriques de plateforme Azure Monitor exportables par le biais des paramètres de diagnostic](platform/metrics-supported-export-diagnostic-settings.md) - Ajout d’une section portant sur la modification du comportement pour les valeurs Null et zéro.


### <a name="visualizations"></a>Visualisations
Plusieurs nouveaux articles d’aide sur le concepteur de vues, concernant les conversions en classeurs.

- [Guide de transition : du concepteur de vues Azure Monitor aux classeurs](platform/view-designer-conversion-overview.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Options de conversion en classeurs](platform/view-designer-conversion-options.md) - Nouvel article.
- [Concepteur de vues Azure Monitor pour les conversions de vignettes de classeurs](platform/view-designer-conversion-tiles.md) - Nouvel article.
- [Récapitulatif et accès pour la conversion du Concepteur de vues Azure Monitor en classeurs](platform/view-designer-conversion-access.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Tâches courantes liées à la conversion de vues en classeurs](platform/view-designer-conversion-tasks.md) - Nouvel article.
- [Concepteur de vues Azure Monitor - Exemples de conversions en classeurs](platform/view-designer-conversion-examples.md) - Nouvel article.




## <a name="january-2020"></a>Janvier 2020

### <a name="general"></a>Général
- [Quels sont les éléments supervisés par Azure Monitor ?](monitor-reference.md) – Nouvel article.

### <a name="agents"></a>Agents
- [Collecter des données de journal avec l’agent Log Analytics](platform/log-analytics-agent.md) – Mise à jour du tableau de la section Configuration requise du pare-feu réseau.


### <a name="alerts"></a>Alertes
- [Créer et gérer des groupes d’actions sur le Portail Azure](platform/action-groups.md) – Suppression d’un paramètre qui n’est plus obligatoire pour les fonctions v2.
- [Créer une alerte de métrique avec un modèle Resource Manager](platform/alerts-metric-create-templates.md) – Ajout d’un exemple pour le paramètre *ignoreDataBefore*.  Ajout de contraintes concernant les règles multicritères.
- [Utilisation de l’API REST d’alerte Log Analytics](platform/api-alerts.md) – Exemple JSON corrigé.


### <a name="application-insights"></a>Application Insights
- [Adresses IP utilisées par Application Insights et Log Analytics](app/ip-addresses.md) – Mise à jour de la section Tests de disponibilité concernant la manière d’ajouter une règle de port d’entrée pour autoriser le trafic en utilisant des groupes de sécurité réseau Azure.
- [Résoudre les problèmes liés à Azure Application Insights Profiler](app/profiler-troubleshooting.md) – Mise à jour générale des solutions de dépannage.
- [Échantillonnage de la télémétrie dans Azure Application Insights](app/sampling.md) – Article mis à jour et restructuré sur la base des commentaires de clients de façon à améliorer la lisibilité.


### <a name="data-security"></a>Sécurité des données
- [Configuration de clés gérées par le client dans Azure Monitor](platform/customer-managed-keys.md) – Nouvel article.

### <a name="insights-and-solutions"></a>Insights et solutions

#### <a name="azure-monitor-for-containers"></a>Azure Monitor pour conteneurs
- [Configurer la collecte de données de l’agent pour Azure Monitor pour les conteneurs](insights/container-insights-agent-config.md) – Ajout de détails concernant la mise à niveau de l’agent dans Azure Red Hat OpenShift et ajout d’informations supplémentaires pour distinguer les méthodes de mise à niveau de l’agent.
- [Créer des alertes pour des problèmes de performances dans Azure Monitor pour les conteneurs](insights/container-insights-alerts.md) – Révision des informations et mise à jour de la procédure de création d’une alerte sur les données de performances stockées dans un espace de travail qui se déclenche en fonction du contexte de l’espace de travail.
- [Supervision Kubernetes avec Azure Monitor pour les conteneurs](insights/container-insights-analyze.md) – Mise à jour de l’article de présentation et de l’article d’analyse concernant la prise en charge des clusters Windows Kubernetes.
- [Configurer des clusters Azure Red Hat OpenShift avec Azure Monitor pour les conteneurs](insights/container-insights-azure-redhat-setup.md) – Ajout de détails concernant la mise à niveau de l’agent dans Azure Red Hat OpenShift et ajout d’informations supplémentaires pour distinguer les méthodes de mise à niveau de l’agent.
- [Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs](insights/container-insights-hybrid-setup.md) – Mise à jour pour prendre en compte l’ajout de la prise en charge de secure port:10250 avec cAdvisor de Kubelet.
- [Guide pratique pour la gestion de l’agent Azure Monitor pour les conteneurs](insights/container-insights-manage-agent.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.
- [Configurer la capture des métriques Prometheus avec Azure Monitor pour les conteneurs](insights/container-insights-prometheus-integration.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction des métriques avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.
- [Mise à jour d’Azure Monitor pour les conteneurs afin d’activer les métriques](insights/container-insights-update-metrics.md) – Mise à jour des détails concernant le comportement et la configuration de l’extraction des métriques avec Azure Red Hat OpenShift par rapport à d’autres types de clusters Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles
- [Questions fréquentes sur Azure Monitor pour machines virtuelles (version en disponibilité générale)](insights/vminsights-ga-release-faq.md) – Ajout d’informations concernant la mise à niveau de l’espace de travail et des agents vers une nouvelle version.

#### <a name="office-365"></a>Office 365
- [Solution de gestion Office 365 dans Azure (préversion)](insights/solution-office-365.md) – Ajout de détails et des questions fréquentes (FAQ) sur la migration vers la solution Office 365 dans Azure Sentinel. Suppression de la section relative à l’intégration.



### <a name="logs"></a>Journaux d’activité
- [Gérer les espaces de travail Log Analytics dans Azure Monitor](platform/manage-access.md) – Mises à jour apportées aux non-actions.
- [Gérer l’utilisation et les coûts des journaux Azure Monitor](platform/manage-cost-storage.md) – Ajout de précisions concernant le calcul du volume de données dans la section Modèle de tarification.
- [Utiliser des modèles Azure Resource Manager pour créer et configurer un espace de travail Log Analytics](platform/template-workspace-configuration.md) – Modèle mis à jour avec les nouveaux niveaux tarifaires.


### <a name="platform-logs"></a>Journaux de plateforme
- [Collecter le journal d’activité Azure avec des paramètres de diagnostic – Azure Monitor](platform/diagnostic-settings-legacy.md) – Informations supplémentaires concernant les propriétés modifiées.
- [Exporter le journal d’activité Azure](platform/activity-log-export.md) – Mis à jour par rapport aux modifications apportées à l’interface utilisateur. 





## <a name="december-2019"></a>Décembre 2019

### <a name="agents"></a>Agents
- [Connecter des ordinateurs Linux à Azure Monitor](platform/agent-linux.md) – Nouvel article.

### <a name="alerts"></a>Alertes
- [Créer une alerte de métrique avec un modèle Resource Manager](platform/alerts-metric-create-templates.md) – Ajout d’un exemple pour le métrique personnalisée.
- [Création d’alertes avec des seuils dynamiques dans Azure Monitor](platform/alerts-dynamic-thresholds.md) – Ajout d’une section relative à l’interprétation des graphiques de seuils dynamiques.
- [Vue d’ensemble des alertes et de la supervision des notifications dans Azure](platform/alerts-overview.md) – Mise à jour concernant la requête Resource Graph.
- [Ressources prises en charge pour les alertes de métrique dans Azure Monitor](platform/alerts-metric-near-real-time.md) – Mise à jour concernant les métriques et les dimensions prises en charge.
- [Passer de l’API Alerte héritée de Log Analytics à la nouvelle API Alertes Azure](platform/alerts-log-api-switch.md) – Ajout d’une remarque concernant la modification du nom de l’alerte.
- [Comprendre le fonctionnement des alertes de métrique dans Azure Monitor.](platform/alerts-metric-overview.md) – Ajout de types de ressources pris en charge pour une supervision à la bonne échelle.

### <a name="application-insights"></a>Application Insights
- [Application Insights pour les applications Service Worker (applications non HTTP)](app/worker-service.md) – Ajout du niveau de journalisation par défaut au code C#. Mise à jour de la version de référence du package.
- [Référence sur ApplicationInsights.config – Azure](app/configuration-with-applicationinsights-config.md) – Mise à jour de l’exemple de code.
- [Automatiser Azure Application Insights avec PowerShell](app/powershell.md) – Mise à jour du modèle Resource Manager.
- [Packages NuGet Application Insights Azure Monitor](app/nuget.md) – Mise à jour des versions de packages.
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
- [Questions fréquentes sur Azure Monitor pour les conteneurs](insights/container-insights-faq.md) – Ajout d’une question concernant les champs Image et Name.
- [Solution Azure SQL Analytics dans Azure Monitor](insights/azure-sql.md) – Mise à jour concernant la prise en charge de Attente de la base de données pour les instances Managed Instance.
- [Configurer Azure Monitor pour la collecte de données de l’agent de conteneurs](insights/container-insights-agent-config.md) – Ajout du paramètre pour enrich_container_logs.
- [Configurer des clusters Kubernetes hybrides avec Azure Monitor pour les conteneurs](insights/container-insights-hybrid-setup.md) – Ajout d’une section Résolution des problèmes.
- [Superviser l’état de la réplication Active Directory avec Azure Monitor](insights/ad-replication-status.md) – Mise à jour des prérequis .NET Microsoft.
- [Solution Network Performance Monitor dans Azure](insights/network-performance-monitor.md) – Ajout des régions prises en charge.
- [Optimiser son environnement Active Directory avec Azure Monitor](insights/ad-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Optimiser son environnement SQL Server avec Azure Monitor](insights/sql-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Optimiser son environnement System Center Operations Manager avec Azure Log Analytics](insights/scom-assessment.md) – Mise à jour des prérequis .NET Framework.
- [Connexions prises en charge avec le connecteur de gestion des services informatiques dans Azure Log Analytics](platform/itsmc-connections.md) – Ajout de New York à l’ID client et à la clé secrète client prérequis.

### <a name="logs"></a>Journaux d’activité
- [Supprimer et récupérer un espace de travail Azure Log Analytics](platform/delete-workspace.md) – Ajout de la méthode PowerShell.
- [Conception de votre déploiement de journaux Azure Monitor](platform/design-logs-deployment.md) – Augmentation du débit d’ingestion des données pour un espace de travail.

### <a name="metrics"></a>Mesures
- [Métriques de plateforme Azure Monitor exportables par le biais des paramètres de diagnostic](platform/metrics-supported-export-diagnostic-settings.md) – Nouvel article.

### <a name="platform-logs"></a>Journaux de plateforme
Plusieurs articles ont été mis à jour après la restructuration du contenu relatif aux journaux de plateforme du fait de la nouvelle fonctionnalité de configuration du journal d’activité à l’aide des paramètres de diagnostic.

- [Archiver des journaux de ressources Azure sur un compte de stockage](platform/resource-logs-collect-storage.md)
- [Schéma d’événement du journal d’activité Azure](platform/activity-log-schema.md)
- [Limites du service Azure Monitor](service-limits.md)
- [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics](platform/activity-log-collect.md)
- [Collecter le journal d’activité Azure avec les paramètres de diagnostic (préversion) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Collecter les journaux d’activité Azure dans un espace de travail Log Analytics auprès de locataire Azure](platform/activity-log-collect-tenants.md)
- [Collecter les journaux de ressources Azure dans l’espace de travail Log Analytics](platform/resource-logs-collect-workspace.md)
- [Créer un paramètre de diagnostic dans Azure à l’aide d’un modèle Resource Manager](platform/diagnostic-settings-template.md)
- [Créer un paramètre de diagnostic pour collecter les journaux et les métriques dans Azure](platform/diagnostic-settings.md)
- [Exporter le journal d’activité Azure](platform/activity-log-export.md)
- [Vue d’ensemble des journaux de plateforme Azure](platform/platform-logs-overview.md)
- [Envoyer en streaming des données de supervision Azure vers un hub d’événements](platform/stream-monitoring-data-event-hubs.md)
- [Diffuser en continu les journaux de plateforme Azure vers un Event Hub](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Guides de démarrages rapides et tutoriels

- [Créer un graphique de métriques dans Azure Monitor](learn/tutorial-metrics-explorer.md) – Nouvel article.
- [Collecter des journaux de ressources à partir d’une ressource Azure et les analyser avec Azure Monitor](learn/tutorial-resource-logs.md) – Nouvel article.
- [Superviser une ressource Azure avec Azure Monitor](learn/quick-monitor-azure-resource.md) – Nouvel article.
   
## <a name="next-steps"></a>Étapes suivantes

- Pour contribuer à la documentation Azure Monitor, consultez le [guide du contributeur de documents](https://docs.microsoft.com/contribute/).
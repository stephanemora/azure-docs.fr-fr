---
title: Sources de données dans Azure Monitor | Microsoft Docs
description: Décrit les données disponibles pour surveiller l’intégrité et les performances de vos ressources Azure, et des applications s’exécutant sur celles-ci.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357484"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Sources de données de surveillance pour Azure Monitor
Azure Monitor est basée sur un [plateforme de données de surveillance courantes](data-platform.md) qui inclut [journaux](data-platform-logs.md) et [métriques](data-platform-metrics.md). Données provenant de plusieurs ressources à analyser à l’aide d’un ensemble d’outils courants dans Azure Monitor permet de collecter des données vers cette plate-forme. Les données de surveillance peuvent également être envoyées à d’autres emplacements pour prendre en charge certains scénarios, et certaines ressources peuvent écrire dans d’autres emplacements avant de pouvoir être collectées dans les journaux ou métriques.

Cet article décrit les différentes sources de données d’analyse collectées par Azure Monitor en plus les données d’analyse créées par les ressources Azure. Liens sont fournis vers des informations détaillées sur la configuration requise pour collecter ces données vers différents emplacements.

## <a name="application-tiers"></a>Couches de l'application

Sources de données de surveillance d’applications Azure peuvent être organisés en niveaux, les niveaux le plus élevés en cours de votre application elle-même et les niveaux inférieurs en cours de composants de plateforme Azure. La méthode d’accès aux données à partir de chaque niveau varie. Les niveaux d’application sont résumées dans le tableau ci-dessous, et les sources de données dans chaque niveau de surveillance sont présentées dans les sections suivantes. Consultez [analyse les emplacements de données dans Azure](data-locations.md) pour obtenir une description de chaque emplacement de données et comment vous pouvez accéder à ses données.


![Niveaux de surveillance](../media/overview/overview.png)


### <a name="azure"></a>Azure
Le tableau suivant décrit brièvement les niveaux d’application qui sont spécifiques à Windows Azure. Suivant le lien pour plus de détails sur chacune d’elles dans les sections ci-dessous.

| Niveau | Description | Méthode de collection |
|:---|:---|:---|
| [Client Azure](#azure-tenant) | données concernant le fonctionnement des services Azure au niveau du locataire, tels qu’Azure Active Directory. | Afficher les données AAD dans le portail ou configurer la collecte à Azure Monitor à l’aide d’un paramètre de diagnostic de client. |
| [Abonnement Azure](#azure-subscription) | Données relatives à l’intégrité et la gestion des services inter-ressources dans votre abonnement Azure, tels que le Gestionnaire de ressources et l’intégrité du Service. | Afficher dans le portail ou configurer la collecte à Azure Monitor à l’aide d’un profil de journal. |
| [Ressources Azure](#azure-resources) |  Données sur le fonctionnement et les performances de chaque ressource Azure. | Les métriques collectées automatiquement, permet d’afficher dans Metrics Explorer.<br>Configurer les paramètres de diagnostic pour collecter des journaux dans Azure Monitor.<br>Surveillance des solutions et disponibles pour l’analyse plus détaillée des types de ressources spécifiques. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, autres cloud ou en local 
Le tableau suivant décrit brièvement les couches application dans Azure, un autre cloud ou en local. Suivant le lien pour plus de détails sur chacune d’elles dans les sections ci-dessous.

| Niveau | Description | Méthode de collection |
|:---|:---|:---|
| [Système d’exploitation (invité)](#operating-system-guest) | Données sur le système d’exploitation sur les ressources de calcul. | Installer l’agent Log Analytique pour collecter des sources de données client dans Azure Monitor et dépendance agent pour collecter des dépendances prenant en charge d’Azure Monitor pour les machines virtuelles.<br>Pour les machines virtuelles Azure, installez l’Extension de Diagnostic Azure pour collecter des journaux et des métriques dans Azure Monitor. |
| [Code d’application](#application-code) | Données sur les performances et les fonctionnalités de l’application réelle et le code, y compris les traces de performances, les journaux des applications et les données de télémétrie utilisateur. | Instrumenter votre code pour collecter des données dans Application Insights. |
| [Sources personnalisées](#custom-sources) | Données à partir des services externes ou d’autres composants ou appareils. | Permet de collecter des données de journaux ou métriques dans Azure Monitor à partir de n’importe quel client REST. |

## <a name="azure-tenant"></a>Client Azure
Les données de télémétrie liées à votre locataire Azure sont collectées à partir des services à l’échelle du locataire, tels qu’Azure Active Directory.

![Collecte de locataire Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Journaux d’audit d’Azure Active Directory
Les [rapports Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contiennent l’historique des connexions et la piste d’audit des modifications apportées à un client particulier. 

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | Configurer les journaux d’Azure AD à collecter dans Azure Monitor pour les analyser avec d’autres données d’analyse. | [Intégrer des journaux Azure AD avec les journaux d’Azure Monitor (version préliminaire)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Stockage Azure | Exporter les journaux d’Azure AD dans le stockage Azure pour l’archivage. | [Tutoriel : Archiver les journaux d’Azure AD à un compte de stockage Azure (version préliminaire)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Event Hub | Stream Azure AD se connecte à d’autres emplacements à l’aide d’Event Hubs. | [Tutoriel : Stream des journaux Azure Active Directory à un concentrateur d’événements Azure (aperçu)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Abonnement Azure
Données de télémétrie liées à l’intégrité et le fonctionnement de votre abonnement Azure.

![Abonnement Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Journal d’activité Azure 
Le [journal des activités Azure](activity-logs-overview.md) comprend les enregistrements de contrôle d’intégrité de service ainsi que les enregistrements des modifications de configuration apportées aux ressources dans votre abonnement Azure. Le journal d’activité est disponible pour toutes les ressources Azure et représente leur affichage _externe_.

| Destination | Description | Référence |
|:---|:---|
| Journal d’activité | Le journal d’activité est collecté dans son propre magasin de données que vous pouvez afficher dans le menu Azure Monitor ou utiliser pour créer des alertes de journal d’activité. | [Interroger le journal d’activité dans le portail Azure](activity-log-view.md#azure-portal) |
| Journaux Azure Monitor | Configurer les journaux d’analyse Azure pour collecter le journal d’activité pour l’analyser avec d’autres données d’analyse. | [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Analytique de journal dans Azure Monitor](activity-log-collect.md) |
| Stockage Azure | Exporter le journal d’activité dans le stockage Azure pour l’archivage. | [Archiver le journal d’activité](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream le journal d’activité à d’autres emplacements à l’aide d’Event Hubs | [Journal d’activité Stream au Hub d’événements](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) fournit des informations sur l’intégrité des services Azure compris dans votre abonnement, dont dépendent votre application et vos ressources.

| Destination | Description | Référence |
|:---|:---|:---|
| Journal d’activité<br>Journaux Azure Monitor | Les enregistrements de l’intégrité du service sont stockés dans le journal d’activité Azure, pour vous permettre de les afficher dans le portail Azure ou d’effectuer toutes les activités que vous pouvez effectuer avec le journal d’activité. | [Afficher les notifications d’intégrité du service à l’aide du Portail Azure](service-notifications.md) |


## <a name="azure-resources"></a>Ressources Azure
Les journaux de diagnostic au niveau métriques et ressources fournissent des informations sur le fonctionnement _interne_ des ressources Azure. Ils sont disponibles pour la plupart des services Azure, et la surveillance des solutions et des insights collectent des données supplémentaires pour les services particuliers.

![Collection de ressources Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métriques de la plateforme 
Services Azure plus enverra [métriques de la plateforme](data-platform-metrics.md) qui reflètent les performances et l’opération directement à la base de données de métriques. Les [métriques spécifiques varient pour chaque type de ressource](metrics-supported.md). 

| Destination | Description | Référence |
|:---|:---|:---|
| Métriques Azure Monitor | Métriques de la plateforme écrira dans la base de données de métriques Azure Monitor sans aucune configuration. Accès aux mesures de plate-forme à partir de Metrics Explorer.  | [Prise en main Azure Metrics Explorer](metrics-getting-started.md)<br>[Mesures prises en charge avec Azure Monitor](metrics-supported.md) |
| Journaux Azure Monitor | Copiez les métriques de la plateforme dans les journaux pour l’analyse de tendance et d’autre à l’aide d’Analytique de journal. | [Diagnostics Azure directement dans le journal Analytique](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Métriques de Stream dans d’autres emplacements à l’aide d’Event Hubs. |[Diffuser des données de surveillance Azure vers un hub d’événements pour les utiliser dans un outil externe](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Journaux de diagnostic
[Journaux de diagnostic](diagnostic-logs-overview.md) fournissent des informations sur le _interne_ fonctionnement d’une ressource Azure.  Journaux de diagnostic ne sont pas activés par défaut. Vous devez les activer et spécifier une destination pour chaque ressource. 

La configuration requise et le contenu des journaux de Diagnostic varient selon le type de ressource, et pas tous les services encore créent des journaux de Diagnostic. Consultez [prise en charge des services, des schémas et des catégories pour les journaux de Diagnostic Azure](diagnostic-logs-schema.md) pour plus d’informations sur chaque service et des liens vers les procédures de configuration détaillées. Si le service n’est pas répertorié dans cet article, ce service n’actuellement écrire aux journaux de diagnostic.

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | Envoyer les journaux de Diagnostic pour les journaux d’Azure Monitor pour l’analyse avec les autres données collectées. Certaines ressources peuvent écrire directement dans Azure Monitor, tandis que d’autres écrire dans un compte de stockage avant son importation dans un espace de travail Analytique de journal. | [Journaux de Diagnostic Azure Stream à l’espace de travail Analytique de journal dans Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Utiliser le portail Azure pour collecter les journaux de stockage Azure](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Stockage | Diagnostic envoyer des journaux vers le stockage Azure pour l’archivage. | [Archivage des journaux de Diagnostic Azure](archive-diagnostic-logs.md) |
| Event Hubs | Diagnostic de Stream se connecte à d’autres emplacements à l’aide d’Event Hubs. |[Journaux de Diagnostic Azure Stream à un concentrateur d’événements](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Système d’exploitation (invité)
Les ressources de calcul dans Azure, dans d’autres clouds et en local ont un système d’exploitation invité à surveiller. Avec l’installation d’un ou plusieurs agents, vous pouvez collecter des données de télémétrie à partir de l’invité dans Azure Monitor pour les analyser avec les mêmes outils de surveillance en tant que les services Azure eux-mêmes.

![Collection de ressources de calcul Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extension Azure Diagnostics
L’activation de l’extension Azure Diagnostics pour les machines virtuelles Azure vous permet pour collecter des journaux et métriques à partir du système d’exploitation invité Azure de ressources, y compris les services de Cloud Azure (classique) rôles Web et Worker, Machines virtuelles, machine virtuelle de calcul Scale sets et Service Fabric.

| Destination | Description | Référence |
|:---|:---|:---|
| Stockage | Lorsque vous activez l’Extension de Diagnostics, il écrit dans un compte de stockage par défaut. | [Stocker et afficher des données de diagnostic dans le stockage Azure](diagnostics-extension-to-storage.md) |
| Métriques Azure Monitor | Lorsque vous configurez l’Extension de Diagnostics pour collecter les compteurs de performances, ils sont écrits dans la base de données de métriques Azure Monitor. | [Envoyer des métriques de système d’exploitation invité pour la métrique Azure Monitor stocker à l’aide d’un modèle Resource Manager pour un ordinateur virtuel de Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Journaux d’application Insights | Collecter les journaux et compteurs de performances à partir de la ressource de calcul prenant en charge votre application à analyser avec d’autres données d’application. | [Envoyer des données de diagnostic de Service Cloud, Machine virtuelle ou Service Fabric à Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Configurez l’Extension de Diagnostics pour diffuser en continu les données vers d’autres emplacements à l’aide d’Event Hubs.  | [Diffusion en continu des données de Diagnostics Azure dans le chemin réactif à l’aide d’Event Hubs](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agent Log Analytics 
Installez l’agent de l’Analytique de journal pour l’analyse complète et la gestion de vos machines virtuelles Windows ou Linux. La machine virtuelle peut s’exécuter dans Azure, dans un autre cloud ou localement.

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | L’agent d’Analytique de journal se connecte à Azure surveiller directement ou via System Center Operations Manager et vous permet de collecter des données à partir de sources de données que vous configurez ou à partir de la surveillance des solutions qui fournissent des informations supplémentaires sur les applications en cours d’exécution sur l’ordinateur virtuel. | [Sources de données de l’agent dans Azure Monitor](agent-data-sources.md)<br>[Connecter Operations Manager à Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles 
[Azure Monitor pour les machines virtuelles](../insights/vminsights-overview.md) fournit une expérience de surveillance personnalisée pour les machines virtuelles fournissant des fonctionnalités au-delà des fonctionnalités d’Azure Monitor principales, y compris l’état du service et contrôle d’intégrité de la machine virtuelle. Elle nécessite un Agent de dépendances sur les machines virtuelles Windows et Linux qui s’intègre à l’agent Log Analytique pour collecter les données découvertes sur les processus en cours d’exécution sur la machine virtuelle et les dépendances de processus externes.

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | Stocke les données sur les processus et les dépendances sur l’agent. | [À l’aide d’Azure Monitor pour les machines virtuelles (version préliminaire) Map pour comprendre les composants d’application](../insights/vminsights-maps.md) |
| Stockage de machine virtuelle | Azure Monitor pour les machines virtuelles stocke les informations d’état de contrôle d’intégrité dans un emplacement personnalisé. Cela est uniquement disponible pour Azure Monitor pour les machines virtuelles dans le portail Azure en plus de la [API REST Azure Resource health](/rest/api/resourcehealth/). | [Comprendre l’intégrité de vos machines virtuelles Azure](../insights/vminsights-health.md)<br>[API REST Azure Resource health](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Code d’application
Analyse des applications détaillées dans Azure Monitor s’effectue avec [Application Insights](https://docs.microsoft.com/azure/application-insights/) qui collecte les données à partir d’applications qui s’exécutent sur diverses plateformes. L’application peut s’exécuter dans Azure, dans un autre cloud ou localement.

![Collecte de données d’application](media/data-sources/applications.png)


### <a name="application-data"></a>Données d'application
Lorsque vous activez Application Insights pour une application en installant un package d’instrumentation, la solution collecte des métriques et journaux d’activité liés aux performances et au fonctionnement de l’application. Application Insights stocke les données collectées dans la même plateforme de données Azure Monitor utilisée par d’autres sources de données. Il inclut des outils étendus pour analyser ces données, mais vous pouvez également l’analyser avec des données provenant d’autres sources à l’aide des outils tels que Metrics Explorer et Analytique de journal.

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | Données opérationnelles relatives à votre application, y compris les affichages de pages, les demandes d’application, les exceptions et les suivis. | [Analyser les données de journal dans Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informations de dépendance entre les composants d’application pour prendre en charge de la cartographie d’Application et de corrélation de télémétrie. | [Corrélation de télémétrie dans Application Insights](../app/correlation.md) <br> [Plan de l’application](../app/app-map.md) |
|            | Résultats de tests de disponibilité qui testent la disponibilité et la réactivité de votre application à partir de différents emplacements sur l’Internet public. | [Analyse de la disponibilité et de la réactivité d'un site Web](../app/monitor-web-app-availability.md) |
| Métriques Azure Monitor | Application Insights collecte des mesures décrivant les performances et le fonctionnement de l’application en plus des mesures personnalisées que vous définissez dans votre application dans la base de données de métriques Azure Monitor. | [Basé sur journal et agrégées au préalable les mesures dans Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API application Insights pour les métriques et événements personnalisés](../app/api-custom-events-metrics.md) |
| Stockage Azure | Envoyer des données d’application dans le stockage Azure pour l’archivage. | [Exporter la télémétrie depuis Application Insights](../app/export-telemetry.md) |
|            | Détails de tests de disponibilité sont stockés dans le stockage Azure. Utilisez Application Insights dans le portail Azure pour télécharger pour une analyse locale. Résultats de tests de disponibilité sont stockés dans les journaux d’Azure Monitor. | [Analyse de la disponibilité et de la réactivité d'un site Web](../app/monitor-web-app-availability.md) |
|            | Les données de trace de Profiler sont stockées dans le stockage Azure. Utilisez Application Insights dans le portail Azure pour télécharger pour une analyse locale.  | [Profiler des applications de production dans Azure avec Application Insights](../app/profiler-overview.md) 
|            | Déboguer l’instantané de données qui sont capturées pour un sous-ensemble d’exceptions sont stockées dans le stockage Azure. Utilisez Application Insights dans le portail Azure pour télécharger pour une analyse locale.  | [Fonctionnement des captures instantanées](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Solutions de surveillance et les Insights
[Surveillance des solutions](../insights/solutions.md) et [Insights](../insights/insights-overview.md) collecter des données pour fournir des informations supplémentaires sur le fonctionnement d’un service particulier ou d’application. Ils peuvent traiter les ressources dans les différentes couches d’application et même à plusieurs niveaux.

### <a name="monitoring-solutions"></a>Solutions de supervision

| Destination | Description | Référence
|:---|:---|:---|
| Journaux Azure Monitor | Solutions de surveillance collectant des données dans les journaux Azure Monitor où il peut être analysé à l’aide du langage de requête ou [vues](view-designer.md) qui sont généralement incluses dans la solution. | [Détails de collecte de données pour la surveillance des solutions dans Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor pour les conteneurs
[Azure Monitor pour les conteneurs](../insights/container-insights-overview.md) fournit une expérience de surveillance personnalisée pour [Azure Kubernetes Service (AKS)](/azure/aks/). Il collecte des données supplémentaires sur ces ressources décrites dans le tableau suivant.

| Destination | Description | Référence |
|:---|:---|:---|
| Journaux Azure Monitor | Magasins de données de surveillance pour AKS, y compris l’inventaire, les journaux et les événements. Données de mesure sont également stockées dans les journaux afin de tirer parti de ses fonctionnalités d’analyse dans le portail. | [Comprendre les performances du cluster AKS avec Azure Monitor pour conteneurs](../insights/container-insights-analyze.md) |
| Métriques Azure Monitor | Données métriques sont stockées dans la base de données de métrique pour la visualisation de lecteur et les alertes. | [Afficher les métriques de conteneur dans metrics explorer](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Dans l’ordre pour une expérience en temps réel proche, Azure Monitor pour les conteneurs présente les données directement à partir du service Azure Kubernetes dans le portail Azure. | [Affichage des journaux de conteneur en temps réel avec Azure Monitor pour les conteneurs (préversion)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor pour machines virtuelles
[Azure Monitor pour les machines virtuelles](../insights/vminsights-overview.md) fournit une expérience personnalisée pour la surveillance des machines virtuelles. Une description des données collectées par Azure Monitor pour les machines virtuelles est incluse dans le [le système d’exploitation (invité)](#operating-system-guest) section ci-dessus.

## <a name="custom-sources"></a>Sources personnalisées
Outre les niveaux standard d’une application, vous devrez peut-être surveiller d’autres ressources qui ont des données de télémétrie qui ne peuvent pas être collectées avec les autres sources de données. Pour ces ressources, écrire ces données dans les métriques ou journaux à l’aide d’une API Azure Monitor.

![Collection personnalisée](media/data-sources/custom.png)

| Destination | Méthode | Description | Référence |
|:---|:---|:---|:---|
| Journaux Azure Monitor | API du collecteur de données | Collecter des données de journal à partir de n’importe quel client REST et le stocker dans l’espace de travail Analytique de journal. | [Envoyer des données de journal à Azure Monitor avec l’API collecteur de données HTTP (préversion publique)](data-collector-api.md) |
| Métriques Azure Monitor | API de métriques personnalisées | Collecter les données de mesure à partir de n’importe quel client REST et les stocke dans la base de données de métriques Azure Monitor. | [Envoyer des mesures personnalisées pour une ressource Azure dans le magasin de métrique Azure Monitor à l’aide d’une API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Autres services
Autres services dans Azure écrivent des données dans la plate-forme de données Azure Monitor. Cela vous permet à analyser les données collectées par ces services avec les données collectées par Azure Monitor et tirer parti de la même analyse et les outils de visualisation.

| de diffusion en continu | Destination | Description | Référence |
|:---|:---|:---|:---|
| [Centre de sécurité Azure](/azure/security-center/) | Journaux Azure Monitor | Azure Security Center stocke les données de sécurité collectées dans un espace de travail Analytique de journal qui lui permet d’être analysées avec les autres données collectées par Azure Monitor.  | [Collecte de données dans Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Sentinel Azure](/azure/sentinel/) | Journaux Azure Monitor | Sentinel Azure stocke les données collectées à partir de différentes sources de données dans un espace de travail Analytique de journal qui lui permet d’être analysées avec les autres données collectées par Azure Monitor.  | [Connecter des sources de données](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [types de données de supervision collectés par Azure Monitor](data-platform.md) et sur l’affichage et l’analyse de ces données.
- Liste les [différents emplacements où les ressources Azure stockent des données](data-locations.md) et comment vous pouvez y accéder. 

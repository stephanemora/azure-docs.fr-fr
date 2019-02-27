---
title: Journalisation et audit Azure | Microsoft Docs
description: Découvrez comment utiliser les données de journalisation pour obtenir des informations détaillées sur votre application.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 492beba1040cef3d5a910cc9db3fe16b41c33cd6
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301585"
---
# <a name="azure-logging-and-auditing"></a>Journalisation et audit Azure

Azure offre un large éventail d’options de journalisation et d’audit de sécurité configurables pour vous aider à identifier les failles dans vos mécanismes et stratégies de sécurité. Cet article décrit la génération, la collecte et l’analyse des journaux de sécurité des services hébergés sur Azure.

> [!Note]
> Certaines recommandations contenues dans cet article risquent d’entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul, et donc une augmentation des coûts de licence ou d’abonnement.

## <a name="types-of-logs-in-azure"></a>Types de journaux dans Azure

Les applications cloud sont complexes, avec de nombreux éléments mobiles. Les journaux fournissent des données pour que vos applications continuent à fonctionner. Ils vous aident à résoudre des problèmes antérieurs et futurs. Ils peuvent aussi aider à améliorer les performances ou la maintenabilité des applications, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

Les journaux Azure sont classés par type :
* Les **journaux de contrôle/gestion** fournissent des informations sur les opérations CREATE, UPDATE, and DELETE Azure Resource Manager. Pour plus d’informations, consultez [Journaux des activités Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* Les **journaux de plan de données** fournissent des informations sur les événements déclenchés lors de l’utilisation des ressources Azure. Il s’agit par exemple des journaux du système d’événements, de la sécurité et des applications Windows dans une machine virtuelle, et des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) qui sont configurés via Azure Monitor.

* Les **événements traités** fournissent des informations sur les événements/alertes analysés en votre nom. Les alertes [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) en sont un exemple, où [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) a traité et analysé votre abonnement et fournit des alertes de sécurité très concises.

Le tableau suivant liste les principaux types de journaux disponibles dans Azure :

| Catégorie de journal | Type de journal | Usage | Intégration |
| ------------ | -------- | ------ | ----------- |
|[Journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Événements de plan de contrôle sur les ressources d’Azure Resource Manager|   Fournissent des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement.|    API Rest, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Journaux de diagnostics Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Données fréquentes sur les opérations des ressources Azure Resource Manager de l’abonnement|    Fournissent des insights sur les opérations que votre ressource réalise elle-même.| Azure Monitor, [diffusion](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Génération de rapports Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Journaux et rapports | Signalent les activités de connexion des utilisateurs et fournissent des informations sur l’activité système relative à la gestion des utilisateurs et des groupes.|[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Machines virtuelles et services cloud](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Service du journal des événements Windows et Syslog Linux|  Capture les données système et les données de journalisation sur les machines virtuelles, puis les transfère vers un compte de stockage de votre choix.|   Windows (avec le stockage Windows Azure Diagnostics [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)]) et Linux dans Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Journalisation du stockage, fournit les données de métriques d’un compte de stockage|Fournit des informations sur les demandes de trace, analyse les tendances d’utilisation et diagnostique les problèmes de votre compte de stockage.|   API REST ou [bibliothèque cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Journalisation des flux de groupe de sécurité réseau (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Format JSON, affiche les flux entrants et sortants, par règle|Affiche des informations sur le trafic IP entrant et sortant via un groupe de sécurité réseau.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Journaux, exceptions et diagnostics personnalisés|   Fournit un service de monitoring des performances de l’application (APM) aux développeurs web sur de nombreuses plateformes.| API REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Traitement des données/alertes de sécurité|    Alertes Azure Security Center, alertes Azure Log Analytics|   Fournit des alertes et des informations sur la sécurité.|  API REST, JSON|

### <a name="activity-logs"></a>Journaux d’activité

Les [journaux d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Les journaux d’activité s’appelaient avant « journaux d’audit » ou « journaux des opérations » parce qu’ils signalaient les [événements de plan de contrôle](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) de vos abonnements. 

Les journaux d’activité vous aident à déterminer le « qui, quoi, quand » des opérations en écriture (PUT, POST ou DELETE). Ils vous aident aussi à comprendre l’état de l’opération et autres propriétés pertinentes. Les journaux d’activité n’incluent pas les opérations (GET) de lecture.

Dans cet article, PUT, POST et DELETE font référence à l’ensemble des opérations d’écriture que contient le journal d’activité sur les ressources. Par exemple, vous pouvez utiliser les journaux d’activité pour rechercher une erreur quand vous résolvez des problèmes ou pour surveiller la manière dont un utilisateur de votre organisation modifie une ressource.

![Diagramme du journal d’activité](./media/azure-log-audit/azure-log-audit-fig1.png)

Vous pouvez extraire des événements d’un journal d’activité en utilisant le portail Azure, [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), des applets de commande PowerShell et l’[API REST Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). La période de rétention des données des journaux d’activité est de 90 jours.

Scénarios d’intégration pour un événement de journal d’activité :

* [Créez une alerte par e-mail ou webhook qui est déclenchée par un événement du journal d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Envoyez-le à un hub d’événements](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) pour ingestion par un service tiers ou une solution d’analytique personnalisée comme PowerBI.

* Analysez-le dans PowerBI à l’aide du [pack de contenu PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Enregistrez-le dans un compte de stockage pour archivage ou inspection manuelle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Vous pouvez spécifier la durée de conservation (en jours) à l’aide de profils de journal.

* Interrogez-le et affichez-le dans le portail Azure.

* Interrogez-le avec une applet de commande PowerShell, Azure CLI ou une API REST.

* Exportez le journal d’activité avec les profils de journal dans [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Vous pouvez utiliser un compte de stockage ou un [espace de noms Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) qui n’est pas dans le même abonnement que celui générant le journal. La personne qui configure le paramètre doit disposer d’un accès [RBAC (contrôle d’accès en fonction du rôle)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) approprié aux deux abonnements.

### <a name="azure-diagnostics-logs"></a>Journaux de diagnostics Azure

Les journaux de diagnostic Azure sont générés par une ressource et fournissent des données complètes et fréquentes sur le fonctionnement de cette ressource. Le contenu de ces journaux varie en fonction du type de ressource. Par exemple, les [journaux système des événements Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) sont une catégorie de journal de diagnostic pour les machines virtuelles et les [journaux de file d’attente, table et objet blob](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) sont des catégories de journaux de diagnostic pour les comptes de stockage. Les journaux de diagnostic diffèrent des journaux d’activité, lesquels fournissent des insights sur les opérations qui ont été effectuées sur les ressources de votre abonnement.

![Diagrammes des journaux de diagnostic Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Les journaux de diagnostic Azure offrent plusieurs options de configuration, comme le portail Azure, PowerShell, Azure CLI et l’API REST.

**Scénarios d’intégration**

* Enregistrez-les dans un [compte de stockage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) pour audit ou inspection manuelle. Vous pouvez spécifier la durée de conservation (en jours) via les paramètres de diagnostic.

* [Envoyez-les à un hub d’événements](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) pour ingestion par un service tiers ou une solution d’analytique personnalisée comme [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Analysez-les avec [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Services pris en charge, schéma des journaux de diagnostic et catégories de journaux prises en charge par type de ressource**


| Service | Schéma et documentation | Type de ressource | Catégorie |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics pour Load Balancer (préversion)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Group|[Log Analytics pour les groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Journalisation des diagnostics pour Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Journaux Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Recherche Azure|[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Accéder aux journaux de diagnostic de Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Audit<br>Requests|
|Service Analytique Azure Data Lake|[Accéder aux journaux de diagnostic de Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Audit<br>Requests|
|Azure Logic Apps|[Schéma de suivi personnalisé Logic Apps B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Journaux de diagnostics Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Log Analytics pour Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Hubs d'événements Azure|[Journaux de diagnostic Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Journaux de diagnostics de travail](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Exécution<br>Création|
|Azure Service Bus|[Journaux de diagnostic Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Création de rapports Active Directory

Azure Active Directory (Azure AD) comprend des rapports sur la sécurité, les activités et l’audit de l’annuaire d’un utilisateur. Le [rapport d’audit Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) vous aide à identifier les actions privilégiées qui se sont produites dans l’instance Azure AD de l’utilisateur. Ces actions privilégiées incluent les changements d'élévation (par exemple la création de rôle ou la réinitialisation de mot de passe), le changement des configurations de stratégie (par exemple les stratégies de mot de passe) ou les changements de configuration d’annuaire (par exemple les changements de paramètres de fédération de domaine).

Les rapports fournissent l’enregistrement d’audit pour le nom d’événement, l’utilisateur qui a effectué l’action, la ressource cible affectée par le changement, ainsi que la date et l’heure (UTC). Les utilisateurs peuvent récupérer la liste des événements d’audit d’Azure AD dans le [portail Azure](https://portal.azure.com/), comme décrit dans [Afficher vos journaux d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

Les rapports inclus sont listés dans le tableau suivant :

| Rapports de sécurité | Rapports d’activité | Rapport d’audit |
| :--------------- | :--------------- | :------------ |
|Connexions à partir de sources inconnues| Utilisation des applications : résumé| Rapport d’audit d’annuaire|
|Connexions après plusieurs échecs|  Utilisation des applications : présentation détaillée||
|Connexions depuis plusieurs zones géographiques|    Tableau de bord de l’application||
|Connexions depuis des adresses IP avec des activités suspectes|   Erreurs de configuration de compte||
|Activité de connexion anormale|    Appareils des utilisateur individuels||
|Connexions à partir d’appareils potentiellement infectés|   Activité des utilisateurs individuels||
|Utilisateurs ayant une activité de connexion anormale| Rapport d'activité de groupes||
||Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe||
||Activité de réinitialisation de mot de passe|||

Les données de ces rapports peuvent être utiles pour vos applications, telles que les systèmes SIEM (Security Information and Event Management), l’audit et les outils de décisionnel. Les API de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) à partir de divers outils et langages de programmation.

Les événements du rapport d’audit d’Azure AD sont conservés pendant 180 jours.

> [!Note]
> Pour plus d’informations sur la conservation des rapports, consultez [Stratégies de conservation des rapports Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Si vous souhaitez conserver vos événements d’audit plus longtemps, utilisez l’API de création de rapport pour tirer régulièrement les [événements d’audit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) dans une banque de données séparée.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Journaux de machines virtuelles utilisant Azure Diagnostics

[Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) est la fonctionnalité Azure qui permet de collecter des données de diagnostic sur une application déployée. Vous pouvez utiliser l’extension de diagnostic à partir de plusieurs sources. Sont actuellement pris en charge les [rôles de travail et web Azure Cloud Services](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Journaux de machines virtuelles utilisant Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Machines virtuelles Azure](/learn/paths/deploy-a-website-with-azure-virtual-machines/) exécutant Microsoft Windows et [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Vous pouvez activer Azure Diagnostics sur une machine virtuelle en effectuant l’une des actions suivantes :

* [Utiliser Visual Studio pour suivre les machines virtuelles Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Configurer Azure Diagnostics à distance sur une machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Utiliser PowerShell pour configurer des diagnostics sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostic à l’aide d’un modèle Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) journalise et fournit les données de métrique d’un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage La journalisation Storage Analytics est disponible pour les [services de stockage Blob Azure, File d’attente Azure et Table Azure](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage.

Vous pouvez utiliser ces informations pour surveiller les demandes individuelles et diagnostiquer les problèmes d’un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort. Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison de blob, mais pas dans ses points de terminaison de table ou de file d’attente, seuls les journaux qui appartiennent au service de stockage Blob sont créés.

Pour utiliser Storage Analytics, activez-le individuellement pour chaque service que vous souhaitez surveiller. Vous pouvez l’activer dans le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez [Surveiller un compte de stockage dans le portail Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer Storage Analytics individuellement pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), auxquels vous pouvez accéder en utilisant les API du service de stockage Blob et Table.

Storage Analytics a une limite de 20 téraoctets (To) pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Tous les journaux sont stockés dans des [objets blob de blocs](https://docs.microsoft.com/azure/storage/storage-analytics) dans un conteneur nommé $logs, qui est automatiquement créé quand vous activez Storage Analytics pour un compte de stockage.

> [!Note]
> * Pour plus d’informations sur les stratégies de facturation et de conservation des données, consultez [Storage Analytics et facturation](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Pour plus d’informations sur les limites des comptes de stockage, consultez [Objectifs de performance et de scalabilité Stockage Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Storage Analytics journalise les types suivants de demandes authentifiées et anonymes :

| Authentifiée  | Anonyme|
| :------------- | :-------------|
| Demandes ayant réussi | Demandes ayant réussi |
|Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs | Demandes utilisant une signature d'accès partagé, notamment les demandes ayant réussi et ayant échoué |
| Demandes utilisant une signature d'accès partagé, notamment les demandes ayant réussi et ayant échoué |Erreurs d’expiration pour le client et le serveur |
|   Demandes de données d’analyse |    Demandes GET ayant échoué avec le code d’erreur 304 (non modifié) |
| Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. La liste complète des données journalisées est disponible dans [Opérations et messages d’état journalisés Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données journalisées est disponible dans [Opérations et messages d’état journalisés Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Journaux réseaux Azure

La journalisation et la surveillance réseau dans Azure sont complètes et couvrent deux grandes catégories :

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) : la surveillance réseau basée sur des scénarios est fournie avec les fonctionnalités de Network Watcher. Ce service inclut la capture de paquets, le tronçon saut suivant, la vérification des flux IP, l’affichage de groupe de sécurité, les journaux de flux de groupe de sécurité réseau. La surveillance basée sur des scénarios fournit une vue de bout en bout des ressources réseau, à l’inverse de la surveillance des ressources réseau qui s’intéresse à chaque ressource spécifique.

* [Surveillance des ressources](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) : la surveillance au niveau des ressources se compose de quatre fonctionnalités : journaux de diagnostic, métriques, résolution des problèmes et intégrité des ressources. Toutes ces fonctionnalités sont conçues au niveau des ressources réseau.

![Journaux réseaux Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des insights sur votre réseau dans Azure.

### <a name="network-security-group-flow-logging"></a>Journalisation des flux de groupe de sécurité réseau

Les [journaux des flux de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) désignent une fonctionnalité de Network Watcher que vous pouvez utiliser pour voir des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Ces journaux de flux sont écrits au format JSON et affichent :
* Les flux entrants et sortants, règle par règle.
* La carte réseau à laquelle s’applique le flux.
* 5 informations sur le flux : adresse IP source ou de destination, port source ou de destination et protocole.
* Si le trafic était autorisé ou refusé.

Même si les journaux de flux ciblent les groupes de sécurité réseau, ils ne sont pas affichés de la même façon que les autres journaux. Les journaux de flux sont uniquement stockés au sein d’un compte de stockage.

Les mêmes stratégies de conservation qui sont dans les autres journaux s’appliquent aux journaux de flux. Les journaux ont une stratégie de conservation que vous pouvez définir dans une plage comprise entre 1 et 365 jours. Si aucune stratégie de rétention n’est définie, les journaux sont conservés indéfiniment.

**Journaux de diagnostics**

Les événements périodiques et spontanés sont créés par les ressources réseau et journalisés dans les comptes de stockage, puis envoyés à un hub d’événements ou à Log Analytics. Les journaux fournissent des insights sur l’intégrité d’une ressource. Vous pouvez les voir dans des outils tels que Power BI et Log Analytics. Pour savoir comment afficher les journaux de diagnostic, visitez [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Journaux de diagnostics](./media/azure-log-audit/azure-log-audit-fig5.png)

Les journaux de diagnostic sont disponibles dans [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), les itinéraires et [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher fournit une vue des journaux de diagnostic. Cette vue contient toutes les ressources réseau qui prennent en charge la journalisation des diagnostics. À partir de celui-ci, vous pouvez activer et désactiver les ressources réseau facilement et rapidement.


En plus des fonctionnalités de journalisation mentionnées ci-dessus, Network Watcher propose actuellement les fonctionnalités suivantes :
- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) : fournit une vue au niveau du réseau qui montre les différentes interconnexions et associations entre les ressources réseau dans un groupe de ressources.

- [Capture de paquets variables](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) : capture les données des paquets qui entrent et sortent d’une machine virtuelle. Des options avancées de filtrage et des commandes d’affinage, comme les paramètres de limites de taille et de durée, offrent une grande souplesse. Les données de paquet peuvent être stockées dans un magasin d’objets blob ou sur le disque local au format de fichier *.cap*.

* [Vérification du flux IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) : vérifie si un paquet est autorisé ou refusé en fonction des paramètres de paquet des 5-tuple de flux (adresse IP de destination, adresse IP source, port de destination, port source et protocole). Si le paquet est refusé par un groupe de sécurité, la règle et le groupe qui ont refusé le paquet sont renvoyés.

* [Tronçon suivant](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) : détermine le tronçon suivant des paquets routés dans la structure fabric du réseau Azure, pour identifier les routes définies par l’utilisateur qui ont été mal configurées.

* [Affichage des groupes de sécurité](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) : Obtient les règles de sécurité effectives et appliquées à une machine virtuelle.

* [Résolution des problèmes de passerelle de réseau virtuel et de connexion](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) : vous aide à résoudre les problèmes liés aux connexions ou aux passerelles de réseau virtuel.

* [Limites d’abonnement réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) : vous permet d’afficher l’utilisation des ressources réseau par rapport aux limites.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service APM extensible destiné aux développeurs web sur plusieurs plateformes. Utilisez-le pour surveiller des applications web en direct. Il détecte automatiquement les problèmes de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application.

Application Insights a été conçu pour vous permettre d’améliorer continuellement les performances et la facilité d’utilisation.

Il fonctionne avec des applications sur un large éventail de plateformes, notamment .NET, Node.js et Java EE, qu’elles soient hébergées localement ou dans le cloud. Il s’intègre à votre processus DevOps et offre des points de connexion avec divers outils de développement.

![Diagramme Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights est destiné à l’équipe de développement et permet de comprendre les performances et l’utilisation de votre application. Il analyse les éléments suivants :

* **Taux de demandes, temps de réponse et taux d’échec** : déterminez quelles sont les pages les plus consultées, à quel moment de la journée et où se trouvent vos utilisateurs. Identifiez les pages qui offrent les meilleures performances. Si vos temps de réponse et votre taux d’échec augmentent quand les demandes augmentent, vous avez peut-être un problème de ressources.

* **Taux de dépendance, temps de réponse et taux d’échec** : déterminez si des services externes vous ralentissent.

* **Exceptions** : analysez les statistiques agrégées, ou choisissez des instances précises et explorez dans le détail les appels de procédure et les requêtes connexes. Les exceptions de serveur et de navigateur sont signalées.

* **Vues de pages et performances de chargement** : examinez les rapports générés par les navigateurs de vos utilisateurs.

* **Appels AJAX** : obtenez les taux de pages web, les temps de réponse et les taux d’échec.

* **Nombre de sessions et d’utilisateurs**.

* **Compteurs de performances** : obtenez des données de vos serveurs Windows ou Linux, par exemple sur le processeur, la mémoire et l’utilisation du réseau.

* **Diagnostics d’hébergement** : obtenez des données de Docker ou d’Azure.

* **Journaux de suivi des diagnostics** : obtenez des données de votre application pour pouvoir mettre en corrélation les événements de suivi avec les requêtes.

* **Métriques et événements personnalisés** : obtenez des données que vous écrivez vous-même dans le code client ou serveur, pour suivre des événements métier tels que les articles vendus ou les jeux gagnés.

Le tableau suivant liste et décrit des scénarios d’intégration :

| Scénario d’intégration | Description |
| --------------------- | :---------- |
|[Plan de l’application](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Les composants de votre application, avec des alertes et des mesures clés.||
|[Recherche de diagnostic pour les données d’instance](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Cherchez et filtrez les événements, comme les requêtes, les exceptions, les appels de dépendance, les suivis de journaux et les affichages de pages.||
|[Metrics Explorer pour les données agrégées](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Explorez, filtrez et segmentez des données agrégées, comme les taux de demandes, d’échecs et d’exceptions, les temps de réponse et les durées de chargement des pages.||
|[Tableaux de bord](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combinez des données de plusieurs sources et partagez-les avec d’autres. Idéal pour les applications à composants multiples et pour un affichage en continu dans la salle de l’équipe.||
|[Flux de métriques temps réel](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Lorsque vous déployez une nouvelle version, observez ces indicateurs de performance quasiment en temps réel pour vous assurer que tout fonctionne comme prévu.||
|[Analyse](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Répondez à des questions difficiles sur les performances et l’utilisation de votre application avec ce langage de requêtes puissant.||
|[Alertes automatiques et manuelles](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Les alertes automatiques s’adaptent aux modèles télémétriques habituels de votre application et se déclenchent lorsqu’un comportement inhabituel est détecté. Vous pouvez également définir des alertes sur des niveaux particuliers de mesures personnalisées ou standard.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Consultez les données de performances dans le code. Accédez au code à partir de l’arborescence des appels de procédure.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Intégrez des mesures d’utilisation à d’autres données décisionnelles.||
|[API REST](https://dev.applicationinsights.io/)|Écrivez du code pour exécuter des requêtes sur vos propres données brutes et mesures.||
|[Exportation continue](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportation en bloc de données brutes vers le système de stockage dès leur arrivée.||

### <a name="azure-security-center-alerts"></a>Alertes Azure Security Center

La détection des menaces d’Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces. Les alertes de sécurité, ainsi que les recommandations sur la façon de répondre à la menace, sont hiérarchisées dans Azure Security Center. Pour plus d’informations, consultez [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Diagramme Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Azure Security Center fait appel à une analytique de sécurité avancée qui va bien au-delà des approches basées sur la signature. Il applique les avancées des technologies de big data et d’[apprentissage automatique](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) pour évaluer les événements de toute la structure fabric cloud. De cette façon, il détecte les menaces qui seraient impossibles à identifier avec des approches manuelles et peut prévoir l’évolution des attaques. Ces analyses de sécurité comprennent les éléments suivants :

* **Threat Intelligence intégrée** : recherche les éléments malveillants connus en exploitant les informations globales de Threat Intelligence provenant des produits et services Microsoft, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) et de sources externes.

* **Analytique comportementale** : applique des modèles connus pour détecter les comportements malveillants.

* **Détection des anomalies** : utilise le profilage statistique pour créer une base de référence. Il avertit sur les écarts par rapport aux lignes de base établies qui se conforment à un vecteur d’attaque potentielle.

De nombreuses équipes de sécurité et d’intervention utilisent une solution SIEM comme point de départ pour le triage et l’examen des alertes de sécurité. Avec Azure Log Integration, vous pouvez synchroniser quasi en temps réel les alertes Azure Security Center et les événements de sécurité des machines virtuelles, collectés par les journaux de diagnostic et d’audit Azure, avec votre solution SIEM ou Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Log Analytics est un service Azure qui vous permet de collecter et d’analyser les données générées par les ressources de vos environnements cloud et locaux. Il vous fournit des insights en temps réel à l’aide d’une recherche intégrée et des tableaux de bord personnalisés permettant d’analyser facilement des millions d’enregistrements sur l’ensemble de vos charges de travail et serveurs, quel que soit leur emplacement physique.

![Diagramme Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Au cœur de Log Analytics se trouve l’espace de travail Log Analytics, qui est hébergé dans Azure. Log Analytics collecte les données dans l’espace de travail à partir de sources connectées en configurant des sources de données et en ajoutant des solutions à votre abonnement. Les sources de données et les solutions créent toutes différents types d’enregistrements, chacune avec son propre jeu de propriétés. Malgré tout, les sources et les solutions peuvent quand même être analysées ensemble dans les requêtes sur l’espace de travail. Cette fonction vous permet d’utiliser les mêmes outils et méthodes pour travailler avec différentes données collectées par différentes sources.

Les sources connectées représentent les ordinateurs et autres ressources qui génèrent les données collectées par Log Analytics. Elles peuvent comprendre des agents installés sur des ordinateurs [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) et [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) directement connectés, ou des agents d’un [groupe d’administration System Center Operations Manager connecté](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics peut également collecter des données d’un [compte de stockage Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

Les [sources de données](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) représentent divers types de données collectées auprès de chaque source connectée. Elles comprennent les événements et les [données de performances](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) d’agents [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) et Linux, en plus des sources telles que les [journaux IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) et les [journaux de texte personnalisés](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Vous configurez chaque source de données que vous souhaitez collecter, et la configuration est automatiquement remise à chaque source connectée.

Il existe quatre façons de [collecter des journaux et des métriques pour les services Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) :

* Azure Diagnostics directement dans Log Analytics (**Diagnostics** dans le tableau suivant)

* Azure Diagnostics dans stockage Azure dans Log Analytics (**Stockage** dans le tableau suivant)

* Connecteurs pour les services Azure (**Connecteur** dans le tableau suivant)

* Scripts pour collecter puis publier les données dans Log Analytics (cellules vides dans le tableau suivant et pour les services qui ne sont pas listés)

| Service | Type de ressource | Journaux | Mesures | Solution |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnostics|Diagnostics|    [Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)[Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Connecteur|  Connecteur|  [Connecteur](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)[Application Insights (version préliminaire)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Comptes Azure Automation| Microsoft.Automation/<br>AutomationAccounts|    Diagnostics||       [Plus d’informations](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Comptes Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnostics|    Diagnostics||
|Services cloud classiques||       Stockage||       [Plus d’informations](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnostics|||
|Service Analytique Azure Data Lake| Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostics|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>accounts|   Diagnostics|||
|Espace de noms Azure Event Hub| Microsoft.EventHub/<br>namespaces|  Diagnostics|    Diagnostics||
|Azure IoT Hub| Microsoft.Devices/<br>IoTHubs||     Diagnostics||
|Azure Key Vault|   Microsoft.KeyVault/<br>vaults|  Diagnostics  || [Analytique Key Vault](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnostics|||
|Azure Logic Apps|  Microsoft.Logic/<br>workflows|  Diagnostics|    Diagnostics||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Group|   Microsoft.Network/<br>networksecuritygroups|Diagnostics||   [Azure Network Security Group Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Coffres de récupération|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services Analytics (version préliminaire)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Services de recherche|   Microsoft.Search/<br>searchServices|    Diagnostics|    Diagnostics||
|Espace de noms Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnostics|Diagnostics|    [Service Bus Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Stockage||    [Service Fabric Analytics (version préliminaire)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>bases de données||       Diagnostics||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Stockage|||         Script| [Azure Storage Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Machines virtuelles Azure|    Microsoft.Compute/<br>virtualMachines|  Extension|  Extension||
||||Diagnostics||
|Groupes identiques de machines virtuelles |    Microsoft.Compute/<br>virtualMachines    ||Diagnostics||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Batteries de serveurs web|Microsoft.Web/<br>serverfarms||   Diagnostics
|Sites web|  Microsoft.Web/<br>sites ||      Diagnostics|    [Plus d’informations](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Intégration des journaux avec les systèmes SIEM locaux

Avec Azure Log Integration, vous pouvez intégrer des journaux bruts issus de vos ressources Azure à vos systèmes SIEM (Security Information and Event Management) locaux. Les téléchargements AzLog ont été désactivés le 27 juin 2018. Pour obtenir des conseils pour évoluer, consultez la publication [Utiliser Azure Monitor pour intégrer avec des outils SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/).

![Diagramme Log Integration](./media/azure-log-audit/azure-log-audit-fig9.png)

Log Integration collecte des diagnostics Azure à partir de vos machines virtuelles Windows, de vos journaux d’activité Azure, de vos alertes Azure Security Center et de vos journaux du fournisseur de ressources Azure. Cette intégration offre un tableau de bord unifié pour toutes vos ressources, qu’elles soient locales ou dans le cloud, pour vous permettre d’agréger, de mettre en corrélation, d’analyser et d’alerter en cas d’événements de sécurité.

Log Integration prend actuellement en charge l’intégration des journaux d’activité Azure, des journaux des événements Windows des machines virtuelles Windows de votre abonnement Azure, des alertes Azure Security Center, des journaux de diagnostic Azure et des journaux d’audit Azure AD.

| Type de journal | Log Analytics prenant en charge JSON (Splunk, ArcSight et IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Journaux d’audit Azure AD|   OUI|
|Journaux d’activité| OUI|
|Alertes Security Center |OUI|
|Journaux de diagnostic (journaux des ressources)|  OUI|
|Journaux des machines virtuelles|   Oui, via les événements transmis et non via JSON|

[Bien démarrer avec Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) : Ce tutoriel vous guide tout au long de l’installation d’Azure Log Integration et de l’intégration des journaux du stockage Azure, des journaux d’activité Azure, des alertes Azure Security Center et des journaux d’audit Azure AD.

Scénarios d’intégration pour SIEM :

* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : Ce billet de blog vous montre comment configurer Azure Log Integration pour fonctionner avec les solutions de partenaire Splunk, HP ArcSight et IBM QRadar.

* [Forum aux questions sur Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) : Cet article répond à des questions sur l’intégration des journaux Azure.

* [Intégration des alertes Security Center avec Azure Log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) : Cet article décrit comment synchroniser les alertes Security Center, les événements de sécurité des machines virtuelles collectés par les journaux de diagnostic Azure et les journaux d’audit Azure avec votre solution SIEM ou Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

- [Audit et journalisation](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview) : protégez les données en conservant le niveau de visibilité et en répondant rapidement aux alertes de sécurité reçues.

- [Collecte des journaux de sécurité et d’audit dans Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/) : appliquez ces paramètres pour vous assurer que vos instances Azure collectent les journaux d’audit et de sécurité appropriés.

- [Configurer les paramètres d’audit pour une collection de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US) : si vous administrez une collection de sites, récupérez l’historique des actions de chaque utilisateur et l’historique des actions réalisées pendant une période donnée. 

- [Effectuer des recherches dans le journal d’audit dans le Centre de sécurité et de conformité Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US) : utilisez le Centre de sécurité et de conformité Office 365 pour effectuer des recherches dans le journal d’audit unifié et afficher les activités utilisateur et administrateur effectuées dans votre organisation Office 365.



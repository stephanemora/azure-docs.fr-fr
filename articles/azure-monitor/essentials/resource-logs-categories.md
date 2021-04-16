---
title: Services et catégories pris en charge des journaux de ressources Azure Monitor
description: Référence d’Azure Monitor Comprendre les services et le schéma d’événement pris en charge pour les journaux de ressource Azure.
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: a4ab4a2e425b752198223da5efd1b07466ab83d1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166936"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Catégories prises en charge pour les journaux de ressources Azure

> [!NOTE]
> Les journaux de ressources étaient auparavant appelés journaux de diagnostic. Le nom a été modifié en octobre 2019 parce que les types de journaux collectés par Azure Monitor ont évolué pour inclure plus que la seule ressource Azure.

Les [journaux de ressource Azure Monitor](../essentials/platform-logs-overview.md) sont des journaux d’activité générés par les services Azure, qui décrivent le fonctionnement de ces services ou ressources. Tous les journaux de ressource disponibles via Azure Monitor partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements.

Une combinaison du type de ressource (disponible dans la propriété `resourceId`) et du `category` permet d’identifier de manière unique un schéma. Il existe un schéma commun pour tous les journaux de ressources comportant des champs propres aux services ajoutés pour différentes catégories de journaux. Pour plus d’informations, consultez [Schémas commun et propres aux services pour les journaux de ressources Azure]().


## <a name="costs"></a>Coûts

Des coûts sont associés à l’envoi et au stockage de données dans Log Analytics, le Stockage Azure et/ou un hub d’événements. Vous pouvez être amené à supporter le coût lié à l’envoi des données vers ces emplacements et à leur conservation.  Les journaux de ressources sont un type de données que vous pouvez envoyer vers ces emplacements. 

L’exportation de certaines catégories de journaux de ressources vers ces emplacements occasionne un coût supplémentaire. Ces journaux contenant les coûts d’exportation sont répertoriés dans le tableau ci-dessous. Pour plus d’informations sur cette tarification, consultez la section Journaux de la plateforme dans la [page de tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource

Voici la liste des types de journaux disponibles pour chaque type de ressource. 

Il est possible que certaines catégories soient prises en charge uniquement pour des types de ressources spécifiques. Si vous avez le sentiment qu’il vous manque une ressource, consultez la documentation spécifique de celle-ci. Par exemple, les catégories Microsoft.Sql/servers/databases ne sont pas disponibles pour tous les types de bases de données. Pour plus d’informations, consultez les [informations sur la journalisation des diagnostics SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Si vous estimez qu’il manque quelque chose, vous pouvez ouvrir un commentaire GitHub au bas de cet article.

## <a name="microsoftaaddomainservices"></a>Microsoft.AAD/DomainServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AccountLogon|AccountLogon|Non|
|AccountManagement|AccountManagement|Non|
|DetailTracking|DetailTracking|Non|
|DirectoryServiceAccess|DirectoryServiceAccess|Non|
|LogonLogoff|LogonLogoff|Non|
|ObjectAccess|ObjectAccess|Non|
|PolicyChange|PolicyChange|Non|
|PrivilegeUse|PrivilegeUse|Non|
|SystemSecurity|SystemSecurity|Non|


## <a name="microsoftaadiamtenants"></a>microsoft.aadiam/tenants

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|SignIn|SignIn|Oui|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Moteur|Moteur|Non|
|Service|Service|Non|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|GatewayLogs|Journaux d’activité relatifs à la passerelle ApiManagement|Non|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|HttpRequest|Requêtes HTTP|Oui|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ApplicationConsole|Application console|Non|
|SystemLogs|Journaux système|Non|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuditEvent|Catégorie du journal des messages AuditEvent.|Non|
|ERR|Catégorie du journal des messages d’erreur.|Non|
|INF|Catégorie du journal des messages d’information.|Non|
|WRN|Catégorie du journal des messages d’avertissement.|Non|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DscNodeStatus|État du nœud DSC|Non|
|JobLogs|Journaux d’activité de travail|Non|
|JobStreams|Flux de travail|Non|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft.AutonomousDevelopmentPlatform/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Audit|Oui|
|En fonctionnement|En fonctionnement|Oui|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ServiceLog|Journaux d’activité de service|Non|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Non|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Non|
|BaiJobEvent|BaiJobEvent|Non|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BlockchainApplication|Application blockchain|Non|
|FabricOrderer|Ordonnanceur de structure|Non|
|FabricPeer|Pair de structure|Non|
|Proxy|Proxy|Non|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BlockchainApplication|Application blockchain|Non|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BotRequest|Demandes des canaux au bot|Non|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|WebApplicationFirewallLogs|Journaux du pare-feu d’applications web|Non|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AzureCdnAccessLog|Journal d’accès à Azure CDN|Non|
|FrontDoorAccessLog|Journal des accès Frontdoor|Oui|
|FrontDoorHealthProbeLog|Journal de sonde d’intégrité FrontDoor|Oui|
|FrontDoorWebApplicationFirewallLog|Journal WebApplicationFirewall de FrontDoor|Oui|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|CoreAnalytics|Obtient les métriques du point de terminaison, par exemple, la bande passante, la sortie, etc.|Non|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Événement de flux de règle de groupe de sécurité réseau|Événement de flux de règle de groupe de sécurité réseau|Non|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Journaux d’audit|Non|
|RequestResponse|Journaux d’activité des requêtes et des réponses|Non|
|Trace|Journaux de suivi|Non|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuthOperational|Journaux d’authentification opérationnels|Oui|
|ChatOperational|Journaux de conversation opérationnels|Non|
|SMSOperational|Journaux SMS opérationnels|Non|
|Usage|Enregistrements d’utilisation|Non|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ContainerRegistryLoginEvents|Événements de connexion|Non|
|ContainerRegistryRepositoryEvents|Journaux RepositoryEvent|Non|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|cluster-autoscaler|Autoscaler de cluster Kubernetes|Non|
|guard|guard|Non|
|kube-apiserver|Serveur d’API Kubernetes|Non|
|kube-audit|Audit Kubernetes|Non|
|kube-audit-admin|Journaux d’administration d’audit Kubernetes|Non|
|kube-controller-manager|Gestionnaire de contrôleur Kubernetes|Non|
|kube-scheduler|Planificateur Kubernetes|Non|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuditLogs|Journaux d’audit pour les appels MiniRP|Non|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft.D365CustomerInsights/instances

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Événements d'audit|Non|
|En fonctionnement|Événements opérationnels|Non|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|accounts|Comptes Databricks|Non|
|clusters|Clusters Databricks|Non|
|dbfs|Système de fichiers Databricks|Non|
|instancePools|Pools d’instances|Non|
|jobs|Travaux Databricks|Non|
|notebook|Databricks Notebook|Non|
|secrets|Secrets Databricks|Non|
|sqlPermissions|SQLPermissions Databricks|Non|
|ssh|SSH Databricks|Non|
|espace de travail|Espace de travail Databricks|Non|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|CollaborationAudit|Audit de collaboration|Oui|
|DataAssets|Ressources de données|Non|
|Pipelines|Pipelines|Non|
|Proposals|Propositions|Non|
|scripts ;|scripts ;|Non|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ActivityRuns|Journal d’exécutions d’activités de pipeline|Non|
|PipelineRuns|Journal d’exécutions de pipeline|Non|
|SandboxActivityRuns|Journal des exécutions d’activité de bac à sable|Oui|
|SandboxPipelineRuns|Journal d’exécutions de pipeline de bac à sable|Oui|
|SSISIntegrationRuntimeLogs|Journaux du runtime d’intégration SSIS|Non|
|SSISPackageEventMessageContext|Contexte du message d’événement du package SSIS|Non|
|SSISPackageEventMessages|Messages d’événements du package SSIS|Non|
|SSISPackageExecutableStatistics|Statistiques de l’exécutable du package SSIS|Non|
|SSISPackageExecutionComponentPhases|Phases du composant d’exécution du package SSIS|Non|
|SSISPackageExecutionDataStatistics|Statistiques des données d’exécution du package SSIS|Non|
|TriggerRuns|Journal d’exécutions de déclencheur|Non|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Journaux d’audit|Non|
|Demandes|Journaux d’activité de requête|Non|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Journaux d’audit|Non|
|Demandes|Journaux d’activité de requête|Non|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ReceivedShareSnapshots|Instantanés de partage reçus|Non|
|SentShareSnapshots|Instantanés de partage envoyés|Non|
|Partages|Partages|Non|
|ShareSubscriptions|Abonnements de partage|Non|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|MySqlAuditLogs|Journaux d’audit MariaDB|Non|
|MySqlSlowLogs|Journaux du serveur MariaDB|Non|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|MySqlAuditLogs|Journaux d’audit MySQL|Non|
|MySqlSlowLogs|Journaux des requêtes lentes MySQL|Non|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|MySqlAuditLogs|Journaux d’audit MySQL|Non|
|MySqlSlowLogs|Journaux d’activité MySQL Server|Non|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|Non|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|Oui|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|Non|
|QueryStoreRuntimeStatistics|Statistiques de runtime du magasin des requêtes PostgreSQL|Non|
|QueryStoreWaitStatistics|Statistiques d’attente du magasin des requêtes PostgreSQL|Non|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|Non|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Point de contrôle|Point de contrôle|Non|
|Error|Error|Non|
|Gestion|Gestion|Non|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Non|
|Point de contrôle|Point de contrôle|Non|
|Connexion|Connexion|Non|
|Error|Error|Non|
|HostRegistration|HostRegistration|Non|
|Gestion|Gestion|Non|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Point de contrôle|Point de contrôle|Non|
|Error|Error|Non|
|Flux|Flux|Non|
|Gestion|Gestion|Non|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|C2DCommands|Commandes C2D|Non|
|C2DTwinOperations|Opérations de jumeaux C2D|Non|
|Configurations|Configurations|Non|
|Connexions|Connexions|Non|
|Opérations de jumeaux D2C|Opérations de jumeaux D2C|Non|
|DeviceIdentityOperations|Opérations d’identité des appareils|Non|
|DeviceStreams|Flux d’appareils (préversion)|Non|
|DeviceTelemetry|Télémétrie d’appareil|Non|
|DirectMethods|Méthodes directes|Non|
|DistributedTracing|Traçage distribué (préversion)|Non|
|FileUploadOperations|Opérations de chargement de fichier|Non|
|JobsOperations|Opérations de travaux|Non|
|Itinéraires|Itinéraires|Non|
|TwinQueries|Requêtes de jumeaux|Non|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|C2DCommands|Commandes C2D|Non|
|C2DTwinOperations|Opérations de jumeaux C2D|Non|
|Configurations|Configurations|Non|
|Connexions|Connexions|Non|
|Opérations de jumeaux D2C|Opérations de jumeaux D2C|Non|
|DeviceIdentityOperations|Opérations d’identité des appareils|Non|
|DeviceStreams|Flux d’appareils (préversion)|Non|
|DeviceTelemetry|Télémétrie d’appareil|Non|
|DirectMethods|Méthodes directes|Non|
|DistributedTracing|Traçage distribué (préversion)|Non|
|FileUploadOperations|Opérations de chargement de fichier|Non|
|JobsOperations|Opérations de travaux|Non|
|Itinéraires|Itinéraires|Non|
|TwinQueries|Requêtes de jumeaux|Non|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeviceOperations|Opérations sur les appareils|Non|
|ServiceOperations|Opérations de service|Non|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Non|
|EventRoutesOperation|EventRoutesOperation|Non|
|ModelsOperation|ModelsOperation|Non|
|QueryOperation|QueryOperation|Non|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|CassandraRequests|CassandraRequests|Non|
|ControlPlaneRequests|ControlPlaneRequests|Non|
|DataPlaneRequests|DataPlaneRequests|Non|
|GremlinRequests|GremlinRequests|Non|
|MongoRequests|MongoRequests|Non|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Non|
|PartitionKeyStatistics|PartitionKeyStatistics|Non|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Non|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeliveryFailures|Journaux d’échec de remise|Non|
|PublishFailures|Journaux d’échec de publication|Non|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeliveryFailures|Journaux d’échec de remise|Non|
|PublishFailures|Journaux d’échec de publication|Non|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeliveryFailures|Journaux d’échec de remise|Non|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeliveryFailures|Journaux d’échec de remise|Non|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DeliveryFailures|Journaux d’échec de remise|Non|
|PublishFailures|Journaux d’échec de publication|Non|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ArchiveLogs|Journaux d’activité d’archivage|Non|
|AutoScaleLogs|Journaux d’activité de mise à l’échelle automatique|Non|
|CustomerManagedKeyUserLogs|Journaux de clés gérées par le client|Non|
|EventHubVNetConnectionEvent|Journaux de connexion de réseau virtuel/Filtrage IP|Non|
|KafkaCoordinatorLogs|Journaux du coordinateur Kafka|Non|
|KafkaUserErrorLogs|Journaux des erreurs d’utilisateur Kafka|Non|
|OperationalLogs|Journaux d’activité des opérations|Non|


## <a name="microsoftexperimentationexperimentworkspaces"></a>microsoft.experimentation/experimentWorkspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Requête|Requête|Non|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuditLogs|Journaux d’audit|Non|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AutoscaleEvaluations|Évaluations de mise à l’échelle automatique|Non|
|AutoscaleScaleActions|Actions de mise à l’échelle automatique|Non|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AppAvailabilityResults|Résultats de la disponibilité|Non|
|AppBrowserTimings|Minutages du navigateur|Non|
|AppDependencies|Les dépendances|Non|
|AppEvents|Événements|Non|
|AppExceptions|Exceptions|Non|
|AppMetrics|Mesures|Non|
|AppPageViews|Affichages de page|Non|
|AppPerformanceCounters|Compteurs de performance|Non|
|AppRequests|Demandes|Non|
|AppSystemEvents|Événements système|Non|
|AppTraces|Traces|Non|


## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuditEvent|Événement d’audit|Non|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AuditEvent|Journaux d’audit|Non|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Commande|Commande|Non|
|FailedIngestion|Opérations d’ingestion ayant échoué|Non|
|IngestionBatching|Traitement par lot de l’ingestion|Non|
|Requête|Requête|Non|
|SucceededIngestion|Opérations d’ingestion réussies|Non|
|TableDetails|Détails de la table|Non|
|TableUsageStatistics|Statistiques d’utilisation de la table|Non|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|IntegrationAccountTrackingEvents|Suivi des événements de compte d’intégration|Non|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|WorkflowRuntime|Événements de diagnostic de runtime de workflow|Non|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Non|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Non|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Non|
|AmlComputeJobEvent|AmlComputeJobEvent|Non|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Non|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|KeyDeliveryRequests|Service de remise de clé|Non|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ApplicationGatewayAccessLog|Journal d’accès à la passerelle d’application|Non|
|ApplicationGatewayFirewallLog|Journal de pare-feu de la passerelle d’application|Non|
|ApplicationGatewayPerformanceLog|Journal de performance de la passerelle d’application|Non|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AzureFirewallApplicationRule|Règle d’application de pare-feu Azure|Non|
|AzureFirewallDnsProxy|Proxy DNS du Pare-feu Azure|Non|
|AzureFirewallNetworkRule|Règle de réseau de pare-feu Azure|Non|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BastionAuditLogs|Journaux d’audit de Bastion|Non|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|PeeringRouteLog|Journaux d’activité de table de routage de Peering|Non|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|FrontdoorAccessLog|Journal des accès Frontdoor|Non|
|FrontdoorWebApplicationFirewallLog|Journal de pare-feu d’applications web Frontdoor|Non|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|LoadBalancerAlertEvent|Événements d’alerte d’équilibreur de charge|Non|
|LoadBalancerProbeHealthStatus|État d’intégrité de la sonde d’équilibreur de charge|Non|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|NetworkSecurityGroupEvent|Événement de groupe de sécurité réseau|Non|
|NetworkSecurityGroupFlowEvent|Événement de flux de règle de groupe de sécurité réseau|Non|
|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|Non|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|Non|
|IKEDiagnosticLog|Journaux de diagnostics IKE|Non|
|P2SDiagnosticLog|Journaux de diagnostics PS2|Non|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DDoSMitigationFlowLogs|Journaux d’activité des flux des décisions d’atténuation DDoS|Non|
|DDoSMitigationReports|Rapports des mesures d’atténuation DDoS|Non|
|DDoSProtectionNotifications|Notifications de protection DDoS|Non|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ProbeHealthStatusEvents|Événement de résultats d’intégrité de sondage Traffic Manager|Non|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|Non|
|IKEDiagnosticLog|Journaux de diagnostics IKE|Non|
|P2SDiagnosticLog|Journaux de diagnostics PS2|Non|
|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|Non|
|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|Non|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|VMProtectionAlerts|Alertes de protection de machine virtuelle|Non|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|Non|
|IKEDiagnosticLog|Journaux de diagnostics IKE|Non|
|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|Non|
|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|Non|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|OperationalLogs|Journaux d’activité des opérations|Non|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Audit|Journaux d’audit|Non|


## <a name="microsoftpowerbitenants"></a>Microsoft.PowerBI/tenants

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Moteur|Moteur|Non|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft.PowerBI/tenants/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Moteur|Moteur|Non|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Moteur|Moteur|Non|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Non|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AddonAzureBackupAlerts|Extension Données d’alerte de sauvegarde Azure|Non|
|AddonAzureBackupJobs|Extension Données du travail de sauvegarde Azure|Non|
|AddonAzureBackupPolicy|Extension Données de stratégie de sauvegarde Azure|Non|
|AddonAzureBackupProtectedInstance|Extension Données d’instance protégée de sauvegarde Azure|Non|
|AddonAzureBackupStorage|Extension Données de stockage de sauvegarde Azure|Non|
|AzureBackupReport|Données de rapport de sauvegarde Azure|Non|
|AzureSiteRecoveryEvents|Événements Azure Site Recovery|Non|
|AzureSiteRecoveryJobs|Travaux Azure Site Recovery|Non|
|AzureSiteRecoveryProtectedDiskDataChurn|Activité des données de disque protégé Azure Site Recovery|Non|
|AzureSiteRecoveryRecoveryPoints|Points de récupération Azure Site Recovery|Non|
|AzureSiteRecoveryReplicatedItems|Éléments répliqués d’Azure Site Recovery|Non|
|AzureSiteRecoveryReplicationDataUploadRate|Taux de chargement de données de réplication Azure Site Recovery|Non|
|AzureSiteRecoveryReplicationStats|Statistiques de réplication Azure Site Recovery|Non|
|CoreAzureBackup|Données de sauvegarde Azure de base|Non|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|HybridConnectionsEvent|Événements HybridConnections|Non|
|HybridConnectionsLogs|HybridConnectionsLogs|Non|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|OperationLogs|Journaux d’activité des opérations|Non|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|OperationalLogs|Journaux d’activité des opérations|Non|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AllLogs|Journaux d’Azure SignalR Service.|Non|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AllLogs|Journaux du service Azure Web PubSub|Oui|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DevOpsOperationsAudit|Journaux d’audit des opérations de Devops|Non|
|ResourceUsageStats|Statistiques d’utilisation des ressources|Non|
|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|Non|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Erreurs|Erreurs|Non|
|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|Non|
|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|Non|
|SQLInsights|SQL Insights|Non|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AutomaticTuning|Réglage automatique|Non|
|Blocs|Blocs|Non|
|DatabaseWaitStatistics|Statistiques d’attente de base de données|Non|
|Blocages|Blocages|Non|
|DevOpsOperationsAudit|Journaux d’audit des opérations de Devops|Non|
|DmsWorkers|Rôles de travail Dms|Non|
|Erreurs|Erreurs|Non|
|ExecRequests|Requêtes d’exécution|Non|
|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|Non|
|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|Non|
|RequestSteps|Étapes de la requête|Non|
|SQLInsights|SQL Insights|Non|
|SqlRequests|Requêtes SQL|Non|
|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|Non|
|Délais d’expiration|Délais d’expiration|Non|
|Attend|Attend|Non|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|StorageDelete|StorageDelete|Oui|
|StorageRead|StorageRead|Oui|
|StorageWrite|StorageWrite|Oui|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|StorageDelete|StorageDelete|Oui|
|StorageRead|StorageRead|Oui|
|StorageWrite|StorageWrite|Oui|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|StorageDelete|StorageDelete|Oui|
|StorageRead|StorageRead|Oui|
|StorageWrite|StorageWrite|Oui|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|StorageDelete|StorageDelete|Oui|
|StorageRead|StorageRead|Oui|
|StorageWrite|StorageWrite|Oui|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Création|Création|Non|
|Exécution|Exécution|Non|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BuiltinSqlReqsEnded|Demandes terminées pour le pool SQL intégré|Non|
|GatewayApiRequests|Demandes d’API de passerelle Synapse|Non|
|IntegrationActivityRuns|Exécutions d’activités d’intégration|Oui|
|IntegrationPipelineRuns|Exécutions de pipelines d’intégration|Oui|
|IntegrationTriggerRuns|Exécutions de déclencheurs d’intégration|Oui|
|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|Non|
|SynapseRbacOperations|Opérations RBAC Synapse|Non|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|BigDataPoolAppsEnded|Applications terminées pour le pool Big Data|Non|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|DmsWorkers|Rôles de travail Dms|Non|
|ExecRequests|Requêtes d’exécution|Non|
|RequestSteps|Étapes de la requête|Non|
|SqlRequests|Requêtes SQL|Non|
|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|Non|
|Attend|Attend|Non|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Entrée|Entrée|Non|
|Gestion|Gestion|Non|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|Entrée|Entrée|Non|
|Gestion|Gestion|Non|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Journaux de plateforme de l’environnement App Service|Non|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Journaux d’audit antivirus de rapport|Non|
|AppServiceAppLogs|Journaux d’application App Service|Non|
|AppServiceAuditLogs|Journaux d’audit d’accès|Non|
|AppServiceConsoleLogs|Journaux de la console App Service|Non|
|AppServiceFileAuditLogs|Journaux d’audit de modification de contenu de site|Non|
|AppServiceHTTPLogs|Journaux HTTP|Non|
|AppServiceIPSecAuditLogs|Journaux d’audit IPSecurity|Non|
|AppServicePlatformLogs|Journaux de la plateforme App Service|Non|
|FunctionAppLogs|Journaux d’application de fonction|Non|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Category|Nom d’affichage de la catégorie|Coûts d’exportation|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Journaux d’audit antivirus de rapport|Non|
|AppServiceAppLogs|Journaux d’application App Service|Non|
|AppServiceAuditLogs|Journaux d’audit d’accès|Non|
|AppServiceConsoleLogs|Journaux de la console App Service|Non|
|AppServiceFileAuditLogs|Journaux d’audit de modification de contenu de site|Non|
|AppServiceHTTPLogs|Journaux HTTP|Non|
|AppServiceIPSecAuditLogs|Journaux d’audit IPSecurity|Non|
|AppServicePlatformLogs|Journaux de la plateforme App Service|Non|
|FunctionAppLogs|Journaux d’application de fonction|Non|


## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de ressources](../essentials/platform-logs-overview.md)
* [Diffuser en continu les journaux de ressource vers **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Modifier les paramètres de diagnostic de journal de ressource à l’aide de l’API REST Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analyser les journaux d’activité du stockage Azure avec Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)


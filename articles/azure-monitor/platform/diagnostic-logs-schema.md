---
title: Services et schémas pris en charge pour les journaux de ressource Azure
description: Découvrez les services pris en charge et le schéma d’événement pour les journaux de ressource Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: de102c5dc4104aafc44b87b14aeea0b30cb7c083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79226341"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Services, schémas et catégories pris en charge pour les journaux de ressource Azure

> [!NOTE]
> Les journaux de ressource étaient auparavant appelés journaux de diagnostic.

Les [journaux de ressource Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) sont des journaux d’activité générés par les services Azure, qui décrivent le fonctionnement de ces services ou ressources. Tous les journaux de ressource disponibles via Azure Monitor partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements.

Une combinaison du type de ressource (disponible dans la propriété `resourceId`) et du `category` permet d’identifier de manière unique un schéma. Cet article décrit le schéma de niveau supérieur pour les journaux de ressource et les liens vers les schémas pour chaque service.

## <a name="top-level-resource-logs-schema"></a>Schéma de niveau supérieur des journaux de ressource

| Name | Obligatoire ou facultatif | Description |
|---|---|---|
| time | Obligatoire | Horodatage (heure UTC) de l’événement. |
| resourceId | Obligatoire | ID de la ressource qui a émis l’événement. Pour les services abonnés, cet ID prend la forme /tenants/tenant-id/providers/provider-name. |
| tenantId | Obligatoire pour les journaux d’activité de l’abonné | ID d’abonné de l’abonné Active Directory auquel cet événement est lié. Cette propriété est utilisée uniquement pour les journaux d’activité de niveau abonné ; elle n’apparaît pas dans les journaux d’activité de niveau ressource. |
| operationName | Obligatoire | Nom de l’opération représentée par cet événement. Si l’événement représente une opération RBAC, il s’agit du nom de l’opération RBAC (par ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Généralement modélisée sous la forme d’une opération du Gestionnaire de ressources, même s’il ne s’agit pas d’opérations réelles documentées du Gestionnaire des ressources (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facultatif | Version d’api associée à l’opération, si operationName a été effectuée à l’aide d’une API (par ex. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). S’il n’existe aucune API qui corresponde à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer. |
| catégorie | Obligatoire | Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux d’activité sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob de propriétés d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique. Les catégories de journaux classiques sont « Audit » « opérationnel » « Exécution » et « Demande ». |
| resultType | Facultatif | État de l’événement. Les valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active et Resolved. |
| resultSignature | Facultatif | Sous-état de l’événement. Si cette opération correspond à un appel d’API REST, il s’agit du code d’état HTTP de l’appel REST correspondant. |
| resultDescription | Facultatif | Description textuelle statique de cette opération, par ex. « Obtenir le fichier de stockage ». |
| durationMS | Facultatif | Durée de l’opération en millisecondes. |
| callerIpAddress | Facultatif | Adresse IP de l’appelant, si l’opération correspond à un appel d’API qui proviendrait d’une entité avec une adresse IP disponible publiquement. |
| correlationId | Facultatif | GUID utilisé pour regrouper un ensemble d’événements associés. En règle générale, si deux événements ont le même operationName, mais deux états différents (par ex., « Started » et « Succeeded »), ils partagent le même ID de corrélation. Cela peut également représenter d’autres relations entre les événements. |
| identité | Facultatif | Objet blob JSON qui décrit l’identité de l’utilisateur ou d’une application qui a effectué l’opération. En général, cela inclut l’autorisation et les revendications/Jeton JWT issus d’active directory. |
| Level | Facultatif | Niveau de gravité de l’événement. Doit être Information, Avertissement, Erreur, ou Critique. |
| location | Facultatif | Région de la ressource générant l’événement, par ex. USA Est ou France Sud |
| properties | Facultatif | Toutes les propriétés étendues associées à cette catégorie particulière d’événements spécifiques. Toutes les propriétés personnalisées/uniques doivent être placées à l’intérieur de cette « Partie B » du schéma. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schémas spécifiques du service pour les journaux de ressource
Le schéma des journaux de diagnostic des ressources varie en fonction de la ressource et de la catégorie de journal d’activité. Cette liste répertorie tous les services qui mettent des journaux de ressource et des liens à disposition du service et un schéma spécifique de la catégorie lorsqu’il est disponible.

| Service | Schéma et documentation |
| --- | --- |
| Azure Active Directory | [Vue d’ensemble](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Schéma des journaux d’audit](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) et [Schéma des connexions](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestion des API | [Journaux de ressource de Gestion des API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Passerelles d’application |[Journalisation pour Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation d’Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database pour MySQL | [Journaux de diagnostic Azure Database pour MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database pour PostgreSQL | [Journaux d’Azure Database pour PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Explorateur de données Azure | [Journaux d’Azure Data Explorer](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Journalisation pour Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Journalisation pour Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Réseau de distribution de contenu | [Journaux Azure pour CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Journalisation Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Surveiller les fabriques de données à l’aide d’Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accès aux journaux d’Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accès aux journaux d’Azure Data Lake Storage](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Journaux d’Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma non disponible. |
| Pare-feu Azure | Schéma non disponible. |
| IoT Hub | [Opérations IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Journalisation d’Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Journalisation d’Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Analyse des journaux de l'équilibreur de charge Azure](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Group |[Analytique des journaux pour les groupes de sécurité réseau (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Protection DDOS | [Gérer le service Azure DDoS Protection standard](../../virtual-network/manage-ddos-protection.md) |
| Power BI dédiées | [Journalisation pour Power BI Embedded dans Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Modèle de données pour la sauvegarde Azure](../../backup/backup-azure-reports-data-model.md)|
| Recherche |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../../search/search-traffic-analytics.md) |
| Service Bus |[Journaux d’Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Journalisation d’Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Journaux de tâches](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma des journaux de Traffic Manager](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | Schéma non disponible. |
| Passerelles de réseau virtuel | Schéma non disponible. |

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource

Il est possible que certaines catégories soient prises en charge uniquement pour des types de ressources spécifiques. Voici la liste de tous les types disponibles dans une forme donnée.  Par exemple, les catégories Microsoft.Sql/servers/databases ne sont pas disponibles pour tous les types de bases de données. Pour plus d’informations, consultez les [informations sur la journalisation des diagnostics SQL Database](../../sql-database/sql-database-metrics-diag-logging.md). 

|Type de ressource|Category|Nom d’affichage de la catégorie|
|---|---|---|
|Microsoft.AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft.AAD/domainServices|AccountManagement|AccountManagement|
|Microsoft.AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft.AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft.AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|AccountLogon|AccountLogon|
|microsoft.aadiam/tenants|SignIn|SignIn|
|Microsoft.AnalysisServices/servers|Moteur|Moteur|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Journaux d’activité relatifs à la passerelle ApiManagement|
|Microsoft.AppPlatform/Spring|ApplicationConsole|Application console|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux d’activité de travail|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Automation/automationAccounts|DscNodeStatus|État du nœud DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux d’activité de service|
|Microsoft.BatchAI/workspaces|BaiClusterEvent|BaiClusterEvent|
|Microsoft.BatchAI/workspaces|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft.BatchAI/workspaces|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication|Application blockchain|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtient les métriques du point de terminaison, par exemple, la bande passante, la sortie, etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Événement de flux de règle de groupe de sécurité réseau|Événement de flux de règle de groupe de sécurité réseau|
|Microsoft.CognitiveServices/accounts|Audit|Journaux d’audit|
|Microsoft.CognitiveServices/accounts|RequestResponse|Journaux d’activité des requêtes et des réponses|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|Journaux RepositoryEvent (préversion)|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|Événements de connexion (préversion)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Serveur d’API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Gestionnaire de contrôleur Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Planificateur Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-audit|Audit Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Autoscaler de cluster Kubernetes|
|Microsoft.Databricks/workspaces|dbfs|Système de fichiers Databricks|
|Microsoft.Databricks/workspaces|clusters|Clusters Databricks|
|Microsoft.Databricks/workspaces|accounts|Comptes Databricks|
|Microsoft.Databricks/workspaces|jobs|Travaux Databricks|
|Microsoft.Databricks/workspaces|notebook|Databricks Notebook|
|Microsoft.Databricks/workspaces|ssh|SSH Databricks|
|Microsoft.Databricks/workspaces|espace de travail|Espace de travail Databricks|
|Microsoft.Databricks/workspaces|secrets|Secrets Databricks|
|Microsoft.Databricks/workspaces|sqlPermissions|SQLPermissions Databricks|
|Microsoft.Databricks/workspaces|instancePools|Pools d’instances|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/factories|ActivityRuns|Journal d’exécutions d’activités de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Journal d’exécutions de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Journal d’exécutions de déclencheur|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Demandes|Journaux d’activité de requête|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Demandes|Journaux d’activité de requête|
|Microsoft.DataShare/accounts|Partages|Partages|
|Microsoft.DataShare/accounts|ShareSubscriptions|Abonnements de partage|
|Microsoft.DataShare/accounts|SentShareSnapshots|Instantanés de partage envoyés|
|Microsoft.DataShare/accounts|ReceivedShareSnapshots|Instantanés de partage reçus|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Journaux d’activité MySQL Server|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Journaux d’audit MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Statistiques de runtime du magasin des requêtes PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Statistiques d’attente du magasin des requêtes PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Journaux d’activité de serveur PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statistiques de runtime du magasin des requêtes PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Statistiques d’attente du magasin des requêtes PostgreSQL|
|Microsoft.DesktopVirtualization/workspaces|Point de contrôle|Point de contrôle|
|Microsoft.DesktopVirtualization/workspaces|Error|Error|
|Microsoft.DesktopVirtualization/workspaces|Gestion|Gestion|
|Microsoft.DesktopVirtualization/workspaces|Flux|Flux|
|Microsoft.DesktopVirtualization/applicationGroups|Point de contrôle|Point de contrôle|
|Microsoft.DesktopVirtualization/applicationGroups|Error|Error|
|Microsoft.DesktopVirtualization/applicationGroups|Gestion|Gestion|
|Microsoft.DesktopVirtualization/hostPools|Point de contrôle|Point de contrôle|
|Microsoft.DesktopVirtualization/hostPools|Error|Error|
|Microsoft.DesktopVirtualization/hostPools|Gestion|Gestion|
|Microsoft.DesktopVirtualization/hostPools|Connexion|Connexion|
|Microsoft.DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|Connexions|Connexions|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Télémétrie d’appareil|
|Microsoft.Devices/IotHubs|C2DCommands|Commandes C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Opérations d’identité des appareils|
|Microsoft.Devices/IotHubs|FileUploadOperations|Opérations de chargement de fichier|
|Microsoft.Devices/IotHubs|Itinéraires|Itinéraires|
|Microsoft.Devices/IotHubs|Opérations de jumeaux D2C|Opérations de jumeaux D2C|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Opérations de jumeaux C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Requêtes de jumeaux|
|Microsoft.Devices/IotHubs|JobsOperations|Opérations de travaux|
|Microsoft.Devices/IotHubs|DirectMethods|Méthodes directes|
|Microsoft.Devices/IotHubs|DistributedTracing|Traçage distribué (préversion)|
|Microsoft.Devices/IotHubs|Configurations|Configurations|
|Microsoft.Devices/IotHubs|DeviceStreams|Flux d’appareils (préversion)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Opérations sur les appareils|
|Microsoft.Devices/provisioningServices|ServiceOperations|Opérations de service|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/services|AuditEvent|AuditEvent log|
|Microsoft.EnterpriseKnowledgeGraph/services|DataIssue|Journal DataIssue|
|Microsoft.EnterpriseKnowledgeGraph/services|Demandes|Journal de configuration|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’activité d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux d’activité des opérations|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Journaux d’activité de mise à l’échelle automatique|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Journaux du coordinateur Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Journaux des erreurs d’utilisateur Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Journaux de connexion de réseau virtuel/Filtrage IP|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Journaux de clés gérées par le client|
|Microsoft.HealthcareApis/services|AuditLogs|Journaux d’audit|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Évaluations de mise à l’échelle automatique|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Actions de mise à l’échelle automatique|
|Microsoft.IoTSpaces/Graph|Trace|Trace|
|Microsoft.IoTSpaces/Graph|En fonctionnement|En fonctionnement|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Entrée|Entrée|
|Microsoft.IoTSpaces/Graph|Sortie|Sortie|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Kusto/Clusters|SucceededIngestion|Opérations d’ingestion réussies|
|Microsoft.Kusto/Clusters|FailedIngestion|Opérations d’ingestion ayant échoué|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic de runtime de workflow|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Suivi des événements de compte d’intégration|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|Service de remise de clé|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événement de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Événement de flux de règle de groupe de sécurité réseau|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notifications de protection DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Journaux d’activité des flux des décisions d’atténuation DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Rapports des mesures d’atténuation DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertes de protection de machine virtuelle|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal d’accès à la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de performance de la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal de pare-feu de la passerelle d’application|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Règle d’application de pare-feu Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Règle de réseau de pare-feu Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Journaux de diagnostics IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Journaux de diagnostics PS2|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Événement de résultats d’intégrité de sondage Traffic Manager|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Journaux d’activité de table de routage de Peering|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Journaux de diagnostics IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Journal des accès Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Journal de pare-feu d’applications web Frontdoor|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Journaux de diagnostics IKE|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|Journaux de diagnostics PS2|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Journaux d’audit de Bastion|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte d’équilibreur de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État d’intégrité de la sonde d’équilibreur de charge|
|Microsoft.PowerBIDedicated/capacities|Moteur|Moteur|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Données de rapport de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Données de sauvegarde Azure de base|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Extension Données du travail de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Extension Données d’alerte de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Extension Données de stratégie de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Extension Données de stockage de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Extension Données d’instance protégée de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Travaux Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Événements Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Éléments répliqués d’Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiques de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Points de récupération Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taux de chargement de données de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Activité des données de disque protégé Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Journaux d’activité des opérations|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux d’activité des opérations|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Réglage automatique|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|
|Microsoft.Sql/servers/databases|Erreurs|Erreurs|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiques d’attente de base de données|
|Microsoft.Sql/servers/databases|Délais d’expiration|Délais d’expiration|
|Microsoft.Sql/servers/databases|Blocs|Blocs|
|Microsoft.Sql/servers/databases|Blocages|Blocages|
|Microsoft.Sql/servers/databases|Audit|Journaux d’audit|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Rôles de travail Dms|
|Microsoft.Sql/servers/databases|ExecRequests|Requêtes d’exécution|
|Microsoft.Sql/servers/databases|RequestSteps|Étapes de la requête|
|Microsoft.Sql/servers/databases|SqlRequests|Requêtes SQL|
|Microsoft.Sql/servers/databases|Attend|Attend|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistiques d’utilisation des ressources|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|
|Microsoft.Sql/managedInstances/databases|Erreurs|Erreurs|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Exécution|Exécution|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|
|microsoft.web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Journaux de plateforme de l’environnement App Service|
|microsoft.web/sites|FunctionAppLogs|Journaux d’application de fonction|
|microsoft.web/sites|AppServiceHTTPLogs|Journaux HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Journaux de la console App Service|
|microsoft.web/sites|AppServiceAppLogs|Journaux d’application App Service|
|microsoft.web/sites|AppServiceFileAuditLogs|Journaux d’audit de modification de contenu de site|
|microsoft.web/sites|AppServiceAuditLogs|Journaux d’audit d’accès|
|microsoft.web/sites/slots|FunctionAppLogs|Journaux d’application de fonction|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Journaux HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Journaux de la console|
|microsoft.web/sites/slots|AppServiceAppLogs|Journaux des applications|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Journaux d’audit de modification de contenu de site|
|microsoft.web/sites/slots|AppServiceAuditLogs|Journaux d’audit d’accès|

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de ressources](../../azure-monitor/platform/platform-logs-overview.md)
* [Diffuser en continu les journaux de ressource vers **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Modifier les paramètres de diagnostic de journal de ressource à l’aide de l’API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analyser les journaux d’activité du stockage Azure avec Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

---
title: Services et schémas pris en charge par les journaux de diagnostic Azure
description: Découvrez les services pris en charge et le schéma d’événement pour les journaux de diagnostic Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 7/06/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: f4bf77f07bd8f6b8172798ec3faf8c0bdaf3d3f5
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921227"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure

Les [journaux de diagnostic des ressources Azure](monitoring-overview-of-diagnostic-logs.md) sont des journaux générés par vos ressources Azure qui décrivent le fonctionnement de cette ressource. Tous les journaux de diagnostic disponibles via Azure Monitor partagent un schéma commun de niveau supérieur, avec la flexibilité pour chaque service d’émettre des propriétés uniques pour ses propres événements.

Une combinaison du type de ressource (disponible dans la propriété `resourceId`) et du `category` permet d’identifier de manière unique un schéma. Cet article décrit le schéma de niveau supérieur pour les journaux de diagnostic et les liens vers les schémas pour chaque service.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma des journaux de diagnostic de niveau supérieur

| NOM | Obligatoire ou facultatif | Description |
|---|---|---|
| time | Obligatoire | Horodatage (heure UTC) de l’événement. |
| ResourceId | Obligatoire | ID de la ressource qui a émis l’événement. |
| operationName | Obligatoire | Nom de l’opération représentée par cet événement. Si l’événement représente une opération RBAC, il s’agit du nom de l’opération RBAC (par ex. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Généralement modélisée sous la forme d’une opération du Gestionnaire de ressources, même s’il ne s’agit pas d’opérations réelles documentées du Gestionnaire des ressources (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Facultatif | Version d’api associée à l’opération, si operationName a été effectuée à l’aide d’une API (par ex. http://myservice.windowsazure.net/object?api-version=2016-06-01). S’il n’existe aucune API qui corresponde à cette opération, la version représente la version de cette opération si les propriétés associées à l’opération viennent à changer. |
| category | Obligatoire | Catégorie de journal de l’événement. La catégorie est la granularité selon laquelle vous pouvez activer ou désactiver des journaux sur une ressource particulière. Les propriétés qui apparaissent dans l’objet blob de propriétés d’un événement sont les mêmes au sein d’un type de ressource et d’une catégorie de journal spécifique. Les catégories de journaux classiques sont « Audit » « opérationnel » « Exécution » et « Demande ». |
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

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Schémas spécifiques du service pour les journaux de diagnostic des ressources
Le schéma des journaux de diagnostic des ressources varie en fonction de la ressource et de la catégorie de journal. Cette liste répertorie tous les services qui mettent des journaux de diagnostics et des liens à disposition du service et un schéma spécifique de la catégorie lorsqu’il est disponible.

| de diffusion en continu | Schéma et documentation |
| --- | --- |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gestion des API | [Journaux de diagnostic Gestion des API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Passerelles d’application |[Journalisation des diagnostics pour Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation des diagnostics Azure Batch](../batch/batch-diagnostics.md) |
| Réseau de distribution de contenu | [Journaux de diagnostic Azure pour CDN](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Journalisation Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Factory | [Surveiller les fabriques de données à l’aide d’Azure Monitor](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accès aux journaux de diagnostic d’Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Base de données pour PostgreSQL |  Schéma non disponible. |
| Event Hubs |[Journaux de diagnostic d’Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma non disponible. |
| IoT Hub | [Opérations IoT Hub](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Journalisation d’Azure Key Vault](../key-vault/key-vault-logging.md) |
| Load Balancer |[Analytique des journaux de l'équilibreur de charge Azure](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Group |[Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Protection DDOS | [Gérer le service Azure DDoS Protection standard](../virtual-network/manage-ddos-protection.md) |
| Dédié à PowerBI | Schéma non disponible. |
| Recovery Services | [Modèle de données pour la sauvegarde Azure](../backup/backup-azure-reports-data-model.md)|
| Recherche |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../search/search-traffic-analytics.md) |
| Service Bus |[Journaux de diagnostic Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Base de données SQL | [Journalisation de diagnostic de base de données SQL Azure](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Journaux de diagnostic des travaux](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | Schéma non disponible. |
| Virtual Network | Schéma non disponible. |
| Passerelles de réseau virtuel | Schéma non disponible. |

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource
|Type de ressource|Catégorie|Nom d’affichage de la catégorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Engine (Moteur)|Engine (Moteur)|
|Microsoft.AnalysisServices/servers|de diffusion en continu|de diffusion en continu|
|Microsoft.ApiManagement/service|GatewayLogs|Journaux relatifs à la passerelle ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux de travail|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Automation/automationAccounts|DscNodeStatus|État du nœud DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux de service|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtient les métriques du point de terminaison, par exemple, la bande passante, la sortie, etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Journal d’exécutions d’activités de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Journal d’exécutions de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Journal d’exécutions de déclencheur|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Requêtes|Journaux de requête|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Requests|Journaux de requête|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Journaux de serveur PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|Événements de sauvegarde PostgreSQL|
|Microsoft.Devices/IotHubs|connexions|connexions|
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
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostics E2E (version préliminaire)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Opérations sur les appareils|
|Microsoft.Devices/provisioningServices|ServiceOperations|Opérations de service|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Journaux de mise à l’échelle automatique|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic de runtime de workflow|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Suivi des événements de compte d’intégration|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événement de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte d’équilibreur de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État d’intégrité de la sonde d’équilibreur de charge|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notifications de protection DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertes de protection de machine virtuelle|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal d’accès à la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de performance de la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal de pare-feu de la passerelle d’application|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Journaux de diagnostic de passerelle|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Journaux de diagnostic de tunnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Journaux de diagnostic d’itinéraires|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Journaux de diagnostics IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Journaux de diagnostics PS2|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Événement de résultats d’intégrité de sondage Traffic Manager|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Table des compteurs GWM|
|Microsoft.PowerBIDedicated/capacities|Engine (Moteur)|Engine (Moteur)|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Données de rapport de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Travaux Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Événements Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Éléments répliqués d’Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiques de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Points de récupération Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taux de chargement de données de réplication Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Activité des données de disque protégé Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Journaux des opérations|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiques d’exécution du magasin de données des requêtes|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiques d’attente du magasin de requêtes|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiques d’attente de base de données|
|Microsoft.Sql/servers/databases|Délais d’expiration|Délais d’expiration|
|Microsoft.Sql/servers/databases|Blocs|Blocs|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Audit|Journaux d’audit|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Événement d’audit de sécurité SQL|
|Microsoft.StreamAnalytics/streamingjobs|Exécution|Exécution|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md)
* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)

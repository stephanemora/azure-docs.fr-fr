---
title: Prise en charge des étiquettes Azure Resource Manager pour les ressources
description: Indique les types de ressources Azure qui prennent en charge les étiquettes. Fournit des détails pour tous les services Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000289"
---
# <a name="tag-support-for-azure-resources"></a>Prise en charge des étiquettes pour les ressources Azure
Cet article indique si un type de ressource prend en charge [l’étiquetage](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>AAD Domain Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| domaines | Non  | 

## <a name="ad-hybrid-health-service"></a>Service de contrôle d’intégrité hybride Active Directory
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| addsservices | Non  |
| aadsupportcases | Non  | 
| agents | Non  | 
| anonymousapiusers | Non  | 
| configuration | Non  | 
| logs | Non  | 
| reports | Non  | 
| services | Non  | 
| servicehealthmetrics | Non  | 

## <a name="aks"></a>AKS
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| managedclusters | Oui | 

## <a name="analysis-services"></a>Analysis Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 

## <a name="api-hubs"></a>Hubs d’API
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| apiManagementAccounts | Non  | 
| apiManagementAccounts/apis | Non  | 
| apiManagementAccounts/connectionAcls | Non  | 
| apiManagementAccounts/connectionProviders | Non  | 
| apiManagementAccounts/connectionProviderAcls | Non  | 
| apiManagementAccounts/connections | Non  | 

## <a name="api-management"></a>Gestion des API
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| service | Oui | 

## <a name="automation"></a>Automatisation
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| automationAccounts | Oui | 
| automationAccounts/configurations | Oui | 
| automationAccounts/jobs | Non  | 
| automationAccounts/runbooks | Oui | 
| automationAccounts/softwareUpdateConfigurations | Non  | 
| automationAccounts/webhooks | Non  | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| serveurs/configurations | Non  |
| servers/databases | Non  |
| serveurs/firewallRules | Non  |
| servers/recoverableServers | Non  | 
| servers/securityAlertPolicies | Non  |
| servers/virtualNetworkRules | Non  | 

## <a name="azure-database-for-mysql"></a>Azure Database pour MySQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| serveurs/configurations | Non  |
| servers/databases | Non  |
| serveurs/firewallRules | Non  |
| servers/recoverableServers | Non  | 
| servers/securityAlertPolicies | Non  |
| servers/virtualNetworkRules | Non  | 

## <a name="azure-database-for-postgresql"></a>Azure Database pour PostgreSQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| servers/advisors | Non  | 
| servers/configurations | Non  |
| servers/databases | Non  |
| servers/firewallRules | Non  |
| servers/queryTexts | Non  | 
| servers/recoverableServers | Non  | 
| servers/securityAlertPolicies | Non  |
| servers/topQueryStatistics | Non  | 
| servers/virtualNetworkRules | Non  | 
| servers/waitStatistics | Non  | 

## <a name="batch"></a>Batch
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| batchAccounts | Oui | 

## <a name="bing-maps"></a>Bing Maps
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| mapApis | Oui | 

## <a name="biztalk-services"></a>BizTalk Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| BizTalk | Oui | 

## <a name="cache"></a>Cache
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| Redis | Oui | 

## <a name="cdn"></a>CDN
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| edgenodes | Non  | 
| profils | Oui | 
| profiles/endpoints | Oui | 
| profiles/endpoints/customdomains | Non  | 
| profiles/endpoints/origins | Non  | 
| validateProbe | Non  | 

## <a name="classic-compute"></a>Calcul classique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| domainNames | Non  | 
| domainNames/slots | Non  | 
| domainNames/slots/roles | Non  | 
| virtualMachines | Non  | 
| virtualMachines/diagnosticSettings | Non  | 
| virtualMachines/metricDefinitions | Non  | 
| virtualMachines/metrics | Non  | 

## <a name="classic-infrastructure-migrate"></a>Migration d’une infrastructure classique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| classicInfrastructureResources | Non  | 

## <a name="classic-network"></a>Réseau classique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| virtualNetworks | Non  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Non  | 
| virtualNetworks/virtualNetworkPeerings | Non  | 

## <a name="classic-storage"></a>Stockage classique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageAccounts/services | Non  | 
| storageAccounts/services/diagnosticSettings | Non  | 

## <a name="compute"></a>Calcul
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| availabilitySets | Oui | 
| disks | Oui | 
| images | Oui | 
| restorePointCollections | Oui | 
| restorePointCollections/restorePoints | Non  | 
| sharedVMImages | Oui | 
| sharedVMImages/versions | Oui | 
| snapshots | Oui | 
| virtualMachines | Oui | 
| virtualMachines/diagnosticSettings | Non  | 
| virtualMachines/extensions | Oui | 
| virtualMachines/metricDefinitions | Non  | 
| virtualMachineScaleSets | Oui | 
| virtualMachineScaleSets/extensions | Non  | 
| virtualMachineScaleSets/networkInterfaces | Non  | 
| virtualMachineScaleSets/publicIPAddresses | Non  | 
| virtualMachineScaleSets/virtualMachines | Non  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Non  | 

## <a name="container"></a>Conteneur
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| containerGroups | Oui | 

## <a name="container-instance"></a>Instance de conteneur
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| containerGroups | Oui | 
| serviceAssociationLinks | Non  | 

## <a name="container-registry"></a>Container Registry
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| registries | Oui | 
| registries/replications | Oui |
| registries/tasks | Oui |
| registries/webhooks | Oui |

## <a name="container-service"></a>Service de conteneur
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| containerServices | Oui | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="cosmos-db"></a>Cosmos DB
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| databaseAccounts | Oui | 
| databaseAccountNames | Non  | 

## <a name="cost-management"></a>Cost Management
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| Connecteurs | Oui | 

## <a name="data-box"></a>Data Box
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| jobs | Oui | 

## <a name="data-box-edge"></a>Data Box Edge
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| DataBoxEdgeDevices | Oui | 

## <a name="data-catalog"></a>Data Catalog
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| catalogs | Oui | 

## <a name="data-connect"></a>Connexion de données
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| connectionManagers | Oui | 

## <a name="data-factory"></a>Data Factory
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| dataFactories | Oui | 
| dataFactories/diagnosticSettings | Non  | 
| dataFactories/metricDefinitions | Non  | 
| dataFactorySchema | Non  | 
| factories | Oui | 
| factories/integrationRuntimes | Non  | 

## <a name="devices"></a>Appareils
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| IoTHubs | Oui | 
| IotHubs/eventGridFilters | Non  | 
| ProvisioningServices | Oui | 

## <a name="devspaces"></a>Devspaces
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| controllers | Oui | 

## <a name="devtest-lab"></a>DevTest Lab
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| labs | Oui | 
| labs/artifactsources | Oui |
| labs/costs | Oui |
| labs/customimages | Oui |
| labs/formulas | Oui |
| labs/notificationchannels | Oui |
| labs/policysets/policies | Oui |
| labs/schedules | Oui |
| labs/serviceRunners | Oui | 
| labs/users | Oui |
| labs/users/disks | Oui |
| labs/users/environments | Oui |
| labs/users/secrets | Oui |
| labs/users/servicefabrics | Oui |
| labs/users/servicefabrics/schedules | Oui |
| labs/virtualMachines | Oui | 
| labs/virtualmachines/schedules | Oui |
| labs/virtualnetworks | Oui |
| schedules | Oui | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| lcsprojects | Non  | 
| lcsprojects/connectors | Non  | 
| lcsprojects/clouddeployments | Non  | 

## <a name="event-grid"></a>Event Grid
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| domaines | Oui | 
| domains/topics | Non  | 
| eventSubscriptions | Non  | 
| extensionTopics | Non  | 
| topics | Oui | 
| topicTypes | Non  | 

## <a name="event-hub"></a>Event Hub
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| clusters | Oui | 
| namespaces | Oui | 
| namespaces/AuthorizationRules | Non  |
| namespaces/disasterRecoveryConfigs | Non  |
| namespaces/eventhubs | Non  |
| namespaces/eventhubs/authorizationRules | Non  |
| namespaces/eventhubs/consumergroups | Non  |

## <a name="hana-on-azure"></a>Hana sur Azure
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| hanaInstances | Oui | 

## <a name="hdinsight"></a>HDInsight
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| clusters | Oui | 
| clusters/applications | Non  | 

## <a name="import-export"></a>Importation - Exportation
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| jobs | Oui | 

## <a name="insights"></a>Insights
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| actionGroups | Oui |
| activityLogAlerts | Oui |
| alertrules | Oui |
| automatedExportSettings | Non  | 
| components | Oui | 
| components/events | Non  | 
| components/metrics | Non  | 
| components/pricingPlans | Non  | 
| components/query | Non  | 
| logs | Non  | 
| metricAlerts | Oui |
| migrateToNewPricingModel | Non  | 
| myWorkbooks | Non  | 
| queries | Non  | 
| rollbackToLegacyPricingModel | Non  | 
| scheduledqueryrules | Oui | 
| webtests | Oui | 
| workbooks | Oui | 

## <a name="key-vault"></a>Key Vault
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| deletedVaults | Non  | 
| vaults | Oui | 
| vaults/accessPolicies | Non  | 
| vaults/secrets | Non  | 

## <a name="log-analytics"></a>Log Analytics
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| logs | Non  | 

## <a name="logic"></a>Logique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| integrationAccounts | Oui | 
| workflows | Oui | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| workspaces | Oui | 
| workspaces/computes | Non  | 

## <a name="managed-identity"></a>Identité managée
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| Identities | Non  | 
| userAssignedIdentities | Oui | 

## <a name="marketplace-apps"></a>Applications de la Place de marché
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| classicDevServices | Oui | 

## <a name="marketplace-ordering"></a>Classement de la Place de marché
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| agreements | Non  | 
| offertypes | Non  | 

## <a name="media"></a>Médias
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| mediaservices | Oui | 
| mediaservices/accountFilters | Non  | 
| mediaservices/assets | Non  | 
| mediaservices/assets/assetFilters | Non  | 
| mediaservices/contentKeyPolicies | Non  | 
| mediaservices/eventGridFilters | Non  | 
| mediaservices/liveEventOperations | Non  | 
| mediaservices/liveEvents | Oui | 
| mediaservices/liveEvents/liveOutputs | Non  | 
| mediaservices/liveOutputOperations | Non  | 
| mediaservices/streamingEndpoints | Oui | 
| mediaservices/streamingEndpointOperations | Non  | 
| mediaservices/streamingLocators | Non  | 
| mediaservices/streamingPolicies | Non  | 
| mediaservices/transforms | Non  | 
| mediaservices/transforms/jobs | Non  | 

## <a name="network"></a>Réseau
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| applicationGateways | Oui | 
| applicationSecurityGroups | Oui | 
| azureFirewalls | Oui | 
| connections | Oui | 
| ddosProtectionPlans | Oui | 
| expressRouteCircuits | Oui | 
| frontdoors | Oui | 
| frontdoorWebApplicationFirewallPolicies | Oui | 
| interfaceEndpoints | Oui | 
| loadBalancers | Oui | 
| localNetworkGateways | Oui | 
| networkIntentPolicies | Oui | 
| networkInterfaces | Oui | 
| networkProfiles | Oui | 
| networkSecurityGroups | Oui | 
| networkWatchers | Oui | 
| networkWatchers/connectionMonitors | Oui | 
| networkWatchers/lenses | Oui | 
| networkWatchers/pingMeshes | Oui | 
| privateLinkServices | Oui | 
| publicIPAddresses | Oui | 
| publicIPPrefixes | Oui | 
| routeFilters | Oui | 
| routeTables | Oui | 
| serviceEndpointPolicies | Oui | 
| virtualHubs | Oui | 
| virtualNetworks | Oui | 
| virtualNetworkGateways | Oui | 
| virtualNetworkTaps | Oui | 
| virtualWans | Oui | 
| vpnGateways | Oui | 
| vpnSites | Oui | 
| webApplicationFirewallPolicies | Oui | 

## <a name="notification-hubs"></a>Notification Hubs
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/notificationHubs | Oui | 

## <a name="operational-insights"></a>Operational Insights
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| workspaces | Oui |
| workspaces/dataSources | Oui |
| workspaces/linkedServices | Oui |
| workspaces/savedSearches | Non  |
| workspaces/storageInsightConfigs | Oui |

## <a name="operations-management"></a>Operations Management
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| solutions | Non  |

## <a name="portal"></a>Portail
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| dashboards | Oui | 

## <a name="portal-sdk"></a>SDK du portail
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| rootResources | Oui | 

## <a name="power-bi"></a>Power BI
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| workspaceCollections | Oui | 

## <a name="recovery-services"></a>Recovery Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| backupProtectedItems | Non  | 
| vaults | Oui | 

## <a name="relay"></a>Relais
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| namespaces | Oui | 

## <a name="resources"></a>Ressources
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| resourceGroups | Oui | 
| subscriptions/resourceGroups | Oui | 

## <a name="scheduler"></a>Scheduler
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| jobcollections | Oui | 
| flows | Oui | 

## <a name="search"></a>Recherche
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| resourceHealthMetadata | Non  | 
| searchServices | Oui | 

## <a name="security"></a>Sécurité
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| dataCollectionAgents | Non  | 

## <a name="service-bus"></a>Service Bus
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/eventgridfilters | Non  | 

## <a name="service-fabric"></a>Service Fabric
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| clusters | Oui | 
| clusters/applications | Non  | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| web | Oui | 
| networks | Oui | 
| volumes | Oui | 

## <a name="signalr-service"></a>Service SignalR
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| SignalR | Oui | 

## <a name="site-recovery"></a>Site Recovery
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| SiteRecoveryVault | Oui | 

## <a name="solutions"></a>solutions
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| web | Oui | 
| applicationDefinitions | Oui | 
| jitRequests | Oui | 

## <a name="sql"></a>SQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| locations/instanceFailoverGroups | Non  |
| managedInstances | Oui |
| managedInstances/databases | Oui |
| managedInstances/databases/backupShortTermRetentionPolicies | Non  |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Non  |
| managedInstances/databases/vulnerabilityAssessments | Non  |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Non  |
| managedInstances/encryptionProtector | Non  |
| managedInstances/keys | Non  |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Non  |
| managedInstances/vulnerabilityAssessments | Non  |
| servers | Oui |
| servers/administrators | Non  |
| servers/advisors | Non  |
| servers/auditingSettings | Non  |
| servers/backupLongTermRetentionVaults | Non  |
| servers/communicationLinks | Non  |
| servers/connectionPolicies | Non  |
| servers/databases | Oui |
| servers/databases/advisors | Non  |
| servers/databases/auditingSettings | Non  |
| servers/databases/backupLongTermRetentionPolicies | Non  |
| servers/databases/backupShortTermRetentionPolicies | Non  |
| servers/databases/connectionPolicies | Non  |
| servers/databases/dataMaskingPolicies | Non  |
| servers/databases/dataMaskingPolicies/rules | Non  |
| servers/databases/extendedAuditingSettings | Non  |
| servers/databases/extensions | Non  |
| servers/databases/geoBackupPolicies | Non  |
| servers/databases/schemas/tables/columns/sensitivityLabels | Non  |
| servers/databases/securityAlertPolicies | Non  |
| servers/databases/syncGroups | Non  |
| servers/databases/syncGroups/syncMembers | Non  |
| servers/databases/transparentDataEncryption | Non  |
| servers/databases/vulnerabilityAssessments | Non  |
| servers/databases/vulnerabilityAssessments/rules/baselines | Non  |
| servers/disasterRecoveryConfiguration | Non  |
| servers/dnsAliases | Non  |
| servers/elasticPools | Oui |
| servers/encryptionProtector | Non  |
| servers/extendedAuditingSettings | Non  |
| servers/failoverGroups | Oui |
| servers/firewallRules | Non  |
| servers/jobAgents | Oui |
| servers/jobAgents/credentials | Non  |
| servers/jobAgents/jobs | Non  |
| servers/jobAgents/jobs/executions | Non  |
| servers/jobAgents/jobs/steps | Non  |
| servers/jobAgents/targetGroups | Non  |
| servers/keys | Non  |
| servers/securityAlertPolicies | Non  |
| servers/syncAgents | Non  |
| servers/virtualNetworkRules | Non  |
| servers/vulnerabilityAssessments | Non  |

## <a name="sql-virtual-machine"></a>Machine virtuelle SQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| DWVM | Oui | 

## <a name="storage"></a>Stockage
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageAccounts | Oui | 
| storageAccounts/blobServices | Non  | 
| storageAccounts/fileServices | Non  | 
| storageAccounts/queueServices | Non  | 
| storageAccounts/services | Non  | 
| storageAccounts/services/metricDefinitions | Non  | 
| storageAccounts/tableServices | Non  | 

## <a name="storage-sync"></a>Synchronisation de stockage
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageSyncServices | Oui | 
| storageSyncServices/registeredServers | Non  | 
| storageSyncServices/syncGroups | Non  | 
| storageSyncServices/syncGroups/cloudEndpoints | Non  | 
| storageSyncServices/syncGroups/serverEndpoints | Non  | 
| storageSyncServices/workflows | Non  | 

## <a name="storsimple"></a>Storsimple
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| managers | Oui | 

## <a name="stream-analytics"></a>Stream Analytics
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| streamingjobs | Oui | 
| streamingjobs/diagnosticSettings | Non  | 
| streamingjobs/metricDefinitions | Non  | 

## <a name="subscription"></a>Abonnement
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| SubscriptionDefinitions | Non  | 
| SubscriptionOperations | Non  | 

## <a name="support"></a>Support
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| supporttickets | Non  | 

## <a name="visual-studio"></a>Visual Studio
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| compte | Oui | 
| account/extension | Oui | 
| account/project | Oui | 

## <a name="web"></a>Web
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| apiManagementAccounts | Non  | 
| apiManagementAccounts/apiAcls | Non  | 
| apiManagementAccounts/apis | Non  | 
| apiManagementAccounts/apis/apiAcls | Non  | 
| apiManagementAccounts/apis/connectionAcls | Non  | 
| apiManagementAccounts/apis/connections | Non  | 
| apiManagementAccounts/apis/connections/connectionAcls | Non  | 
| apiManagementAccounts/apis/localizedDefinitions | Non  | 
| apiManagementAccounts/connectionAcls | Non  | 
| apiManagementAccounts/connections | Non  | 
| billingMeters | Non  | 
| certificates | Oui | 
| connectionGateways | Oui | 
| connections | Oui | 
| customApis | Oui | 
| deletedSites | Non  | 
| functions | Non  | 
| hostingEnvironments | Oui | 
| hostingEnvironments/metrics | Non  | 
| hostingEnvironments/multiRolePools | Non  | 
| hostingEnvironments/workerPools | Non  | 
| publishingUsers | Non  | 
| serverFarms | Oui | 
| serverFarms/workers | Non  | 
| sites | Oui | 
| sites/domainOwnershipIdentifiers | Non  | 
| sites/hostNameBindings | Non  | 
| sites/instances | Non  | 
| sites/instances/extensions | Non  | 
| sites/metrics | Non  | 
| sites/premieraddons | Oui | 
| sites/slots | Oui | 
| sites/slots/hostNameBindings | Non  | 
| sites/slots/instances | Non  | 
| sites/slots/instances/extensions | Non  | 
| sites/slots/metrics | Non  | 
| sourceControls | Non  | 
| validate | Non  | 
| verifyHostingEnvironmentVnet | Non  | 

## <a name="xrm"></a>XRM
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| organizations | Non  | 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment appliquer des étiquettes aux ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](resource-group-using-tags.md).

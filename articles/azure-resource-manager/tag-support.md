---
title: Prise en charge des étiquettes Azure Resource Manager pour les ressources
description: Indique les types de ressources Azure qui prennent en charge les étiquettes. Fournit des détails pour tous les services Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: a4bb423dc5eddde0fd2d2b9b4f263ab39dbd801f
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284980"
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
| services | Non  | 
| addsservices | Non  | 
| configuration | Non  | 
| agents | Non  | 
| aadsupportcases | Non  | 
| reports | Non  | 
| servicehealthmetrics | Non  | 
| logs | Non  | 
| anonymousapiusers | Non  | 

## <a name="analysis-services"></a>Analysis Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 

## <a name="api-hubs"></a>Hubs d’API
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| apiManagementAccounts | Non  | 
| apiManagementAccounts/connectionProviders | Non  | 
| apiManagementAccounts/connections | Non  | 
| apiManagementAccounts/connectionAcls | Non  | 
| apiManagementAccounts/connectionProviderAcls | Non  | 
| apiManagementAccounts/apis | Non  | 

## <a name="api-management"></a>API Management
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| service | Oui | 

## <a name="automation"></a>Automatisation
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| automationAccounts | Oui | 
| automationAccounts/runbooks | Oui | 
| automationAccounts/configurations | Oui | 
| automationAccounts/webhooks | Non  | 
| automationAccounts/softwareUpdateConfigurations | Non  | 
| automationAccounts/jobs | Non  | 

## <a name="batch"></a>Batch
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| batchAccounts | Oui | 

## <a name="batch-ai"></a>Batch AI
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| clusters | Oui | 
| jobs | Oui | 
| fileservers | Oui | 
| workspaces | Oui | 
| workspaces/clusters | Non  | 
| workspaces/fileservers | Non  | 
| workspaces/experiments | Non  | 
| workspaces/experiments/jobs | Non  | 

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
| profils | Oui | 
| profiles/endpoints | Oui | 
| profiles/endpoints/origins | Non  | 
| profiles/endpoints/customdomains | Non  | 
| validateProbe | Non  | 
| edgenodes | Non  | 

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
| virtualNetworks/virtualNetworkPeerings | Non  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Non  | 

## <a name="classic-storage"></a>Stockage classique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageAccounts/services | Non  | 
| storageAccounts/services/diagnosticSettings | Non  | 

## <a name="compute"></a>Calcul
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| availabilitySets | Oui | 
| virtualMachines | Oui | 
| virtualMachines/extensions | Oui | 
| virtualMachineScaleSets | Oui | 
| virtualMachineScaleSets/extensions | Non  | 
| virtualMachineScaleSets/virtualMachines | Non  | 
| virtualMachineScaleSets/networkInterfaces | Non  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Non  | 
| virtualMachineScaleSets/publicIPAddresses | Non  | 
| restorePointCollections | Oui | 
| restorePointCollections/restorePoints | Non  | 
| virtualMachines/diagnosticSettings | Non  | 
| virtualMachines/metricDefinitions | Non  | 
| sharedVMImages | Oui | 
| sharedVMImages/versions | Oui | 
| disks | Oui | 
| snapshots | Oui | 
| images | Oui | 

## <a name="container"></a>Conteneur
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| containerGroups | Oui | 

## <a name="container-instance"></a>Instance de conteneur
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| containerGroups | Oui | 
| serviceAssociationLinks | Non  | 

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
| factories | Oui | 
| factories/integrationRuntimes | Non  | 
| dataFactories/diagnosticSettings | Non  | 
| dataFactories/metricDefinitions | Non  | 
| dataFactorySchema | Non  | 

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
| schedules | Oui | 
| labs/virtualMachines | Oui | 
| labs/serviceRunners | Oui | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| lcsprojects | Non  | 
| lcsprojects/connectors | Non  | 
| lcsprojects/clouddeployments | Non  | 

## <a name="event-grid"></a>Event Grid
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| eventSubscriptions | Non  | 
| topics | Oui | 
| domaines | Oui | 
| domains/topics | Non  | 
| topicTypes | Non  | 
| extensionTopics | Non  | 

## <a name="event-hub"></a>Event Hub
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| namespaces | Oui | 
| clusters | Oui | 

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
| components | Oui | 
| components/query | Non  | 
| components/metrics | Non  | 
| components/events | Non  | 
| webtests | Oui | 
| queries | Non  | 
| scheduledqueryrules | Oui | 
| components/pricingPlans | Non  | 
| migrateToNewPricingModel | Non  | 
| rollbackToLegacyPricingModel | Non  | 
| automatedExportSettings | Non  | 
| workbooks | Oui | 
| myWorkbooks | Non  | 
| logs | Non  | 

## <a name="key-vault"></a>Key Vault
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| vaults | Oui | 
| vaults/secrets | Non  | 
| vaults/accessPolicies | Non  | 
| deletedVaults | Non  | 

## <a name="log-analytics"></a>Log Analytics
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| logs | Non  | 

## <a name="logic"></a>Logique
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| workflows | Oui | 
| integrationAccounts | Oui | 

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

## <a name="mariadb"></a>MariaDB
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| servers/recoverableServers | Non  | 
| servers/virtualNetworkRules | Non  | 

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
| mediaservices/assets | Non  | 
| mediaservices/contentKeyPolicies | Non  | 
| mediaservices/streamingLocators | Non  | 
| mediaservices/streamingPolicies | Non  | 
| mediaservices/eventGridFilters | Non  | 
| mediaservices/transforms | Non  | 
| mediaservices/transforms/jobs | Non  | 
| mediaservices/streamingEndpoints | Oui | 
| mediaservices/liveEvents | Oui | 
| mediaservices/liveEvents/liveOutputs | Non  | 
| mediaservices/streamingEndpointOperations | Non  | 
| mediaservices/liveEventOperations | Non  | 
| mediaservices/liveOutputOperations | Non  | 
| mediaservices/assets/assetFilters | Non  | 
| mediaservices/accountFilters | Non  | 

## <a name="mysql"></a>MySQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| servers/recoverableServers | Non  | 
| servers/virtualNetworkRules | Non  | 

## <a name="network"></a>Réseau
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| virtualNetworks | Oui | 
| publicIPAddresses | Oui | 
| networkInterfaces | Oui | 
| interfaceEndpoints | Oui | 
| loadBalancers | Oui | 
| networkSecurityGroups | Oui | 
| applicationSecurityGroups | Oui | 
| serviceEndpointPolicies | Oui | 
| networkIntentPolicies | Oui | 
| routeTables | Oui | 
| publicIPPrefixes | Oui | 
| networkWatchers | Oui | 
| networkWatchers/connectionMonitors | Oui | 
| networkWatchers/lenses | Oui | 
| networkWatchers/pingMeshes | Oui | 
| virtualNetworkGateways | Oui | 
| localNetworkGateways | Oui | 
| connections | Oui | 
| applicationGateways | Oui | 
| expressRouteCircuits | Oui | 
| routeFilters | Oui | 
| virtualWans | Oui | 
| vpnSites | Oui | 
| virtualHubs | Oui | 
| vpnGateways | Oui | 
| azureFirewalls | Oui | 
| virtualNetworkTaps | Oui | 
| privateLinkServices | Oui | 
| ddosProtectionPlans | Oui | 
| networkProfiles | Oui | 
| frontdoors | Oui | 
| frontdoorWebApplicationFirewallPolicies | Oui | 
| webApplicationFirewallPolicies | Oui | 

## <a name="notification-hubs"></a>Notification Hubs
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/notificationHubs | Oui | 

## <a name="portal"></a>Portail
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| dashboards | Oui | 

## <a name="portal-sdk"></a>SDK du portail
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| rootResources | Oui | 

## <a name="postgresql"></a>PostgreSQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| servers | Oui | 
| servers/recoverableServers | Non  | 
| servers/virtualNetworkRules | Non  | 
| servers/topQueryStatistics | Non  | 
| servers/queryTexts | Non  | 
| servers/waitStatistics | Non  | 
| servers/advisors | Non  | 

## <a name="power-bi"></a>Power BI
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| workspaceCollections | Oui | 

## <a name="recovery-services"></a>Recovery Services
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| vaults | Oui | 
| backupProtectedItems | Non  | 

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
| searchServices | Oui | 
| resourceHealthMetadata | Non  | 

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

## <a name="sql-virtual-machine"></a>Machine virtuelle SQL
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| DWVM | Oui | 

## <a name="storage"></a>Stockage
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageAccounts | Oui | 
| storageAccounts/blobServices | Non  | 
| storageAccounts/tableServices | Non  | 
| storageAccounts/queueServices | Non  | 
| storageAccounts/fileServices | Non  | 
| storageAccounts/services | Non  | 
| storageAccounts/services/metricDefinitions | Non  | 

## <a name="storage-sync"></a>Synchronisation de stockage
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| storageSyncServices | Oui | 
| storageSyncServices/syncGroups | Non  | 
| storageSyncServices/syncGroups/cloudEndpoints | Non  | 
| storageSyncServices/syncGroups/serverEndpoints | Non  | 
| storageSyncServices/registeredServers | Non  | 
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
| account/project | Oui | 
| account/extension | Oui | 
| compte | Oui | 
| account/project | Oui | 
| account/extension | Oui | 

## <a name="web"></a>Web
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| sites/instances | Non  | 
| sites/slots/instances | Non  | 
| sites/instances/extensions | Non  | 
| sites/slots/instances/extensions | Non  | 
| publishingUsers | Non  | 
| validate | Non  | 
| sourceControls | Non  | 
| sites/hostNameBindings | Non  | 
| sites/domainOwnershipIdentifiers | Non  | 
| sites/slots/hostNameBindings | Non  | 
| certificates | Oui | 
| serverFarms | Oui | 
| serverFarms/workers | Non  | 
| sites | Oui | 
| sites/slots | Oui | 
| sites/metrics | Non  | 
| sites/slots/metrics | Non  | 
| sites/premieraddons | Oui | 
| hostingEnvironments | Oui | 
| hostingEnvironments/multiRolePools | Non  | 
| hostingEnvironments/workerPools | Non  | 
| hostingEnvironments/metrics | Non  | 
| functions | Non  | 
| deletedSites | Non  | 
| apiManagementAccounts | Non  | 
| apiManagementAccounts/connections | Non  | 
| apiManagementAccounts/connectionAcls | Non  | 
| apiManagementAccounts/apis/connections/connectionAcls | Non  | 
| apiManagementAccounts/apis/connectionAcls | Non  | 
| apiManagementAccounts/apiAcls | Non  | 
| apiManagementAccounts/apis/apiAcls | Non  | 
| apiManagementAccounts/apis | Non  | 
| apiManagementAccounts/apis/localizedDefinitions | Non  | 
| apiManagementAccounts/apis/connections | Non  | 
| connections | Oui | 
| customApis | Oui | 
| connectionGateways | Oui | 
| billingMeters | Non  | 
| verifyHostingEnvironmentVnet | Non  | 

## <a name="xrm"></a>XRM
| Type de ressource | Prend en charge les étiquettes |
| ------------- | ----------- |
| organizations | Non  | 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment appliquer des étiquettes aux ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](resource-group-using-tags.md).

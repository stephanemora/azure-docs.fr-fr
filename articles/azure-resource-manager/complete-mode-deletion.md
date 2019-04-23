---
title: Suppression du mode complet Azure Resource Manager par type de ressource
description: Montre comment les types de ressources gèrent la suppression du mode complet dans les modèles Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: fded37fee844a01f4d51518f2ca56dcf575704b2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150147"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Suppression de ressources Azure pour les déploiements en mode complet
Cet article décrit la manière dont les types de ressources gèrent la suppression en dehors d'un modèle déployé en mode complet.

Les types de ressources indiqués par `Yes` sont supprimés lorsque le type n’est pas présent dans le modèle déployé en mode complet. 

Les types de ressources indiqués par `No` ne sont pas automatiquement supprimés lorsqu'ils ne sont pas présents dans le modèle, mais sont supprimés si la ressource parente est supprimée. Pour une description complète du comportement, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| DomainServices | Oui | 
| DomainServices/oucontainer | Non  | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| diagnosticSettings | Non  | 
| diagnosticSettingsCategories | Non  | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| supportProviders | Non  | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| aadsupportcases | Non  | 
| addsservices | Non  | 
| agents | Non  | 
| anonymousapiusers | Non  | 
| configuration | Non  | 
| journaux d’activité | Non  | 
| reports | Non  | 
| services | Non  | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| configurations | Non  | 
| generateRecommendations | Non  | 
| de films | Non  | 
| suppressions | Non  | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| actionRules | Non  | 
| alertes | Non  | 
| alertsList | Non  | 
| alertsSummary | Non  | 
| alertsSummaryList | Non  | 
| smartDetectorAlertRules | Non  | 
| smartDetectorRuntimeEnvironments | Non  | 
| smartGroups | Non  | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| servers | Oui | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| reportFeedback | Non  | 
| service | Oui | 
| validateServiceName | Non  | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| attestationProviders | Non  | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| classicAdministrators | Non  | 
| denyAssignments | Non  | 
| elevateAccess | Non  | 
| locks | Non  | 
| Autorisations | Non  | 
| policyAssignments | Non  | 
| policyDefinitions | Non  | 
| policySetDefinitions | Non  | 
| providerOperations | Non  | 
| roleAssignments | Non  | 
| roleDefinitions | Non  | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| automationAccounts | Oui | 
| automationAccounts/configurations | Oui | 
| automationAccounts/jobs | Non  | 
| automationAccounts/runbooks | Oui | 
| automationAccounts/softwareUpdateConfigurations | Non  | 
| automationAccounts/webhooks | Non  | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| environments | Non  | 
| environments/accounts | Non  | 
| environments/accounts/namespaces | Non  | 
| environments/accounts/namespaces/configurations | Non  | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| b2cDirectories | Oui | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| registrations | Oui | 
| registrations/customerSubscriptions | Non  | 
| registrations/products | Non  | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| batchAccounts | Oui | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| billingAccounts | Non  | 
| billingAccounts/billingProfiles | Non  | 
| billingAccounts/billingProfiles/billingSubscriptions | Non  | 
| billingAccounts/billingProfiles/invoices | Non  | 
| billingAccounts/billingProfiles/invoices/pricesheet | Non  | 
| billingAccounts/billingProfiles/operationStatus | Non  | 
| billingAccounts/billingProfiles/paymentMethods | Non  | 
| billingAccounts/billingProfiles/policies | Non  | 
| billingAccounts/billingProfiles/pricesheet | Non  | 
| billingAccounts/billingProfiles/products | Non  | 
| billingAccounts/billingProfiles/transactions | Non  | 
| billingAccounts/billingSubscriptions | Non  | 
| billingAccounts/departments | Non  | 
| billingAccounts/eligibleOffers | Non  | 
| billingAccounts/enrollmentAccounts | Non  | 
| billingAccounts/invoices | Non  | 
| billingAccounts/invoiceSections | Non  | 
| billingAccounts/invoiceSections/billingSubscriptions | Non  | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Non  | 
| billingAccounts/invoiceSections/importRequests | Non  | 
| billingAccounts/invoiceSections/initiateImportRequest | Non  | 
| billingAccounts/invoiceSections/initiateTransfer | Non  | 
| billingAccounts/invoiceSections/operationStatus | Non  | 
| billingAccounts/invoiceSections/products | Non  | 
| billingAccounts/invoiceSections/transfers | Non  | 
| billingAccounts/products | Non  | 
| billingAccounts/projects | Non  | 
| billingAccounts/projects/billingSubscriptions | Non  | 
| billingAccounts/projects/importRequests | Non  | 
| billingAccounts/projects/initiateImportRequest | Non  | 
| billingAccounts/projects/operationStatus | Non  | 
| billingAccounts/projects/products | Non  | 
| billingAccounts/transactions | Non  | 
| billingPeriods | Non  | 
| BillingPermissions | Non  | 
| billingProperty | Non  | 
| BillingRoleAssignments | Non  | 
| BillingRoleDefinitions | Non  | 
| CreateBillingRoleAssignment | Non  | 
| departments | Non  | 
| enrollmentAccounts | Non  | 
| importRequests | Non  | 
| importRequests/acceptImportRequest | Non  | 
| importRequests/declineImportRequest | Non  | 
| invoices | Non  | 
| transfers | Non  | 
| transfers/acceptTransfer | Non  | 
| transfers/declineTransfer | Non  | 
| transfers/operationStatus | Non  | 
| usagePlans | Non  | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| mapApis | Oui | 
| updateCommunicationPreference | Non  | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| BizTalk | Oui | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| blueprintAssignments | Non  | 
| blueprintAssignments/assignmentOperations | Non  | 
| blueprintAssignments/operations | Non  | 
| blueprints | Non  | 
| blueprints/artifacts | Non  | 
| blueprints/versions | Non  | 
| blueprints/versions/artifacts | Non  | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| botServices | Oui | 
| botServices/channels | Non  | 
| botServices/connections | Non  | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| Redis | Oui | 
| RedisConfigDefinition | Non  | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| appliedReservations | Non  | 
| calculatePrice | Non  | 
| catalogs | Non  | 
| commercialReservationOrders | Non  | 
| reservationOrders | Non  | 
| reservationOrders/calculateRefund | Non  | 
| reservationOrders/merge | Non  | 
| reservationOrders/reservations | Non  | 
| reservationOrders/reservations/revisions | Non  | 
| reservationOrders/return | Non  | 
| reservationOrders/split | Non  | 
| reservationOrders/swap | Non  | 
| reservations | Non  | 
| les ressources | Non  | 
| validateReservationOrder | Non  | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| edgenodes | Non  | 
| profiles | Oui | 
| profiles/endpoints | Oui | 
| profiles/endpoints/customdomains | Non  | 
| profiles/endpoints/origins | Non  | 
| validateProbe | Non  | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| certificateOrders | Oui | 
| certificateOrders/certificates | Non  | 
| validateCertificateRegistrationInformation | Non  | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| capabilities | Non  | 
| domainNames | Non  | 
| domainNames/capabilities | Non  | 
| domainNames/internalLoadBalancers | Non  | 
| domainNames/serviceCertificates | Non  | 
| domainNames/slots | Non  | 
| domainNames/slots/roles | Non  | 
| moveSubscriptionResources | Non  | 
| operatingSystemFamilies | Non  | 
| operatingSystems | Non  | 
| quotas | Non  | 
| resourceTypes | Non  | 
| validateSubscriptionMoveAvailability | Non  | 
| virtualMachines | Non  | 
| virtualMachines/diagnosticSettings | Non  | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| classicInfrastructureResources | Non  | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| capabilities | Non  | 
| expressRouteCrossConnections | Non  | 
| expressRouteCrossConnections/peerings | Non  | 
| gatewaySupportedDevices | Non  | 
| networkSecurityGroups | Non  | 
| quotas | Non  | 
| reservedIps | Non  | 
| virtualNetworks | Non  | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Non  | 
| virtualNetworks/virtualNetworkPeerings | Non  | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| capabilities | Non  | 
| disks | Non  | 
| images | Non  | 
| osImages | Non  | 
| osPlatformImages | Non  | 
| publicImages | Non  | 
| quotas | Non  | 
| storageAccounts | Non  | 
| storageAccounts/services | Non  | 
| storageAccounts/services/diagnosticSettings | Non  | 
| storageAccounts/vmImages | Non  | 
| vmImages | Non  | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| RateCard | Non  | 
| UsageAggregates | Non  | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Effectuer la suppression du mode |
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
| virtualMachineScaleSets | Oui | 
| virtualMachineScaleSets/extensions | Non  | 
| virtualMachineScaleSets/networkInterfaces | Non  | 
| virtualMachineScaleSets/publicIPAddresses | Non  | 
| virtualMachineScaleSets/virtualMachines | Non  | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Non  | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| AggregatedCost | Non  | 
| Balances | Non  | 
| Budgets | Non  | 
| Charges | Non  | 
| CostTags | Non  | 
| credits | Non  | 
| événements | Non  | 
| Prévisions | Non  | 
| lots | Non  | 
| Marketplaces | Non  | 
| Pricesheets | Non  | 
| products | Non  | 
| ReservationDetails | Non  | 
| ReservationRecommendations | Non  | 
| ReservationSummaries | Non  | 
| ReservationTransactions | Non  | 
| Balises | Non  | 
| Termes | Non  | 
| UsageDetails | Non  | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| containerGroups | Oui | 
| serviceAssociationLinks | Non  | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| registries | Oui | 
| registries/builds | Non  | 
| registries/builds/cancel | Non  | 
| registries/builds/getLogLink | Non  | 
| registries/buildTasks | Oui | 
| registries/buildTasks/steps | Non  | 
| registries/eventGridFilters | Non  | 
| registries/getBuildSourceUploadUrl | Non  | 
| registries/GetCredentials | Non  | 
| registries/importImage | Non  | 
| registries/queueBuild | Non  | 
| registries/regenerateCredential | Non  | 
| registries/regenerateCredentials | Non  | 
| registries/replications | Oui | 
| registries/runs | Non  | 
| registries/runs/cancel | Non  | 
| registries/scheduleRun | Non  | 
| registries/tasks | Oui | 
| registries/updatePolicies | Non  | 
| registries/webhooks | Oui | 
| registries/webhooks/getCallbackConfig | Non  | 
| registries/webhooks/ping | Non  | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| containerServices | Oui | 
| managedclusters | Oui | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| applications | Oui | 
| updateCommunicationPreference | Non  | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| Alertes | Non  | 
| BillingAccounts | Non  | 
| Connecteurs | Oui | 
| Departments | Non  | 
| Dimensions | Non  | 
| EnrollmentAccounts | Non  | 
| Interroger | Non  | 
| inscription | Non  | 
| Reportconfigs | Non  | 
| Rapports | Non  | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| hubs | Oui | 
| hubs/authorizationPolicies | Non  | 
| hubs/connectors | Non  | 
| hubs/connectors/mappings | Non  | 
| hubs/interactions | Non  | 
| hubs/kpi | Non  | 
| hubs/links | Non  | 
| hubs/profiles | Non  | 
| hubs/roleAssignments | Non  | 
| hubs/roles | Non  | 
| hubs/suggestTypeSchema | Non  | 
| hubs/views | Non  | 
| hubs/widgetTypes | Non  | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| jobs | Oui | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| DataBoxEdgeDevices | Oui | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| workspaces | Oui | 
| workspaces/virtualNetworkPeerings | Non  | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| catalogs | Oui | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| connectionManagers | Oui | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| dataFactories | Oui | 
| dataFactories/diagnosticSettings | Non  | 
| dataFactorySchema | Non  | 
| factories | Oui | 
| factories/integrationRuntimes | Non  | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 
| accounts/dataLakeStoreAccounts | Non  | 
| accounts/storageAccounts | Non  | 
| accounts/storageAccounts/containers | Non  | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 
| accounts/eventGridFilters | Non  | 
| accounts/firewallRules | Non  | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| services | Oui | 
| services/projects | Oui | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| servers | Oui | 
| servers/recoverableServers | Non  | 
| servers/virtualNetworkRules | Non  | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| servers | Oui | 
| servers/recoverableServers | Non  | 
| servers/virtualNetworkRules | Non  | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| servers | Oui | 
| servers/advisors | Non  | 
| servers/queryTexts | Non  | 
| servers/recoverableServers | Non  | 
| servers/topQueryStatistics | Non  | 
| servers/virtualNetworkRules | Non  | 
| servers/waitStatistics | Non  | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| IoTHubs | Oui | 
| IotHubs/eventGridFilters | Non  | 
| ProvisioningServices | Oui | 
| usages | Non  | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| controllers | Oui | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| labs | Oui | 
| labs/serviceRunners | Oui | 
| labs/virtualMachines | Oui | 
| schedules | Oui | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| databaseAccountNames | Non  | 
| databaseAccounts | Oui | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| domaines | Oui | 
| domains/domainOwnershipIdentifiers | Non  | 
| generateSsoRequest | Non  | 
| topLevelDomains | Non  | 
| validateDomainRegistrationInformation | Non  | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| lcsprojects | Non  | 
| lcsprojects/clouddeployments | Non  | 
| lcsprojects/connectors | Non  | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| domaines | Oui | 
| domains/topics | Non  | 
| eventSubscriptions | Non  | 
| extensionTopics | Non  | 
| topics | Oui | 
| topicTypes | Non  | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| clusters | Oui | 
| namespaces | Oui | 
| namespaces/authorizationrules | Non  | 
| namespaces/disasterrecoveryconfigs | Non  | 
| namespaces/eventhubs | Non  | 
| namespaces/eventhubs/authorizationrules | Non  | 
| namespaces/eventhubs/consumergroups | Non  | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| features | Non  | 
| fournisseurs | Non  | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| enroll | Non  | 
| galleryitems | Non  | 
| generateartifactaccessuri | Non  | 
| myareas | Non  | 
| myareas/areas | Non  | 
| myareas/areas/areas | Non  | 
| myareas/areas/areas/galleryitems | Non  | 
| myareas/areas/galleryitems | Non  | 
| myareas/galleryitems | Non  | 
| inscription | Non  | 
| les ressources | Non  | 
| retrieveresourcesbyid | Non  | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| guestConfigurationAssignments | Non  | 
| software | Non  | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| hanaInstances | Oui | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| clusters | Non  | 
| clusters/applications | Non  | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| jobs | Oui | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| labelGroups | Non  | 
| labelGroups/labels | Non  | 
| labelGroups/labels/conditions | Non  | 
| labelGroups/labels/subLabels | Non  | 
| labelGroups/labels/subLabels/conditions | Non  | 

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| actiongroups | Oui | 
| activityLogAlerts | Oui | 
| alertrules | Oui | 
| automatedExportSettings | Non  | 
| autoscalesettings | Oui | 
| baseline | Non  | 
| calculatebaseline | Non  | 
| components | Oui | 
| components/events | Non  | 
| components/pricingPlans | Non  | 
| components/query | Non  | 
| diagnosticSettings | Non  | 
| diagnosticSettingsCategories | Non  | 
| eventCategories | Non  | 
| eventtypes | Non  | 
| extendedDiagnosticSettings | Non  | 
| logDefinitions | Non  | 
| logprofiles | Non  | 
| journaux d’activité | Non  | 
| metricAlerts | Oui |
| migrateToNewPricingModel | Non  | 
| myWorkbooks | Non  | 
| queries | Non  | 
| rollbackToLegacyPricingModel | Non  | 
| scheduledqueryrules | Oui | 
| vmInsightsOnboardingStatuses | Non  | 
| webtests | Oui | 
| workbooks | Oui | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| diagnosticSettings | Non  | 
| diagnosticSettingsCategories | Non  | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| IoTApps | Oui | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| Graph | Oui | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| deletedVaults | Non  | 
| vaults | Oui | 
| vaults/accessPolicies | Non  | 
| vaults/secrets | Non  | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| clusters | Oui | 
| clusters/databases | Non  | 
| clusters/databases/dataconnections | Non  | 
| clusters/databases/eventhubconnections | Non  | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| labaccounts | Oui | 
| users | Non  | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| journaux d’activité | Non  | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| integrationAccounts | Oui | 
| workflows | Oui | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| commitmentPlans | Oui | 
| webServices | Oui | 
| Workspaces | Oui | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 
| accounts/workspaces | Oui | 
| accounts/workspaces/projects | Oui | 
| teamAccounts | Oui | 
| teamAccounts/workspaces | Oui | 
| teamAccounts/workspaces/projects | Oui | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| workspaces | Oui | 
| workspaces/computes | Non  | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| Identities | Non  | 
| userAssignedIdentities | Oui | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| getEntities | Non  | 
| managementGroups | Non  | 
| les ressources | Non  | 
| startTenantBackfill | Non  | 
| tenantBackfillStatus | Non  | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 
| accounts/eventGridFilters | Non  | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| offers | Non  | 
| offerTypes | Non  | 
| offerTypes/publishers | Non  | 
| offerTypes/publishers/offers | Non  | 
| offerTypes/publishers/offers/plans | Non  | 
| offerTypes/publishers/offers/plans/agreements | Non  | 
| offerTypes/publishers/offers/plans/configs | Non  | 
| offerTypes/publishers/offers/plans/configs/importImage | Non  | 
| privategalleryitems | Non  | 
| products | Non  | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| classicDevServices | Oui | 
| updateCommunicationPreference | Non  | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| agreements | Non  | 
| offertypes | Non  | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Effectuer la suppression du mode |
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
| mediaservices/streamingEndpointOperations | Non  | 
| mediaservices/streamingEndpoints | Oui | 
| mediaservices/streamingLocators | Non  | 
| mediaservices/streamingPolicies | Non  | 
| mediaservices/transforms | Non  | 
| mediaservices/transforms/jobs | Non  | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| projects | Oui | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| applicationGateways | Oui | 
| applicationSecurityGroups | Oui | 
| azureFirewallFqdnTags | Non  | 
| azureFirewalls | Oui | 
| bgpServiceCommunities | Non  | 
| connections | Oui | 
| ddosCustomPolicies | Oui | 
| ddosProtectionPlans | Oui | 
| dnsOperationStatuses | Non  | 
| dnszones | Oui | 
| dnszones/A | Non  | 
| dnszones/AAAA | Non  | 
| dnszones/all | Non  | 
| dnszones/CAA | Non  | 
| dnszones/CNAME | Non  | 
| dnszones/MX | Non  | 
| dnszones/NS | Non  | 
| dnszones/PTR | Non  | 
| dnszones/recordsets | Non  | 
| dnszones/SOA | Non  | 
| dnszones/SRV | Non  | 
| dnszones/TXT | Non  | 
| expressRouteCircuits | Oui | 
| expressRouteServiceProviders | Non  | 
| frontdoors | Oui | 
| frontdoorWebApplicationFirewallPolicies | Oui | 
| getDnsResourceReference | Non  | 
| interfaceEndpoints | Oui | 
| internalNotify | Non  | 
| loadBalancers | Oui | 
| localNetworkGateways | Oui | 
| natGateways | Oui | 
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
| trafficManagerGeographicHierarchies | Non  | 
| trafficmanagerprofiles | Oui | 
| trafficmanagerprofiles/heatMaps | Non  | 
| virtualHubs | Oui | 
| virtualNetworkGateways | Oui | 
| virtualNetworks | Oui | 
| virtualNetworkTaps | Oui | 
| virtualWans | Oui | 
| vpnGateways | Oui | 
| vpnSites | Oui | 
| webApplicationFirewallPolicies | Oui | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/notificationHubs | Oui | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| périphériques | Non  | 
| linkTargets | Non  | 
| storageInsightConfigs | Non  | 
| workspaces | Oui | 
| workspaces/dataSources | Non  | 
| workspaces/linkedServices | Non  | 
| workspaces/query | Non  | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| managementassociations | Non  | 
| managementconfigurations | Oui | 
| solutions | Oui | 
| views | Oui | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| policyEvents | Non  | 
| policyStates | Non  | 
| policyTrackedResources | Non  | 
| remediations | Non  | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| consoles | Non  | 
| dashboards | Oui | 
| userSettings | Non  | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| workspaceCollections | Oui | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| capacities | Oui | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| accounts | Oui | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| backupProtectedItems | Non  | 
| vaults | Oui | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/authorizationrules | Non  | 
| namespaces/hybridconnections | Non  | 
| namespaces/hybridconnections/authorizationrules | Non  | 
| namespaces/wcfrelays | Non  | 
| namespaces/wcfrelays/authorizationrules | Non  | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| les ressources | Non  | 
| subscriptionsStatus | Non  | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| availabilityStatuses | Non  | 
| childAvailabilityStatuses | Non  | 
| childResources | Non  | 
| événements | Non  | 
| impactedResources | Non  | 
| Notifications | Non  | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| deployments | Non  | 
| deployments/operations | Non  | 
| links | Non  | 
| notifyResourceJobs | Non  | 
| fournisseurs | Non  | 
| resourceGroups | Non  | 
| les ressources | Non  | 
| subscriptions | Non  | 
| subscriptions/providers | Non  | 
| subscriptions/resourceGroups | Non  | 
| subscriptions/resourcegroups/resources | Non  | 
| subscriptions/resources | Non  | 
| subscriptions/tagnames | Non  | 
| subscriptions/tagNames/tagValues | Non  | 
| tenants | Non  | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| applications | Oui | 
| saasresources | Non  | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| flows | Oui | 
| jobcollections | Oui | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| resourceHealthMetadata | Non  | 
| searchServices | Oui | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Non  | 
| alertes | Non  | 
| allowedConnections | Non  | 
| appliances | Non  | 
| applicationWhitelistings | Non  | 
| AutoProvisioningSettings | Non  | 
| Compliances | Non  | 
| dataCollectionAgents | Non  | 
| discoveredSecuritySolutions | Non  | 
| externalSecuritySolutions | Non  | 
| InformationProtectionPolicies | Non  | 
| jitNetworkAccessPolicies | Non  | 
| monitoring | Non  | 
| monitoring/antimalware | Non  | 
| monitoring/baseline | Non  | 
| monitoring/patch | Non  | 
| stratégies | Non  | 
| pricings | Non  | 
| securityContacts | Non  | 
| securitySolutions | Non  | 
| securitySolutionsReferenceData | Non  | 
| securityStatus | Non  | 
| securityStatus/endpoints | Non  | 
| securityStatus/subnets | Non  | 
| securityStatus/virtualMachines | Non  | 
| securityStatuses | Non  | 
| securityStatusesSummaries | Non  | 
| paramètres | Non  | 
| tâches | Non  | 
| topologies | Non  | 
| workspaceSettings | Non  | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| diagnosticSettings | Non  | 
| diagnosticSettingsCategories | Non  | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| namespaces | Oui | 
| namespaces/authorizationrules | Non  | 
| namespaces/disasterrecoveryconfigs | Non  | 
| namespaces/eventgridfilters | Non  | 
| namespaces/queues | Non  | 
| namespaces/queues/authorizationrules | Non  | 
| namespaces/topics | Non  | 
| namespaces/topics/authorizationrules | Non  | 
| namespaces/topics/subscriptions | Non  | 
| namespaces/topics/subscriptions/rules | Non  | 
| premiumMessagingRegions | Non  | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| clusters | Oui | 
| clusters/applications | Non  | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| applications | Oui | 
| gateways | Oui | 
| networks | Oui | 
| secrets | Oui | 
| volumes | Oui | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| SignalR | Oui | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| applianceDefinitions | Oui | 
| appliances | Oui | 
| applicationDefinitions | Oui | 
| applications | Oui | 
| jitRequests | Oui | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| managedInstances | Oui |
| managedInstances/databases | Oui (voir la remarque ci-dessous) |
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
| servers/communicationLinks | Non  | 
| servers/databases | Oui (voir la remarque ci-dessous) | 
| servers/encryptionProtector | Non  | 
| servers/firewallRules | Non  | 
| servers/keys | Non  | 
| servers/restorableDroppedDatabases | Non  | 
| servers/serviceobjectives | Non  | 
| servers/tdeCertificates | Non  | 

> [!NOTE]
> La base de données MASTER ne prend pas en charge les balises, à la différence d’autres bases de données, comme les bases de données Data Warehouse.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Oui | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Non  | 
| SqlVirtualMachines | Oui | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| storageAccounts | Oui | 
| storageAccounts/blobServices | Non  | 
| storageAccounts/fileServices | Non  | 
| storageAccounts/queueServices | Non  | 
| storageAccounts/services | Non  | 
| storageAccounts/tableServices | Non  | 
| usages | Non  | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| storageSyncServices | Oui | 
| storageSyncServices/registeredServers | Non  | 
| storageSyncServices/syncGroups | Non  | 
| storageSyncServices/syncGroups/cloudEndpoints | Non  | 
| storageSyncServices/syncGroups/serverEndpoints | Non  | 
| storageSyncServices/workflows | Non  | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| managers | Oui | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| streamingjobs | Oui (voir la remarque ci-dessous) | 
| streamingjobs/diagnosticSettings | Non  | 

> [!NOTE]
> Vous ne pouvez pas ajouter une balise lorsque streamingjobs est en cours d’exécution. Arrêtez la ressource pour pouvoir ajouter une balise.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| CreateSubscription | Non  | 
| SubscriptionDefinitions | Non  | 
| SubscriptionOperations | Non  | 

## <a name="microsoftsupport"></a>microsoft.support
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| supporttickets | Non  | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| providerRegistrations | Oui | 
| les ressources | Oui | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| environments | Oui | 
| environments/accessPolicies | Non  | 
| environments/eventsources | Oui | 
| environments/referencedatasets | Oui | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| account | Oui | 
| account/extension | Oui | 
| account/project | Oui | 

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Effectuer la suppression du mode |
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
| hostingEnvironments/multiRolePools | Non  | 
| hostingEnvironments/multiRolePools/instances | Non  | 
| hostingEnvironments/workerPools | Non  | 
| hostingEnvironments/workerPools/instances | Non  | 
| publishingUsers | Non  | 
| de films | Non  | 
| resourceHealthMetadata | Non  | 
| runtimes | Non  | 
| serverFarms | Oui | 
| serverFarms/workers | Non  | 
| sites | Oui | 
| sites/domainOwnershipIdentifiers | Non  | 
| sites/hostNameBindings | Non  | 
| sites/instances | Non  | 
| sites/instances/extensions | Non  | 
| sites/premieraddons | Oui | 
| sites/recommendations | Non  | 
| sites/resourceHealthMetadata | Non  | 
| sites/slots | Oui | 
| sites/slots/hostNameBindings | Non  | 
| sites/slots/instances | Non  | 
| sites/slots/instances/extensions | Non  | 
| sourceControls | Non  | 
| validate | Non  | 
| verifyHostingEnvironmentVnet | Non  | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| diagnosticSettings | Non  | 
| diagnosticSettingsCategories | Non  | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| DeviceServices | Oui | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Type de ressource | Effectuer la suppression du mode |
| ------------- | ----------- |
| components | Non  | 
| componentsSummary | Non  | 
| monitorInstances | Non  | 
| monitorInstancesSummary | Non  | 
| monitors | Non  | 
| notificationSettings | Non  | 

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment appliquer des étiquettes aux ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](resource-group-using-tags.md).

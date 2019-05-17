---
title: Prise en charge des étiquettes Azure Resource Manager pour les ressources
description: Indique les types de ressources Azure qui prennent en charge les étiquettes. Fournit des détails pour tous les services Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/10/2019
ms.author: tomfitz
ms.openlocfilehash: 7ef37323fb8150e3a6b52800bfafa2585ae328c2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523234"
---
# <a name="tag-support-for-azure-resources"></a>Prise en charge des étiquettes pour les ressources Azure
Cet article indique si un type de ressource prend en charge les [étiquettes](resource-group-using-tags.md). La colonne intitulée **prend en charge les balises** indique si le type de ressource a une propriété pour la balise. La colonne intitulée **balise dans le rapport de coût** indique si ce type de ressource transmet la balise pour le rapport de coût.

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [tag-tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| DomainServices | Oui | Oui |
| DomainServices/oucontainer | Non | Non |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Non |  Non |
| diagnosticSettingsCategories | Non |  Non |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| supportProviders | Non |  Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| aadsupportcases | Non |  Non |
| addsservices | Non |  Non |
| agents | Non |  Non |
| anonymousapiusers | Non |  Non |
| configuration | Non |  Non |
| journaux d’activité | Non |  Non |
| rapports | Non |  Non |
| services | Non |  Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| configurations | Non |  Non |
| generateRecommendations | Non |  Non |
| recommandations | Non |  Non |
| suppressions | Non |  Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| actionRules | Non |  Non |
| alertes | Non |  Non |
| alertsList | Non |  Non |
| alertsSummary | Non |  Non |
| alertsSummaryList | Non |  Non |
| smartDetectorAlertRules | Non |  Non |
| smartDetectorRuntimeEnvironments | Non |  Non |
| smartGroups | Non |  Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| serveurs | Oui | Oui |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| reportFeedback | Non |  Non |
| service | Oui | Oui |
| validateServiceName | Non |  Non |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| attestationProviders | Non |  Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| classicAdministrators | Non |  Non |
| denyAssignments | Non |  Non |
| elevateAccess | Non |  Non |
| locks | Non |  Non |
| autorisations | Non |  Non |
| policyAssignments | Non |  Non |
| policyDefinitions | Non |  Non |
| policySetDefinitions | Non |  Non |
| providerOperations | Non |  Non |
| roleAssignments | Non |  Non |
| roleDefinitions | Non |  Non |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| automationAccounts | Oui | Oui |
| automationAccounts/configurations | Oui | Oui |
| automationAccounts/jobs | Non |  Non |
| automationAccounts/runbooks | Oui | Oui |
| automationAccounts/softwareUpdateConfigurations | Non | Non |
| automationAccounts/webhooks | Non |  Non |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| environments | Non |  Non |
| environments/accounts | Non |  Non |
| environments/accounts/namespaces | Non |  Non |
| environments/accounts/namespaces/configurations | Non |  Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| b2cDirectories | Oui | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| registrations | Oui | Oui |
| registrations/customerSubscriptions | Non |  Non |
| registrations/products | Non |  Non |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| batchAccounts | Oui | Oui |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| billingAccounts | Non |  Non |
| billingAccounts/billingProfiles | Non |  Non |
| billingAccounts/billingProfiles/billingSubscriptions | Non |  Non |
| billingAccounts/billingProfiles/invoices | Non |  Non |
| billingAccounts/billingProfiles/invoices/pricesheet | Non |  Non |
| billingAccounts/billingProfiles/operationStatus | Non |  Non |
| billingAccounts/billingProfiles/paymentMethods | Non |  Non |
| billingAccounts/billingProfiles/policies | Non |  Non |
| billingAccounts/billingProfiles/pricesheet | Non |  Non |
| billingAccounts/billingProfiles/products | Non |  Non |
| billingAccounts/billingProfiles/transactions | Non |  Non |
| billingAccounts/billingSubscriptions | Non |  Non |
| billingAccounts/departments | Non |  Non |
| billingAccounts/eligibleOffers | Non |  Non |
| billingAccounts/enrollmentAccounts | Non |  Non |
| billingAccounts/invoices | Non |  Non |
| billingAccounts/invoiceSections | Non |  Non |
| billingAccounts/invoiceSections/billingSubscriptions | Non |  Non |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Non |  Non |
| billingAccounts/invoiceSections/importRequests | Non |  Non |
| billingAccounts/invoiceSections/initiateImportRequest | Non |  Non |
| billingAccounts/invoiceSections/initiateTransfer | Non |  Non |
| billingAccounts/invoiceSections/operationStatus | Non |  Non |
| billingAccounts/invoiceSections/products | Non |  Non |
| billingAccounts/invoiceSections/transfers | Non |  Non |
| billingAccounts/products | Non |  Non |
| billingAccounts/projects | Non |  Non |
| billingAccounts/projects/billingSubscriptions | Non |  Non |
| billingAccounts/projects/importRequests | Non |  Non |
| billingAccounts/projects/initiateImportRequest | Non |  Non |
| billingAccounts/projects/operationStatus | Non |  Non |
| billingAccounts/projects/products | Non |  Non |
| billingAccounts/transactions | Non |  Non |
| billingPeriods | Non |  Non |
| BillingPermissions | Non |  Non |
| billingProperty | Non |  Non |
| BillingRoleAssignments | Non |  Non |
| BillingRoleDefinitions | Non |  Non |
| CreateBillingRoleAssignment | Non |  Non |
| departments | Non |  Non |
| enrollmentAccounts | Non |  Non |
| importRequests | Non |  Non |
| importRequests/acceptImportRequest | Non |  Non |
| importRequests/declineImportRequest | Non |  Non |
| invoices | Non |  Non |
| transfers | Non |  Non |
| transfers/acceptTransfer | Non |  Non |
| transfers/declineTransfer | Non |  Non |
| transfers/operationStatus | Non |  Non |
| usagePlans | Non |  Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| mapApis | Oui | Oui |
| updateCommunicationPreference | Non |  Non |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| BizTalk | Oui | Oui |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Non |  Non |
| blueprintAssignments/assignmentOperations | Non |  Non |
| blueprintAssignments/operations | Non |  Non |
| blueprints | Non |  Non |
| blueprints/artifacts | Non |  Non |
| blueprints/versions | Non |  Non |
| blueprints/versions/artifacts | Non |  Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| botServices | Oui | Oui |
| botServices/channels | Non |  Non |
| botServices/connections | Non |  Non |

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| Redis | Oui | Oui |
| RedisConfigDefinition | Non |  Non |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| appliedReservations | Non |  Non |
| calculatePrice | Non |  Non |
| catalogs | Non |  Non |
| commercialReservationOrders | Non |  Non |
| reservationOrders | Non |  Non |
| reservationOrders/calculateRefund | Non |  Non |
| reservationOrders/merge | Non |  Non |
| reservationOrders/reservations | Non |  Non |
| reservationOrders/reservations/revisions | Non |  Non |
| reservationOrders/return | Non |  Non |
| reservationOrders/split | Non |  Non |
| reservationOrders/swap | Non |  Non |
| reservations | Non |  Non |
| ressources | Non |  Non |
| validateReservationOrder | Non |  Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| edgenodes | Non |  Non |
| profiles | Oui | Oui |
| profiles/endpoints | Oui | Oui |
| profiles/endpoints/customdomains | Non |  Non |
| profiles/endpoints/origins | Non |  Non |
| validateProbe | Non |  Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| certificateOrders | Oui | Oui |
| certificateOrders/certificates | Non |  Non |
| validateCertificateRegistrationInformation | Non |  Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| capabilities | Non |  Non |
| domainNames | Non |  Non |
| domainNames/capabilities | Non |  Non |
| domainNames/internalLoadBalancers | Non |  Non |
| domainNames/serviceCertificates | Non |  Non |
| domainNames/slots | Non |  Non |
| domainNames/slots/roles | Non |  Non |
| moveSubscriptionResources | Non |  Non |
| operatingSystemFamilies | Non |  Non |
| operatingSystems | Non |  Non |
| quotas | Non |  Non |
| resourceTypes | Non |  Non |
| validateSubscriptionMoveAvailability | Non |  Non |
| virtualMachines | Non |  Non |
| virtualMachines/diagnosticSettings | Non |  Non |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Non |  Non |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| capabilities | Non |  Non |
| expressRouteCrossConnections | Non |  Non |
| expressRouteCrossConnections/peerings | Non |  Non |
| gatewaySupportedDevices | Non |  Non |
| networkSecurityGroups | Non |  Non |
| quotas | Non |  Non |
| reservedIps | Non |  Non |
| virtualNetworks | Non |  Non |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Non |  Non |
| virtualNetworks/virtualNetworkPeerings | Non |  Non |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| capabilities | Non |  Non |
| disks | Non |  Non |
| images | Non |  Non |
| osImages | Non |  Non |
| osPlatformImages | Non |  Non |
| publicImages | Non |  Non |
| quotas | Non |  Non |
| storageAccounts | Non |  Non |
| storageAccounts/services | Non |  Non |
| storageAccounts/services/diagnosticSettings | Non |  Non |
| storageAccounts/vmImages | Non |  Non |
| vmImages | Non |  Non |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| RateCard | Non |  Non |
| UsageAggregates | Non |  Non |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| availabilitySets | Oui | Oui |
| disks | Oui | Oui |
| images | Oui | Oui |
| restorePointCollections | Oui | Oui |
| restorePointCollections/restorePoints | Non |  Non |
| sharedVMImages | Oui | Oui |
| sharedVMImages/versions | Oui | Oui |
| captures instantanées | Oui | Oui |
| virtualMachines | Oui | Oui |
| virtualMachines/diagnosticSettings | Non |  Non |
| virtualMachines/extensions | Oui | Oui |
| virtualMachineScaleSets | Oui | Oui |
| virtualMachineScaleSets/extensions | Non |  Non |
| virtualMachineScaleSets/networkInterfaces | Non |  Non |
| virtualMachineScaleSets/publicIPAddresses | Non |  Non |
| virtualMachineScaleSets/virtualMachines | Non |  Non |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Non |  Non |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| AggregatedCost | Non |  Non |
| Balances | Non |  Non |
| Budgets | Non |  Non |
| Charges | Non |  Non |
| CostTags | Non |  Non |
| credits | Non |  Non |
| événements | Non |  Non |
| Prévisions | Non |  Non |
| lots | Non |  Non |
| Marketplaces | Non |  Non |
| Pricesheets | Non |  Non |
| products | Non |  Non |
| ReservationDetails | Non |  Non |
| ReservationRecommendations | Non |  Non |
| ReservationSummaries | Non |  Non |
| ReservationTransactions | Non |  Non |
| Balises | Non |  Non |
| Conditions générales | Non |  Non |
| UsageDetails | Non |  Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| containerGroups | Oui | Oui |
| serviceAssociationLinks | Non |  Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| registries | Oui | Oui |
| registries/builds | Non |  Non |
| registries/builds/cancel | Non |  Non |
| registries/builds/getLogLink | Non |  Non |
| registries/buildTasks | Oui | Oui |
| registries/buildTasks/steps | Non |  Non |
| registries/eventGridFilters | Non |  Non |
| registries/getBuildSourceUploadUrl | Non |  Non |
| registries/GetCredentials | Non |  Non |
| registries/importImage | Non |  Non |
| registries/queueBuild | Non |  Non |
| registries/regenerateCredential | Non |  Non |
| registries/regenerateCredentials | Non |  Non |
| registries/replications | Oui | Oui |
| registries/runs | Non |  Non |
| registries/runs/cancel | Non |  Non |
| registries/scheduleRun | Non |  Non |
| registries/tasks | Oui | Oui |
| registries/updatePolicies | Non |  Non |
| registries/webhooks | Oui | Oui |
| registries/webhooks/getCallbackConfig | Non |  Non |
| registries/webhooks/ping | Non |  Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| containerServices | Oui | Oui |
| managedclusters | Oui | Oui |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| applications | Oui | Oui |
| updateCommunicationPreference | Non |  Non |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| Alertes | Non |  Non |
| BillingAccounts | Non |  Non |
| Connecteurs | Oui | Oui |
| Departments | Non |  Non |
| Dimensions | Non |  Non |
| EnrollmentAccounts | Non |  Non |
| Interroger | Non |  Non |
| inscription | Non |  Non |
| Reportconfigs | Non |  Non |
| Rapports | Non |  Non |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| hubs | Oui | Oui |
| hubs/authorizationPolicies | Non |  Non |
| hubs/connectors | Non |  Non |
| hubs/connectors/mappings | Non |  Non |
| hubs/interactions | Non |  Non |
| hubs/kpi | Non |  Non |
| hubs/links | Non |  Non |
| hubs/profiles | Non |  Non |
| hubs/roleAssignments | Non |  Non |
| hubs/roles | Non |  Non |
| hubs/suggestTypeSchema | Non |  Non |
| hubs/views | Non |  Non |
| hubs/widgetTypes | Non |  Non |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| tâches | Oui | Oui |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Oui | Oui |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| workspaces | Oui | Non |
| workspaces/virtualNetworkPeerings | Non |  Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| catalogs | Oui | Oui |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| connectionManagers | Oui | Oui |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| dataFactories | Oui | Non |
| dataFactories/diagnosticSettings | Non |  Non |
| dataFactorySchema | Non |  Non |
| factories | Oui | Non |
| factories/integrationRuntimes | Non |  Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |
| accounts/dataLakeStoreAccounts | Non |  Non |
| accounts/storageAccounts | Non |  Non |
| accounts/storageAccounts/containers | Non |  Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |
| accounts/eventGridFilters | Non |  Non |
| accounts/firewallRules | Non |  Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| services | Oui | Oui |
| services/projects | Oui | Oui |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| serveurs | Oui | Oui |
| servers/recoverableServers | Non |  Non |
| servers/virtualNetworkRules | Non |  Non |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| serveurs | Oui | Oui |
| servers/recoverableServers | Non |  Non |
| servers/virtualNetworkRules | Non |  Non |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| serveurs | Oui | Oui |
| servers/advisors | Non |  Non |
| servers/queryTexts | Non |  Non |
| servers/recoverableServers | Non |  Non |
| servers/topQueryStatistics | Non |  Non |
| servers/virtualNetworkRules | Non |  Non |
| servers/waitStatistics | Non |  Non |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| IoTHubs | Oui | Oui |
| IotHubs/eventGridFilters | Non |  Non |
| ProvisioningServices | Oui | Oui |
| usages | Non |  Non |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| Contrôleurs | Oui | Oui |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| labs | Oui | Oui |
| labs/serviceRunners | Oui | Oui |
| labs/virtualMachines | Oui | Oui |
| schedules | Oui | Oui |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Non |  Non |
| databaseAccounts | Oui | Oui |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| domaines | Oui | Oui |
| domains/domainOwnershipIdentifiers | Non |  Non |
| generateSsoRequest | Non |  Non |
| topLevelDomains | Non |  Non |
| validateDomainRegistrationInformation | Non |  Non |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| lcsprojects | Non |  Non |
| lcsprojects/clouddeployments | Non |  Non |
| lcsprojects/connectors | Non |  Non |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| domaines | Oui | Non |
| domains/topics | Non |  Non |
| eventSubscriptions | Non |  Non |
| extensionTopics | Non |  Non |
| topics | Oui | Non |
| topicTypes | Non |  Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| clusters | Oui | Non |
| espaces de noms | Oui | Non |
| namespaces/authorizationrules | Non |  Non |
| namespaces/disasterrecoveryconfigs | Non |  Non |
| namespaces/eventhubs | Non |  Non |
| namespaces/eventhubs/authorizationrules | Non |  Non |
| namespaces/eventhubs/consumergroups | Non |  Non |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| features | Non |  Non |
| fournisseurs | Non |  Non |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| inscrire | Non |  Non |
| galleryitems | Non |  Non |
| generateartifactaccessuri | Non |  Non |
| myareas | Non |  Non |
| myareas/areas | Non |  Non |
| myareas/areas/areas | Non |  Non |
| myareas/areas/areas/galleryitems | Non |  Non |
| myareas/areas/galleryitems | Non |  Non |
| myareas/galleryitems | Non |  Non |
| inscription | Non |  Non |
| ressources | Non |  Non |
| retrieveresourcesbyid | Non |  Non |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Non |  Non |
| software | Non |  Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| hanaInstances | Oui |  Oui |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| clusters | Oui | Oui |
| clusters/applications | Non |  Non |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| tâches | Oui | Oui |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| labelGroups | Non |  Non |
| labelGroups/labels | Non |  Non |
| labelGroups/labels/conditions | Non |  Non |
| labelGroups/labels/subLabels | Non |  Non |
| labelGroups/labels/subLabels/conditions | Non |  Non |

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| actiongroups | Oui | Oui |
| activityLogAlerts | Oui | Oui |
| alertrules | Oui | Oui |
| automatedExportSettings | Non |  Non |
| autoscalesettings | Oui | Oui |
| baseline | Non |  Non |
| calculatebaseline | Non |  Non |
| components | Oui | Oui |
| components/events | Non |  Non |
| components/pricingPlans | Non |  Non |
| components/query | Non |  Non |
| diagnosticSettings | Non |  Non |
| diagnosticSettingsCategories | Non |  Non |
| eventCategories | Non |  Non |
| eventtypes | Non |  Non |
| extendedDiagnosticSettings | Non |  Non |
| logDefinitions | Non |  Non |
| logprofiles | Non |  Non |
| journaux d’activité | Non |  Non |
| metricAlerts | Oui | Oui |
| migrateToNewPricingModel | Non |  Non |
| myWorkbooks | Non |  Non |
| queries | Non |  Non |
| rollbackToLegacyPricingModel | Non |  Non |
| scheduledqueryrules | Oui | Oui |
| vmInsightsOnboardingStatuses | Non |  Non |
| webtests | Oui | Oui |
| classeurs | Oui | Oui |

## <a name="microsoftintune"></a>Microsoft.Intune
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Non |  Non |
| diagnosticSettingsCategories | Non |  Non |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| IoTApps | Oui | Oui |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| Graphe | Oui | Oui |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| deletedVaults | Non |  Non |
| vaults | Oui | Oui |
| vaults/accessPolicies | Non |  Non |
| vaults/secrets | Non |  Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| clusters | Oui | Oui |
| clusters/databases | Non |  Non |
| clusters/databases/dataconnections | Non |  Non |
| clusters/databases/eventhubconnections | Non |  Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| labaccounts | Oui | Oui |
| utilisateurs  | Non |  Non |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| journaux d’activité | Non |  Non |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| integrationAccounts | Oui | Oui |
| workflows | Oui | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| commitmentPlans | Oui | Oui |
| webServices | Oui | Oui |
| Workspaces | Oui | Oui |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |
| accounts/workspaces | Oui | Oui |
| accounts/workspaces/projects | Oui | Oui |
| teamAccounts | Oui | Oui |
| teamAccounts/workspaces | Oui | Oui |
| teamAccounts/workspaces/projects | Oui | Oui |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| workspaces | Oui | Oui |
| workspaces/computes | Non |  Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| Identités | Non |  Non |
| userAssignedIdentities | Oui | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| getEntities | Non |  Non |
| managementGroups | Non |  Non |
| ressources | Non |  Non |
| startTenantBackfill | Non |  Non |
| tenantBackfillStatus | Non |  Non |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |
| accounts/eventGridFilters | Non |  Non |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| offers | Non |  Non |
| offerTypes | Non |  Non |
| offerTypes/publishers | Non |  Non |
| offerTypes/publishers/offers | Non |  Non |
| offerTypes/publishers/offers/plans | Non |  Non |
| offerTypes/publishers/offers/plans/agreements | Non |  Non |
| offerTypes/publishers/offers/plans/configs | Non |  Non |
| offerTypes/publishers/offers/plans/configs/importImage | Non |  Non |
| privategalleryitems | Non |  Non |
| products | Non |  Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| classicDevServices | Oui | Oui |
| updateCommunicationPreference | Non |  Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| agreements | Non |  Non |
| offertypes | Non |  Non |

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| mediaservices | Oui | Oui |
| mediaservices/accountFilters | Non |  Non |
| mediaservices/assets | Non |  Non |
| mediaservices/assets/assetFilters | Non |  Non |
| mediaservices/contentKeyPolicies | Non |  Non |
| mediaservices/eventGridFilters | Non |  Non |
| mediaservices/liveEventOperations | Non |  Non |
| mediaservices/liveEvents | Oui | Oui |
| mediaservices/liveEvents/liveOutputs | Non |  Non |
| mediaservices/liveOutputOperations | Non |  Non |
| mediaservices/streamingEndpointOperations | Non |  Non |
| mediaservices/streamingEndpoints | Oui | Oui |
| mediaservices/streamingLocators | Non |  Non |
| mediaservices/streamingPolicies | Non |  Non |
| mediaservices/transforms | Non |  Non |
| mediaservices/transforms/jobs | Non |  Non |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| projects | Oui | Oui |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| applicationGateways | Oui | Non |
| applicationSecurityGroups | Oui | Oui |
| azureFirewallFqdnTags | Non |  Non |
| azureFirewalls | Oui | Non |
| bgpServiceCommunities | Non |  Non |
| connexions | Oui | Oui |
| ddosCustomPolicies | Oui | Oui |
| ddosProtectionPlans | Oui | Oui |
| dnsOperationStatuses | Non |  Non |
| dnszones | Oui | Oui |
| dnszones/A | Non |  Non |
| dnszones/AAAA | Non |  Non |
| dnszones/all | Non |  Non |
| dnszones/CAA | Non |  Non |
| dnszones/CNAME | Non |  Non |
| dnszones/MX | Non |  Non |
| dnszones/NS | Non |  Non |
| dnszones/PTR | Non |  Non |
| dnszones/recordsets | Non |  Non |
| dnszones/SOA | Non |  Non |
| dnszones/SRV | Non |  Non |
| dnszones/TXT | Non |  Non |
| expressRouteCircuits | Oui  | Non |
| expressRouteServiceProviders | Non |  Non |
| portes d'entrée | Oui | Oui |
| frontdoorWebApplicationFirewallPolicies | Oui | Oui |
| getDnsResourceReference | Non |  Non |
| interfaceEndpoints | Oui | Oui |
| internalNotify | Non |  Non |
| loadBalancers | Oui | Non |
| localNetworkGateways | Oui | Oui |
| natGateways | Oui | Oui |
| networkIntentPolicies | Oui | Oui |
| networkInterfaces | Oui | Oui |
| networkProfiles | Oui | Oui |
| networkSecurityGroups | Oui | Oui |
| networkWatchers | Oui | Non |
| networkWatchers/connectionMonitors | Oui | Non |
| networkWatchers/lenses | Oui | Non |
| networkWatchers/pingMeshes | Oui | Non |
| privateLinkServices | Oui | Oui |
| publicIPAddresses | Oui | Oui |
| publicIPPrefixes | Oui | Oui |
| routeFilters | Oui | Oui |
| routeTables | Oui | Oui |
| serviceEndpointPolicies | Oui | Oui |
| trafficManagerGeographicHierarchies | Non |  Non |
| trafficmanagerprofiles | Oui | Oui |
| trafficmanagerprofiles/heatMaps | Non |  Non |
| virtualHubs | Oui | Oui |
| virtualNetworkGateways | Oui | Non |
| virtualNetworks | Oui | Oui |
| virtualNetworks/subnets | Non |  Non |
| virtualNetworkTaps | Oui | Oui |
| virtualWans | Oui | Oui |
| vpnGateways | Oui | Non |
| vpnSites | Oui | Oui |
| webApplicationFirewallPolicies | Oui | Oui |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| espaces de noms | Oui | Non |
| namespaces/notificationHubs | Oui | Non |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| appareils | Non |  Non |
| linkTargets | Non |  Non |
| storageInsightConfigs | Non |  Non |
| workspaces | Oui | Oui |
| workspaces/dataSources | Non |  Non |
| workspaces/linkedServices | Non |  Non |
| workspaces/query | Non |  Non |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| managementassociations | Non |  Non |
| managementconfigurations | Oui | Oui |
| solutions | Oui | Oui |
| views | Oui | Oui |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| policyEvents | Non |  Non |
| policyStates | Non |  Non |
| policyTrackedResources | Non |  Non |
| remediations | Non |  Non |

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| consoles | Non |  Non |
| dashboards | Oui | Oui |
| userSettings | Non |  Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| workspaceCollections | Oui | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| capacities | Oui | Oui |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| comptes | Oui | Oui |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Non |  Non |
| vaults | Oui | Oui |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| espaces de noms | Oui | Oui |
| namespaces/authorizationrules | Non |  Non |
| namespaces/hybridconnections | Non |  Non |
| namespaces/hybridconnections/authorizationrules | Non |  Non |
| namespaces/wcfrelays | Non |  Non |
| namespaces/wcfrelays/authorizationrules | Non |  Non |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| ressources | Non |  Non |
| subscriptionsStatus | Non |  Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Non |  Non |
| childAvailabilityStatuses | Non |  Non |
| childResources | Non |  Non |
| événements | Non |  Non |
| impactedResources | Non |  Non |
| notifications | Non |  Non |

## <a name="microsoftresources"></a>Microsoft.Resources
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| déploiements | Non |  Non |
| deployments/operations | Non |  Non |
| links | Non |  Non |
| notifyResourceJobs | Non |  Non |
| fournisseurs | Non |  Non |
| resourceGroups | Non |  Non |
| ressources | Non |  Non |
| abonnements | Non |  Non |
| subscriptions/providers | Non |  Non |
| subscriptions/resourceGroups | Non |  Non |
| subscriptions/resourcegroups/resources | Non |  Non |
| subscriptions/resources | Non |  Non |
| subscriptions/tagnames | Non |  Non |
| subscriptions/tagNames/tagValues | Non |  Non |
| tenants | Non |  Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| applications | Oui | Oui |
| saasresources | Non |  Non |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| flux | Oui | Oui |
| jobcollections | Oui | Oui |

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Non |  Non |
| searchServices | Oui | Oui |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Non |  Non |
| alertes | Non |  Non |
| allowedConnections | Non |  Non |
| appliances | Non |  Non |
| applicationWhitelistings | Non |  Non |
| AutoProvisioningSettings | Non |  Non |
| Compliances | Non |  Non |
| dataCollectionAgents | Non |  Non |
| discoveredSecuritySolutions | Non |  Non |
| externalSecuritySolutions | Non |  Non |
| InformationProtectionPolicies | Non |  Non |
| jitNetworkAccessPolicies | Non |  Non |
| monitoring | Non |  Non |
| monitoring/antimalware | Non |  Non |
| monitoring/baseline | Non |  Non |
| monitoring/patch | Non |  Non |
| stratégies | Non |  Non |
| pricings | Non |  Non |
| securityContacts | Non |  Non |
| securitySolutions | Non |  Non |
| securitySolutionsReferenceData | Non |  Non |
| securityStatus | Non |  Non |
| securityStatus/endpoints | Non |  Non |
| securityStatus/subnets | Non |  Non |
| securityStatus/virtualMachines | Non |  Non |
| securityStatuses | Non |  Non |
| securityStatusesSummaries | Non |  Non |
| paramètres | Non |  Non |
| tâches | Non |  Non |
| topologies | Non |  Non |
| workspaceSettings | Non |  Non |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Non |  Non |
| diagnosticSettingsCategories | Non |  Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| espaces de noms | Oui | Non |
| namespaces/authorizationrules | Non |  Non |
| namespaces/disasterrecoveryconfigs | Non |  Non |
| namespaces/eventgridfilters | Non |  Non |
| namespaces/queues | Non |  Non |
| namespaces/queues/authorizationrules | Non |  Non |
| namespaces/topics | Non |  Non |
| namespaces/topics/authorizationrules | Non |  Non |
| namespaces/topics/subscriptions | Non |  Non |
| namespaces/topics/subscriptions/rules | Non |  Non |
| premiumMessagingRegions | Non |  Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| clusters | Oui | Oui |
| clusters/applications | Non |  Non |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| applications | Oui | Oui |
| gateways | Oui | Oui |
| Réseaux | Oui | Oui |
| secrets | Oui | Oui |
| volumes | Oui | Oui |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| SignalR | Oui | Oui |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Oui | Oui |
| appliances | Oui | Oui |
| applicationDefinitions | Oui | Oui |
| applications | Oui | Oui |
| jitRequests | Oui | Oui |

## <a name="microsoftsql"></a>Microsoft.SQL
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| managedInstances | Oui | Oui |
| managedInstances/databases | Oui (voir la remarque ci-dessous) | Oui |
| managedInstances/databases/backupShortTermRetentionPolicies | Non | Non |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Non | Non |
| managedInstances/databases/vulnerabilityAssessments | Non | Non |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Non | Non |
| managedInstances/encryptionProtector | Non | Non |
| managedInstances/keys | Non | Non |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Non | Non |
| managedInstances/vulnerabilityAssessments | Non | Non |
| serveurs | Oui | Oui |
| servers/administrators | Non |  Non |
| servers/communicationLinks | Non |  Non |
| servers/databases | Oui (voir la remarque ci-dessous) | Oui |
| servers/encryptionProtector | Non |  Non |
| servers/firewallRules | Non |  Non |
| servers/keys | Non |  Non |
| servers/restorableDroppedDatabases | Non |  Non |
| servers/serviceobjectives | Non |  Non |
| servers/tdeCertificates | Non |  Non |

> [!NOTE]
> La base de données MASTER ne prend pas en charge les balises, à la différence d’autres bases de données, comme les bases de données Azure SQL Data Warehouse. Les bases de données Azure SQL Data Warehouse doivent avoir l’état Actif (pas en pause).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Oui | Oui |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Non |  Non |
| SqlVirtualMachines | Oui | Oui |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| storageAccounts | Oui | Oui |
| storageAccounts/blobServices | Non |  Non |
| storageAccounts/fileServices | Non |  Non |
| storageAccounts/queueServices | Non |  Non |
| storageAccounts/services | Non |  Non |
| storageAccounts/tableServices | Non |  Non |
| usages | Non |  Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| storageSyncServices | Oui | Oui |
| storageSyncServices/registeredServers | Non |  Non |
| storageSyncServices/syncGroups | Non |  Non |
| storageSyncServices/syncGroups/cloudEndpoints | Non |  Non |
| storageSyncServices/syncGroups/serverEndpoints | Non |  Non |
| storageSyncServices/workflows | Non |  Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| managers | Oui | Oui |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| streamingjobs | Oui (voir la remarque ci-dessous) | Oui |
| streamingjobs/diagnosticSettings | Non |  Non |

> [!NOTE]
> Vous ne pouvez pas ajouter une balise lorsque streamingjobs est en cours d’exécution. Arrêtez la ressource pour pouvoir ajouter une balise.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| CreateSubscription | Non |  Non |
| SubscriptionDefinitions | Non |  Non |
| SubscriptionOperations | Non |  Non |

## <a name="microsoftsupport"></a>microsoft.support
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| supporttickets | Non |  Non |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| providerRegistrations | Oui | Oui |
| ressources | Oui | Oui |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| environments | Oui | Non |
| environments/accessPolicies | Non |  Non |
| environments/eventsources | Oui | Non |
| environments/referencedatasets | Oui | Non |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| compte | Oui | Oui |
| account/extension | Oui | Oui |
| account/project | Oui | Oui |

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Non |  Non |
| apiManagementAccounts/apiAcls | Non |  Non |
| apiManagementAccounts/apis | Non |  Non |
| apiManagementAccounts/apis/apiAcls | Non |  Non |
| apiManagementAccounts/apis/connectionAcls | Non |  Non |
| apiManagementAccounts/apis/connections | Non |  Non |
| apiManagementAccounts/apis/connections/connectionAcls | Non |  Non |
| apiManagementAccounts/apis/localizedDefinitions | Non |  Non |
| apiManagementAccounts/connectionAcls | Non |  Non |
| apiManagementAccounts/connections | Non |  Non |
| billingMeters | Non |  Non |
| certificats | Oui | Oui |
| connectionGateways | Oui | Oui |
| connexions | Oui | Oui |
| customApis | Oui | Oui |
| deletedSites | Non |  Non |
| functions | Non |  Non |
| hostingEnvironments | Oui | Non |
| hostingEnvironments/multiRolePools | Non |  Non |
| hostingEnvironments/multiRolePools/instances | Non |  Non |
| hostingEnvironments/workerPools | Non |  Non |
| hostingEnvironments/workerPools/instances | Non |  Non |
| publishingUsers | Non |  Non |
| recommandations | Non |  Non |
| resourceHealthMetadata | Non |  Non |
| runtimes | Non |  Non |
| serverFarms | Oui | Oui |
| serverFarms/workers | Non |  Non |
| sites | Oui | Oui |
| sites/domainOwnershipIdentifiers | Non |  Non |
| sites/hostNameBindings | Non |  Non |
| sites/instances | Non |  Non |
| sites/instances/extensions | Non |  Non |
| sites/premieraddons | Oui | Oui |
| sites/recommendations | Non |  Non |
| sites/resourceHealthMetadata | Non |  Non |
| sites/slots | Oui | Oui |
| sites/slots/hostNameBindings | Non |  Non |
| sites/slots/instances | Non |  Non |
| sites/slots/instances/extensions | Non |  Non |
| sourceControls | Non |  Non |
| validate | Non |  Non |
| verifyHostingEnvironmentVnet | Non |  Non |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Non |  Non |
| diagnosticSettingsCategories | Non |  Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| DeviceServices | Oui | Oui |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Type de ressource | Prend en charge les étiquettes | Appliquer des balises dans le rapport de coût |
| ------------- | ----------- | ----------- |
| components | Non |  Non |
| componentsSummary | Non |  Non |
| monitorInstances | Non |  Non |
| monitorInstancesSummary | Non |  Non |
| monitors | Non |  Non |
| notificationSettings | Non |  Non  |

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment appliquer des étiquettes aux ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](resource-group-using-tags.md).

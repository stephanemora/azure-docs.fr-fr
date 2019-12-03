---
title: Effectuer la suppression du mode
description: Montre comment les types de ressources gèrent la suppression du mode complet dans les modèles Azure Resource Manager.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232692"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Suppression de ressources Azure pour les déploiements en mode complet

Cet article décrit la manière dont les types de ressources gèrent la suppression en dehors d'un modèle déployé en mode complet.

Les types de ressources marqués d’un **Oui** sont supprimés lorsque le type n’est pas présent dans le modèle déployé en mode complet.

Les types de ressources marqués d’un **Non** ne sont pas automatiquement supprimés lorsqu'ils ne sont pas présents dans le modèle, mais sont supprimés si la ressource parente est supprimée. Pour une description complète du comportement, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).

Si vous déployez sur [plusieurs groupes de ressources dans un modèle](resource-manager-cross-resource-group-deployment.md), les ressources du groupe de ressources spécifié dans le déploiement peuvent être supprimées. Les ressources dans les groupes de ressources secondaires ne sont pas supprimées.

Accédez à un espace de noms du fournisseur de ressources :
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DomainServices | OUI |
> | DomainServices / oucontainer | Non |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | supportProviders | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | aadsupportcases | Non |
> | addsservices | Non |
> | agents | Non |
> | anonymousapiusers | Non |
> | configuration | Non |
> | logs | Non |
> | reports | Non |
> | servicehealthmetrics | Non |
> | services | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurations | Non |
> | generateRecommendations | Non |
> | metadata | Non |
> | de films | Non |
> | suppressions | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | actionRules | OUI |
> | alertes | Non |
> | alertsList | Non |
> | alertsMetaData | Non |
> | alertsSummary | Non |
> | alertsSummaryList | Non |
> | feedback | Non |
> | smartDetectorAlertRules | OUI |
> | smartDetectorRuntimeEnvironments | Non |
> | smartGroups | Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | reportFeedback | Non |
> | service | OUI |
> | validateServiceName | Non |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurationStores | OUI |
> | configurationStores / eventGridFilters | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Spring | OUI |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | attestationProviders | Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicAdministrators | Non |
> | dataAliases | Non |
> | denyAssignments | Non |
> | elevateAccess | Non |
> | findOrphanRoleAssignments | Non |
> | locks | Non |
> | Autorisations | Non |
> | policyAssignments | Non |
> | policyDefinitions | Non |
> | policySetDefinitions | Non |
> | providerOperations | Non |
> | roleAssignments | Non |
> | roleDefinitions | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | automationAccounts | OUI |
> | automationAccounts / configurations | OUI |
> | automationAccounts / jobs | Non |
> | automationAccounts / runbooks | OUI |
> | automationAccounts / softwareUpdateConfigurations | Non |
> | automationAccounts / webhooks | Non |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurationStores | OUI |
> | configurationStores / eventGridFilters | Non |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | Non |
> | environments / accounts | Non |
> | environments / accounts / namespaces | Non |
> | environments / accounts / namespaces / configurations | Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | b2cDirectories | OUI |
> | b2ctenants | Non |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hybridDataManagers | OUI |
> | postgresInstances | OUI |
> | sqlBigDataClusters | OUI |
> | sqlInstances | OUI |
> | sqlServerRegistrations | OUI |
> | sqlServerRegistrations / sqlServers | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | registrations | OUI |
> | registrations / customerSubscriptions | Non |
> | registrations / products | Non |
> | verificationKeys | Non |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | batchAccounts | OUI |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | billingAccounts | Non |
> | billingAccounts / agreements | Non |
> | billingAccounts / billingPermissions | Non |
> | billingAccounts / billingProfiles | Non |
> | billingAccounts / billingProfiles / billingPermissions | Non |
> | billingAccounts / billingProfiles / billingRoleAssignments | Non |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Non |
> | billingAccounts / billingProfiles / billingSubscriptions | Non |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Non |
> | billingAccounts / billingProfiles / customers | Non |
> | billingAccounts / billingProfiles / invoices | Non |
> | billingAccounts / billingProfiles / invoices / pricesheet | Non |
> | billingAccounts / billingProfiles / invoiceSections | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Non |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Non |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Non |
> | billingAccounts / billingProfiles / invoiceSections / products | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Non |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Non |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Non |
> | billingAccounts / BillingProfiles / patchOperations | Non |
> | billingAccounts / billingProfiles / paymentMethods | Non |
> | billingAccounts / billingProfiles / policies | Non |
> | billingAccounts / billingProfiles / pricesheet | Non |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Non |
> | billingAccounts / billingProfiles / products | Non |
> | billingAccounts / billingProfiles / transactions | Non |
> | billingAccounts / billingRoleAssignments | Non |
> | billingAccounts / billingRoleDefinitions | Non |
> | billingAccounts / billingSubscriptions | Non |
> | billingAccounts / billingSubscriptions / invoices | Non |
> | billingAccounts / createBillingRoleAssignment | Non |
> | billingAccounts / createInvoiceSectionOperations | Non |
> | billingAccounts / customers | Non |
> | billingAccounts / customers / billingPermissions | Non |
> | billingAccounts / customers / billingSubscriptions | Non |
> | billingAccounts / customers / initiateTransfer | Non |
> | billingAccounts / customers / policies | Non |
> | billingAccounts / customers / products | Non |
> | billingAccounts / customers / transactions | Non |
> | billingAccounts / customers / transfers | Non |
> | billingAccounts / departments | Non |
> | billingAccounts / enrollmentAccounts | Non |
> | billingAccounts / invoices | Non |
> | billingAccounts / invoiceSections | Non |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Non |
> | billingAccounts / invoiceSections / billingSubscriptions | Non |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Non |
> | billingAccounts / invoiceSections / elevate | Non |
> | billingAccounts / invoiceSections / initiateTransfer | Non |
> | billingAccounts / invoiceSections / patchOperations | Non |
> | billingAccounts / invoiceSections / productMoveOperations | Non |
> | billingAccounts / invoiceSections / products | Non |
> | billingAccounts / invoiceSections / products / transfer | Non |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Non |
> | billingAccounts / invoiceSections / transactions | Non |
> | billingAccounts / invoiceSections / transfers | Non |
> | billingAccounts / lineOfCredit | Non |
> | billingAccounts / patchOperations | Non |
> | billingAccounts / paymentMethods | Non |
> | billingAccounts / products | Non |
> | billingAccounts / transactions | Non |
> | billingPeriods | Non |
> | billingPermissions | Non |
> | billingProperty | Non |
> | billingRoleAssignments | Non |
> | billingRoleDefinitions | Non |
> | createBillingRoleAssignment | Non |
> | departments | Non |
> | enrollmentAccounts | Non |
> | invoices | Non |
> | transfers | Non |
> | transfers / acceptTransfer | Non |
> | transfers / declineTransfer | Non |
> | transfers / operationStatus | Non |
> | transfers / validateTransfer | Non |
> | validateAddress | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mapApis | OUI |
> | updateCommunicationPreference | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | blockchainMembers | OUI |
> | cordaMembers | OUI |
> | watchers | OUI |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | blueprintAssignments | Non |
> | blueprintAssignments / assignmentOperations | Non |
> | blueprintAssignments / operations | Non |
> | blueprints | Non |
> | blueprints / artifacts | Non |
> | blueprints / versions | Non |
> | blueprints / versions / artifacts | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | botServices | OUI |
> | botServices / channels | Non |
> | botServices / connections | Non |
> | languages | Non |
> | modèles | Non |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Redis | OUI |
> | RedisConfigDefinition | Non |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appliedReservations | Non |
> | calculateExchange | Non |
> | calculatePrice | Non |
> | calculatePurchasePrice | Non |
> | catalogs | Non |
> | commercialReservationOrders | Non |
> | change | Non |
> | placePurchaseOrder | Non |
> | reservationOrders | Non |
> | reservationOrders / calculateRefund | Non |
> | reservationOrders / merge | Non |
> | reservationOrders / reservations | Non |
> | reservationOrders / reservations / revisions | Non |
> | reservationOrders / return | Non |
> | reservationOrders / split | Non |
> | reservationOrders / swap | Non |
> | reservations | Non |
> | les ressources | Non |
> | validateReservationOrder | Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Non |
> | CdnWebApplicationFirewallPolicies | OUI |
> | edgenodes | Non |
> | profiles | OUI |
> | profiles / endpoints | OUI |
> | profiles / endpoints / customdomains | Non |
> | profiles / endpoints / origins | Non |
> | validateProbe | Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | certificateOrders | OUI |
> | certificateOrders / certificates | Non |
> | validateCertificateRegistrationInformation | Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | domainNames | OUI |
> | domainNames / capabilities | Non |
> | domainNames / internalLoadBalancers | Non |
> | domainNames / serviceCertificates | Non |
> | domainNames / slots | Non |
> | domainNames / slots / roles | Non |
> | domainNames / slots / roles / metricDefinitions | Non |
> | domainNames / slots / roles / metrics | Non |
> | moveSubscriptionResources | Non |
> | operatingSystemFamilies | Non |
> | operatingSystems | Non |
> | quotas | Non |
> | resourceTypes | Non |
> | validateSubscriptionMoveAvailability | Non |
> | virtualMachines | OUI |
> | virtualMachines / diagnosticSettings | Non |
> | virtualMachines / metricDefinitions | Non |
> | virtualMachines / metrics | Non |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicInfrastructureResources | Non |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | expressRouteCrossConnections | Non |
> | expressRouteCrossConnections / peerings | Non |
> | gatewaySupportedDevices | Non |
> | networkSecurityGroups | OUI |
> | quotas | Non |
> | reservedIps | OUI |
> | virtualNetworks | OUI |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Non |
> | virtualNetworks / virtualNetworkPeerings | Non |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | disks | Non |
> | images | Non |
> | osImages | Non |
> | osPlatformImages | Non |
> | publicImages | Non |
> | quotas | Non |
> | storageAccounts | OUI |
> | storageAccounts / blobServices | Non |
> | storageAccounts / fileServices | Non |
> | storageAccounts / metricDefinitions | Non |
> | storageAccounts / metrics | Non |
> | storageAccounts / queueServices | Non |
> | storageAccounts / services | Non |
> | storageAccounts / services / diagnosticSettings | Non |
> | storageAccounts / services / metricDefinitions | Non |
> | storageAccounts / services / metrics | Non |
> | storageAccounts / tableServices | Non |
> | storageAccounts / vmImages | Non |
> | vmImages | Non |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | RateCard | Non |
> | UsageAggregates | Non |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilitySets | OUI |
> | diskEncryptionSets | OUI |
> | disks | OUI |
> | galleries | OUI |
> | galleries / applications | Non |
> | galleries / applications / versions | Non |
> | galleries / images | Non |
> | galleries / images / versions | Non |
> | hostGroups | OUI |
> | hostGroups / hosts | OUI |
> | images | OUI |
> | proximityPlacementGroups | OUI |
> | restorePointCollections | OUI |
> | restorePointCollections / restorePoints | Non |
> | sharedVMImages | OUI |
> | sharedVMImages / versions | Non |
> | snapshots | OUI |
> | virtualMachines | OUI |
> | virtualMachines / extensions | OUI |
> | virtualMachines / metricDefinitions | Non |
> | virtualMachineScaleSets | OUI |
> | virtualMachineScaleSets / extensions | Non |
> | virtualMachineScaleSets / networkInterfaces | Non |
> | virtualMachineScaleSets / publicIPAddresses | Non |
> | virtualMachineScaleSets / virtualMachines | Non |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Non |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | AggregatedCost | Non |
> | Balances | Non |
> | Budgets | Non |
> | Charges | Non |
> | CostTags | Non |
> | credits | Non |
> | événements | Non |
> | Prévisions | Non |
> | lots | Non |
> | Marketplaces | Non |
> | Pricesheets | Non |
> | products | Non |
> | ReservationDetails | Non |
> | ReservationRecommendations | Non |
> | ReservationSummaries | Non |
> | ReservationTransactions | Non |
> | Balises | Non |
> | tenants | Non |
> | Termes | Non |
> | UsageDetails | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerGroups | OUI |
> | serviceAssociationLinks | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | registries | OUI |
> | registries / builds | Non |
> | registries / builds / cancel | Non |
> | registries / builds / getLogLink | Non |
> | registries / buildTasks | OUI |
> | registries / buildTasks / steps | Non |
> | registries / eventGridFilters | Non |
> | registries / generateCredentials | Non |
> | registries / getBuildSourceUploadUrl | Non |
> | registries / GetCredentials | Non |
> | registries / importImage | Non |
> | registries / queueBuild | Non |
> | registries / regenerateCredential | Non |
> | registries / regenerateCredentials | Non |
> | registries / replications | OUI |
> | registries / runs | Non |
> | registries / runs / cancel | Non |
> | registries / scheduleRun | Non |
> | registries / scopeMaps | Non |
> | registries / taskRuns | OUI |
> | registries / tasks | OUI |
> | registries / tokens | Non |
> | registries / updatePolicies | Non |
> | registries / webhooks | OUI |
> | registries / webhooks / getCallbackConfig | Non |
> | registries / webhooks / ping | Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerServices | OUI |
> | managedclusters | OUI |
> | openShiftManagedClusters | OUI |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Alertes | Non |
> | BillingAccounts | Non |
> | Budgets | Non |
> | CloudConnectors | Non |
> | Connecteurs | OUI |
> | Departments | Non |
> | Dimensions | Non |
> | EnrollmentAccounts | Non |
> | Exports | Non |
> | ExternalBillingAccounts | Non |
> | ExternalBillingAccounts / Alerts | Non |
> | ExternalBillingAccounts / Dimensions | Non |
> | ExternalBillingAccounts / Forecast | Non |
> | ExternalBillingAccounts / Query | Non |
> | ExternalSubscriptions | Non |
> | ExternalSubscriptions / Alerts | Non |
> | ExternalSubscriptions / Dimensions | Non |
> | ExternalSubscriptions / Forecast | Non |
> | ExternalSubscriptions / Query | Non |
> | Forecast | Non |
> | Requête | Non |
> | inscription | Non |
> | Reportconfigs | Non |
> | Rapports | Non |
> | Paramètres | Non |
> | showbackRules | Non |
> | Les vues | Non |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | requêtes | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | associations | Non |
> | resourceProviders | OUI |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | OUI |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | OUI |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaces | OUI |
> | workspaces / virtualNetworkPeerings | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | catalogs | OUI |
> | datacatalogs | OUI |
> | datacatalogs / datasources | Non |
> | datacatalogs / datasources / scans | Non |
> | datacatalogs / datasources / scans / datasets | Non |
> | datacatalogs / datasources / scans / triggers | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dataFactories | OUI |
> | dataFactories / diagnosticSettings | Non |
> | dataFactories / metricDefinitions | Non |
> | dataFactorySchema | Non |
> | factories | OUI |
> | factories / integrationRuntimes | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |
> | accounts / dataLakeStoreAccounts | Non |
> | accounts / storageAccounts | Non |
> | accounts / storageAccounts / containers | Non |
> | accounts / transferAnalyticsUnits | Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |
> | accounts / eventGridFilters | Non |
> | accounts / firewallRules | Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | OUI |
> | services / projects | OUI |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |
> | accounts / shares | Non |
> | accounts / shares / datasets | Non |
> | accounts / shares / invitations | Non |
> | accounts / shares / providersharesubscriptions | Non |
> | accounts / shares / synchronizationSettings | Non |
> | accounts / sharesubscriptions | Non |
> | accounts / sharesubscriptions / consumerSourceDataSets | Non |
> | accounts / sharesubscriptions / datasetmappings | Non |
> | accounts / sharesubscriptions / triggers | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI |
> | servers / advisors | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / topQueryStatistics | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI |
> | servers / advisors | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / topQueryStatistics | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | serverGroups | OUI |
> | servers | OUI |
> | servers / advisors | Non |
> | servers / keys | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / topQueryStatistics | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |
> | serversv2 | OUI |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | artifactSources | OUI |
> | rollouts | OUI |
> | serviceTopologies | OUI |
> | serviceTopologies / services | OUI |
> | serviceTopologies / services / serviceUnits | OUI |
> | steps | OUI |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationgroups | OUI |
> | applicationgroups / applications | Non |
> | applicationgroups / desktops | Non |
> | applicationgroups / startmenuitems | Non |
> | hostpools | OUI |
> | hostpools / sessionhosts | Non |
> | hostpools / sessionhosts / usersessions | Non |
> | hostpools / usersessions | Non |
> | workspaces | OUI |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | ElasticPools | OUI |
> | ElasticPools / IotHubTenants | OUI |
> | IoTHubs | OUI |
> | IotHubs / eventGridFilters | Non |
> | ProvisioningServices | OUI |
> | usages | Non |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | pipelines | OUI |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | controllers | OUI |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labcenters | OUI |
> | labs | OUI |
> | labs / environments | OUI |
> | labs / serviceRunners | OUI |
> | labs / virtualMachines | OUI |
> | schedules | OUI |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | databaseAccountNames | Non |
> | databaseAccounts | OUI |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | OUI |
> | domains / domainOwnershipIdentifiers | Non |
> | generateSsoRequest | Non |
> | topLevelDomains | Non |
> | validateDomainRegistrationInformation | Non |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | lcsprojects | Non |
> | lcsprojects / clouddeployments | Non |
> | lcsprojects / connectors | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | OUI |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | OUI |
> | domains / topics | Non |
> | eventSubscriptions | Non |
> | extensionTopics | Non |
> | topics | OUI |
> | topicTypes | Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI |
> | namespaces | OUI |
> | namespaces / authorizationrules | Non |
> | namespaces / disasterrecoveryconfigs | Non |
> | namespaces / eventhubs | Non |
> | namespaces / eventhubs / authorizationrules | Non |
> | namespaces / eventhubs / consumergroups | Non |
> | namespaces / networkrulesets | Non |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | features | Non |
> | fournisseurs | Non |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | enroll | Non |
> | galleryitems | Non |
> | generateartifactaccessuri | Non |
> | myareas | Non |
> | myareas / areas | Non |
> | myareas / areas / areas | Non |
> | myareas / areas / areas / galleryitems | Non |
> | myareas / areas / galleryitems | Non |
> | myareas / galleryitems | Non |
> | inscription | Non |
> | les ressources | Non |
> | retrieveresourcesbyid | Non |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurationProfileAssignments | Non |
> | guestConfigurationAssignments | Non |
> | software | Non |
> | softwareUpdateProfile | Non |
> | softwareUpdates | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hanaInstances | OUI |
> | sapMonitors | OUI |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dedicatedHSMs | OUI |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI |
> | clusters / applications | Non |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | OUI |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | machines | OUI |
> | machines / extensions | OUI |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dataManagers | OUI |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | components | OUI |
> | networkScopes | OUI |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | OUI |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appTemplates | Non |
> | IoTApps | OUI |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Graph | OUI |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deletedVaults | Non |
> | hsmPools | OUI |
> | vaults | OUI |
> | vaults / accessPolicies | Non |
> | vaults / eventGridFilters | Non |
> | vaults / secrets | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI |
> | clusters / attacheddatabaseconfigurations | Non |
> | clusters / databases | Non |
> | clusters / databases / dataconnections | Non |
> | clusters / databases / eventhubconnections | Non |
> | clusters / sharedidentities | Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labaccounts | OUI |
> | users | Non |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hostingEnvironments | OUI |
> | integrationAccounts | OUI |
> | integrationServiceEnvironments | OUI |
> | integrationServiceEnvironments / managedApis | OUI |
> | isolatedEnvironments | OUI |
> | workflows | OUI |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | commitmentPlans | OUI |
> | webServices | OUI |
> | Workspaces | OUI |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaces | OUI |
> | workspaces / computes | Non |
> | workspaces / eventGridFilters | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Identities | Non |
> | userAssignedIdentities | OUI |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Non |
> | registrationAssignments | Non |
> | registrationDefinitions | Non |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | getEntities | Non |
> | managementGroups | Non |
> | les ressources | Non |
> | startTenantBackfill | Non |
> | tenantBackfillStatus | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI |
> | accounts / eventGridFilters | Non |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | offers | Non |
> | offerTypes | Non |
> | offerTypes / publishers | Non |
> | offerTypes / publishers / offers | Non |
> | offerTypes / publishers / offers / plans | Non |
> | offerTypes / publishers / offers / plans / agreements | Non |
> | offerTypes / publishers / offers / plans / configs | Non |
> | offerTypes / publishers / offers / plans / configs / importImage | Non |
> | privategalleryitems | Non |
> | products | Non |
> | publishers | Non |
> | publishers / offers | Non |
> | publishers / offers / amendments | Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicDevServices | OUI |
> | updateCommunicationPreference | Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | agreements | Non |
> | offertypes | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mediaservices | OUI |
> | mediaservices / accountFilters | Non |
> | mediaservices / assets | Non |
> | mediaservices / assets / assetFilters | Non |
> | mediaservices / contentKeyPolicies | Non |
> | mediaservices / eventGridFilters | Non |
> | mediaservices / liveEventOperations | Non |
> | mediaservices / liveEvents | OUI |
> | mediaservices / liveEvents / liveOutputs | Non |
> | mediaservices / liveOutputOperations | Non |
> | mediaservices / mediaGraphs | Non |
> | mediaservices / streamingEndpointOperations | Non |
> | mediaservices / streamingEndpoints | OUI |
> | mediaservices / streamingLocators | Non |
> | mediaservices / streamingPolicies | Non |
> | mediaservices / transforms | Non |
> | mediaservices / transforms / jobs | Non |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appClusters | OUI |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | assessmentProjects | OUI |
> | migrateprojects | OUI |
> | projects | OUI |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | OUI |
> | objectUnderstandingAccounts | OUI |
> | remoteRenderingAccounts | OUI |
> | spatialAnchorsAccounts | OUI |
> | surfaceReconstructionAccounts | OUI |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | netAppAccounts | OUI |
> | netAppAccounts / capacityPools | OUI |
> | netAppAccounts / capacityPools / volumes | OUI |
> | netAppAccounts / capacityPools / volumes / mountTargets | OUI |
> | netAppAccounts / capacityPools / volumes / snapshots | OUI |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationGateways | OUI |
> | applicationGatewayWebApplicationFirewallPolicies | OUI |
> | applicationSecurityGroups | OUI |
> | azureFirewallFqdnTags | Non |
> | azureFirewalls | OUI |
> | bastionHosts | OUI |
> | bgpServiceCommunities | Non |
> | connections | OUI |
> | ddosCustomPolicies | OUI |
> | ddosProtectionPlans | OUI |
> | dnsOperationStatuses | Non |
> | dnszones | OUI |
> | dnszones / A | Non |
> | dnszones / AAAA | Non |
> | dnszones / all | Non |
> | dnszones / CAA | Non |
> | dnszones / CNAME | Non |
> | dnszones / MX | Non |
> | dnszones / NS | Non |
> | dnszones / PTR | Non |
> | dnszones / recordsets | Non |
> | dnszones / SOA | Non |
> | dnszones / SRV | Non |
> | dnszones / TXT | Non |
> | expressRouteCircuits | OUI |
> | expressRouteCrossConnections | OUI |
> | expressRouteGateways | OUI |
> | expressRoutePorts | OUI |
> | expressRouteServiceProviders | Non |
> | firewallPolicies | OUI |
> | frontdoors | OUI |
> | frontdoorWebApplicationFirewallManagedRuleSets | Non |
> | frontdoorWebApplicationFirewallPolicies | OUI |
> | getDnsResourceReference | Non |
> | internalNotify | Non |
> | loadBalancers | OUI |
> | localNetworkGateways | OUI |
> | natGateways | OUI |
> | networkIntentPolicies | OUI |
> | networkInterfaces | OUI |
> | networkProfiles | OUI |
> | networkSecurityGroups | OUI |
> | networkWatchers | OUI |
> | networkWatchers / connectionMonitors | OUI |
> | networkWatchers / lenses | OUI |
> | networkWatchers / pingMeshes | OUI |
> | p2sVpnGateways | OUI |
> | privateDnsOperationStatuses | Non |
> | privateDnsZones | OUI |
> | privateDnsZones / A | Non |
> | privateDnsZones / AAAA | Non |
> | privateDnsZones / all | Non |
> | privateDnsZones / CNAME | Non |
> | privateDnsZones / MX | Non |
> | privateDnsZones / PTR | Non |
> | privateDnsZones / SOA | Non |
> | privateDnsZones / SRV | Non |
> | privateDnsZones / TXT | Non |
> | privateDnsZones / virtualNetworkLinks | OUI |
> | privateEndpoints | OUI |
> | privateLinkServices | OUI |
> | publicIPAddresses | OUI |
> | publicIPPrefixes | OUI |
> | routeFilters | OUI |
> | routeTables | OUI |
> | serviceEndpointPolicies | OUI |
> | trafficManagerGeographicHierarchies | Non |
> | trafficmanagerprofiles | OUI |
> | trafficmanagerprofiles / heatMaps | Non |
> | trafficManagerUserMetricsKeys | Non |
> | virtualHubs | OUI |
> | virtualNetworkGateways | OUI |
> | virtualNetworks | OUI |
> | virtualNetworkTaps | OUI |
> | virtualWans | OUI |
> | vpnGateways | OUI |
> | vpnSites | OUI |
> | webApplicationFirewallPolicies | OUI |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI |
> | namespaces / notificationHubs | OUI |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | osNamespaces | OUI |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | HyperVSites | OUI |
> | ImportSites | OUI |
> | ServerSites | OUI |
> | VMwareSites | OUI |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI |
> | périphériques | Non |
> | linkTargets | Non |
> | storageInsightConfigs | Non |
> | workspaces | OUI |
> | workspaces / dataSources | Non |
> | workspaces / linkedServices | Non |
> | workspaces / query | Non |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managementassociations | Non |
> | managementconfigurations | OUI |
> | solutions | OUI |
> | views | OUI |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | legacyPeerings | Non |
> | peerAsns | Non |
> | peerings | OUI |
> | peeringServiceProviders | Non |
> | peeringServices | OUI |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | policyEvents | Non |
> | policyMetadata | Non |
> | policyStates | Non |
> | policyTrackedResources | Non |
> | remediations | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | consoles | Non |
> | dashboards | OUI |
> | userSettings | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaceCollections | OUI |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capacities | OUI |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | backupProtectedItems | Non |
> | vaults | OUI |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI |
> | namespaces / authorizationrules | Non |
> | namespaces / hybridconnections | Non |
> | namespaces / hybridconnections / authorizationrules | Non |
> | namespaces / wcfrelays | Non |
> | namespaces / wcfrelays / authorizationrules | Non |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Non |
> | collections | OUI |
> | collections / applications | Non |
> | collections / securityprincipals | Non |
> | templateImages | Non |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | queries | OUI |
> | resourceChangeDetails | Non |
> | resourceChanges | Non |
> | les ressources | Non |
> | resourcesHistory | Non |
> | subscriptionsStatus | Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilityStatuses | Non |
> | childAvailabilityStatuses | Non |
> | childResources | Non |
> | événements | Non |
> | impactedResources | Non |
> | metadata | Non |
> | Notifications | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deployments | Non |
> | deployments / operations | Non |
> | deploymentScripts | OUI |
> | deploymentScripts / logs | Non |
> | links | Non |
> | notifyResourceJobs | Non |
> | fournisseurs | Non |
> | resourceGroups | Non |
> | les ressources | Non |
> | subscriptions | Non |
> | subscriptions / providers | Non |
> | subscriptions / resources | Non |
> | subscriptions / tagnames | Non |
> | subscriptions / tagNames / tagValues | Non |
> | tenants | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI |
> | saasresources | Non |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobcollections | OUI |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | resourceHealthMetadata | Non |
> | searchServices | OUI |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Non |
> | advancedThreatProtectionSettings | Non |
> | alertes | Non |
> | allowedConnections | Non |
> | applicationWhitelistings | Non |
> | assessmentMetadata | Non |
> | assessments | Non |
> | autoDismissAlertsRules | Non |
> | automations | OUI |
> | AutoProvisioningSettings | Non |
> | Compliances | Non |
> | dataCollectionAgents | Non |
> | deviceSecurityGroups | Non |
> | discoveredSecuritySolutions | Non |
> | externalSecuritySolutions | Non |
> | InformationProtectionPolicies | Non |
> | iotSecuritySolutions | OUI |
> | iotSecuritySolutions / analyticsModels | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Non |
> | jitNetworkAccessPolicies | Non |
> | networkData | Non |
> | stratégies | Non |
> | pricings | Non |
> | regulatoryComplianceStandards | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Non |
> | securityContacts | Non |
> | securitySolutions | Non |
> | securitySolutionsReferenceData | Non |
> | securityStatuses | Non |
> | securityStatusesSummaries | Non |
> | serverVulnerabilityAssessments | Non |
> | paramètres | Non |
> | subAssessments | Non |
> | tâches | Non |
> | topologies | Non |
> | workspaceSettings | Non |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | agrégations | Non |
> | alertRules | Non |
> | alertRuleTemplates | Non |
> | bookmarks | Non |
> | cas | Non |
> | dataConnectors | Non |
> | entities | Non |
> | entityQueries | Non |
> | officeConsents | Non |
> | paramètres | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI |
> | namespaces / authorizationrules | Non |
> | namespaces / disasterrecoveryconfigs | Non |
> | namespaces / eventgridfilters | Non |
> | namespaces / networkrulesets | Non |
> | namespaces / queues | Non |
> | namespaces / queues / authorizationrules | Non |
> | namespaces / topics | Non |
> | namespaces / topics / authorizationrules | Non |
> | namespaces / topics / subscriptions | Non |
> | namespaces / topics / subscriptions / rules | Non |
> | premiumMessagingRegions | Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI |
> | clusters | OUI |
> | clusters / applications | Non |
> | containerGroups | OUI |
> | containerGroupSets | OUI |
> | edgeclusters | OUI |
> | edgeclusters / applications | Non |
> | networks | OUI |
> | secretstores | OUI |
> | secretstores / certificates | Non |
> | secretstores / secrets | Non |
> | volumes | OUI |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI |
> | containerGroups | OUI |
> | gateways | OUI |
> | networks | OUI |
> | secrets | OUI |
> | volumes | OUI |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | providerRegistrations | Non |
> | providerRegistrations / resourceTypeRegistrations | Non |
> | rollouts | OUI |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SignalR | OUI |
> | SignalR / eventGridFilters | Non |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SiteRecoveryVault | OUI |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hybridUseBenefits | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationDefinitions | OUI |
> | applications | OUI |
> | jitRequests | OUI |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managedInstances | OUI |
> | managedInstances / databases | OUI |
> | managedInstances / databases / backupShortTermRetentionPolicies | Non |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Non |
> | managedInstances / databases / vulnerabilityAssessments | Non |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Non |
> | managedInstances / encryptionProtector | Non |
> | managedInstances / keys | Non |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Non |
> | managedInstances / vulnerabilityAssessments | Non |
> | servers | OUI |
> | servers / administrators | Non |
> | servers / communicationLinks | Non |
> | servers / databases | OUI |
> | servers / encryptionProtector | Non |
> | servers / firewallRules | Non |
> | servers / keys | Non |
> | servers / restorableDroppedDatabases | Non |
> | servers / serviceobjectives | Non |
> | servers / tdeCertificates | Non |
> | virtualClusters | Non |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | OUI |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Non |
> | SqlVirtualMachines | OUI |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageAccounts | OUI |
> | storageAccounts / blobServices | Non |
> | storageAccounts / fileServices | Non |
> | storageAccounts / queueServices | Non |
> | storageAccounts / services | Non |
> | storageAccounts / services / metricDefinitions | Non |
> | storageAccounts / tableServices | Non |
> | usages | Non |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | caches | OUI |
> | caches / storageTargets | Non |
> | usageModels | Non |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | replicationGroups | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageSyncServices | OUI |
> | storageSyncServices / registeredServers | Non |
> | storageSyncServices / syncGroups | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non |
> | storageSyncServices / workflows | Non |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageSyncServices | OUI |
> | storageSyncServices / registeredServers | Non |
> | storageSyncServices / syncGroups | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non |
> | storageSyncServices / workflows | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageSyncServices | OUI |
> | storageSyncServices / registeredServers | Non |
> | storageSyncServices / syncGroups | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non |
> | storageSyncServices / workflows | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managers | OUI |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | streamingjobs | OUI |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | annuler | Non |
> | CreateSubscription | Non |
> | enable | Non |
> | renommer | Non |
> | SubscriptionDefinitions | Non |
> | SubscriptionOperations | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | OUI |
> | environments / accessPolicies | Non |
> | environments / eventsources | OUI |
> | environments / referenceDataSets | OUI |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dedicatedCloudNodes | OUI |
> | dedicatedCloudServices | OUI |
> | virtualMachines | OUI |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | apiManagementAccounts | Non |
> | apiManagementAccounts / apiAcls | Non |
> | apiManagementAccounts / apis | Non |
> | apiManagementAccounts / apis / apiAcls | Non |
> | apiManagementAccounts / apis / connectionAcls | Non |
> | apiManagementAccounts / apis / connections | Non |
> | apiManagementAccounts / apis / connections / connectionAcls | Non |
> | apiManagementAccounts / apis / localizedDefinitions | Non |
> | apiManagementAccounts / connectionAcls | Non |
> | apiManagementAccounts / connections | Non |
> | billingMeters | Non |
> | certificates | OUI |
> | connectionGateways | OUI |
> | connections | OUI |
> | customApis | OUI |
> | deletedSites | Non |
> | functions | Non |
> | hostingEnvironments | OUI |
> | hostingEnvironments / multiRolePools | Non |
> | hostingEnvironments / workerPools | Non |
> | publishingUsers | Non |
> | de films | Non |
> | resourceHealthMetadata | Non |
> | runtimes | Non |
> | serverFarms | OUI |
> | serverFarms / eventGridFilters | Non |
> | sites | OUI |
> | sites/config  | Non |
> | sites / eventGridFilters | Non |
> | sites / hostNameBindings | Non |
> | sites / networkConfig | Non |
> | sites / premieraddons | OUI |
> | sites / slots | OUI |
> | sites / slots / eventGridFilters | Non |
> | sites / slots / hostNameBindings | Non |
> | sites / slots / networkConfig | Non |
> | sourceControls | Non |
> | validate | Non |
> | verifyHostingEnvironmentVnet | Non |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DeviceServices | OUI |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | components | Non |
> | componentsSummary | Non |
> | monitorInstances | Non |
> | monitorInstancesSummary | Non |
> | monitors | Non |
> | notificationSettings | Non |

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

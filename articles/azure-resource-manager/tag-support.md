---
title: Prise en charge des étiquettes pour les ressources
description: Indique les types de ressources Azure qui prennent en charge les étiquettes. Fournit des détails pour tous les services Azure.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422140"
---
# <a name="tag-support-for-azure-resources"></a>Prise en charge des étiquettes pour les ressources Azure
Cet article indique si un type de ressource prend en charge les [étiquettes](resource-group-using-tags.md). La colonne intitulée **Prend en charge les balises** indique si le type de ressource a une propriété pour la balise. La colonne intitulée **Balise dans le rapport des coûts** indique si ce type de ressource transmet la balise au rapport des coûts. Vous pouvez afficher les coûts à l’aide d’étiquettes dans l’[analyse Azure Cost Management](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) et les [données de facturation et d’utilisation quotidienne Azure](../billing/billing-download-azure-invoice-daily-usage-date.md).

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [tag-tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | DomainServices | OUI | OUI |
> | DomainServices / oucontainer | Non | Non |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | supportProviders | Non | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Non | Non |
> | addsservices | Non | Non |
> | agents | Non | Non |
> | anonymousapiusers | Non | Non |
> | configuration | Non | Non |
> | logs | Non | Non |
> | reports | Non | Non |
> | servicehealthmetrics | Non | Non |
> | services | Non | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | configurations | Non | Non |
> | generateRecommendations | Non | Non |
> | metadata | Non | Non |
> | de films | Non | Non |
> | suppressions | Non | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | actionRules | OUI | OUI |
> | alertes | Non | Non |
> | alertsList | Non | Non |
> | alertsMetaData | Non | Non |
> | alertsSummary | Non | Non |
> | alertsSummaryList | Non | Non |
> | feedback | Non | Non |
> | smartDetectorAlertRules | OUI | OUI |
> | smartDetectorRuntimeEnvironments | Non | Non |
> | smartGroups | Non | Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | servers | OUI | OUI |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Non | Non |
> | service | OUI | OUI |
> | validateServiceName | Non | Non |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | configurationStores | OUI | OUI |
> | configurationStores / eventGridFilters | Non | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | Spring | OUI | OUI |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Non | Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Non | Non |
> | dataAliases | Non | Non |
> | denyAssignments | Non | Non |
> | elevateAccess | Non | Non |
> | findOrphanRoleAssignments | Non | Non |
> | locks | Non | Non |
> | Autorisations | Non | Non |
> | policyAssignments | Non | Non |
> | policyDefinitions | Non | Non |
> | policySetDefinitions | Non | Non |
> | providerOperations | Non | Non |
> | roleAssignments | Non | Non |
> | roleDefinitions | Non | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | automationAccounts | OUI | OUI |
> | automationAccounts / configurations | OUI | OUI |
> | automationAccounts / jobs | Non | Non |
> | automationAccounts / runbooks | OUI | OUI |
> | automationAccounts / softwareUpdateConfigurations | Non | Non |
> | automationAccounts / webhooks | Non | Non |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | configurationStores | OUI | OUI |
> | configurationStores / eventGridFilters | Non | Non |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | environments | Non | Non |
> | environments / accounts | Non | Non |
> | environments / accounts / namespaces | Non | Non |
> | environments / accounts / namespaces / configurations | Non | Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | OUI | Non |
> | b2ctenants | Non | Non |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | OUI | OUI |
> | postgresInstances | OUI | OUI |
> | sqlBigDataClusters | OUI | OUI |
> | sqlInstances | OUI | OUI |
> | sqlServerRegistrations | OUI | OUI |
> | sqlServerRegistrations / sqlServers | Non | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | registrations | OUI | OUI |
> | registrations / customerSubscriptions | Non | Non |
> | registrations / products | Non | Non |
> | verificationKeys | Non | Non |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | batchAccounts | OUI | OUI |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Non | Non |
> | billingAccounts / agreements | Non | Non |
> | billingAccounts / billingPermissions | Non | Non |
> | billingAccounts / billingProfiles | Non | Non |
> | billingAccounts / billingProfiles / billingPermissions | Non | Non |
> | billingAccounts / billingProfiles / billingRoleAssignments | Non | Non |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Non | Non |
> | billingAccounts / billingProfiles / billingSubscriptions | Non | Non |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Non | Non |
> | billingAccounts / billingProfiles / customers | Non | Non |
> | billingAccounts / billingProfiles / invoices | Non | Non |
> | billingAccounts / billingProfiles / invoices / pricesheet | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / products | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Non | Non |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Non | Non |
> | billingAccounts / BillingProfiles / patchOperations | Non | Non |
> | billingAccounts / billingProfiles / paymentMethods | Non | Non |
> | billingAccounts / billingProfiles / policies | Non | Non |
> | billingAccounts / billingProfiles / pricesheet | Non | Non |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Non | Non |
> | billingAccounts / billingProfiles / products | Non | Non |
> | billingAccounts / billingProfiles / transactions | Non | Non |
> | billingAccounts / billingRoleAssignments | Non | Non |
> | billingAccounts / billingRoleDefinitions | Non | Non |
> | billingAccounts / billingSubscriptions | Non | Non |
> | billingAccounts / billingSubscriptions / invoices | Non | Non |
> | billingAccounts / createBillingRoleAssignment | Non | Non |
> | billingAccounts / createInvoiceSectionOperations | Non | Non |
> | billingAccounts / customers | Non | Non |
> | billingAccounts / customers / billingPermissions | Non | Non |
> | billingAccounts / customers / billingSubscriptions | Non | Non |
> | billingAccounts / customers / initiateTransfer | Non | Non |
> | billingAccounts / customers / policies | Non | Non |
> | billingAccounts / customers / products | Non | Non |
> | billingAccounts / customers / transactions | Non | Non |
> | billingAccounts / customers / transfers | Non | Non |
> | billingAccounts / departments | Non | Non |
> | billingAccounts / enrollmentAccounts | Non | Non |
> | billingAccounts / invoices | Non | Non |
> | billingAccounts / invoiceSections | Non | Non |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Non | Non |
> | billingAccounts / invoiceSections / billingSubscriptions | Non | Non |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Non | Non |
> | billingAccounts / invoiceSections / elevate | Non | Non |
> | billingAccounts / invoiceSections / initiateTransfer | Non | Non |
> | billingAccounts / invoiceSections / patchOperations | Non | Non |
> | billingAccounts / invoiceSections / productMoveOperations | Non | Non |
> | billingAccounts / invoiceSections / products | Non | Non |
> | billingAccounts / invoiceSections / products / transfer | Non | Non |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Non | Non |
> | billingAccounts / invoiceSections / transactions | Non | Non |
> | billingAccounts / invoiceSections / transfers | Non | Non |
> | billingAccounts / lineOfCredit | Non | Non |
> | billingAccounts / patchOperations | Non | Non |
> | billingAccounts / paymentMethods | Non | Non |
> | billingAccounts / products | Non | Non |
> | billingAccounts / transactions | Non | Non |
> | billingPeriods | Non | Non |
> | billingPermissions | Non | Non |
> | billingProperty | Non | Non |
> | billingRoleAssignments | Non | Non |
> | billingRoleDefinitions | Non | Non |
> | createBillingRoleAssignment | Non | Non |
> | departments | Non | Non |
> | enrollmentAccounts | Non | Non |
> | invoices | Non | Non |
> | transfers | Non | Non |
> | transfers / acceptTransfer | Non | Non |
> | transfers / declineTransfer | Non | Non |
> | transfers / operationStatus | Non | Non |
> | transfers / validateTransfer | Non | Non |
> | validateAddress | Non | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | mapApis | OUI | OUI |
> | updateCommunicationPreference | Non | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | OUI | OUI |
> | cordaMembers | OUI | OUI |
> | watchers | OUI | OUI |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Non | Non |
> | blueprintAssignments / assignmentOperations | Non | Non |
> | blueprintAssignments / operations | Non | Non |
> | blueprints | Non | Non |
> | blueprints / artifacts | Non | Non |
> | blueprints / versions | Non | Non |
> | blueprints / versions / artifacts | Non | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | botServices | OUI | OUI |
> | botServices / channels | Non | Non |
> | botServices / connections | Non | Non |
> | languages | Non | Non |
> | modèles | Non | Non |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | Redis | OUI | OUI |
> | RedisConfigDefinition | Non | Non |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Non | Non |
> | calculateExchange | Non | Non |
> | calculatePrice | Non | Non |
> | calculatePurchasePrice | Non | Non |
> | catalogs | Non | Non |
> | commercialReservationOrders | Non | Non |
> | change | Non | Non |
> | placePurchaseOrder | Non | Non |
> | reservationOrders | Non | Non |
> | reservationOrders / calculateRefund | Non | Non |
> | reservationOrders / merge | Non | Non |
> | reservationOrders / reservations | Non | Non |
> | reservationOrders / reservations / revisions | Non | Non |
> | reservationOrders / return | Non | Non |
> | reservationOrders / split | Non | Non |
> | reservationOrders / swap | Non | Non |
> | reservations | Non | Non |
> | les ressources | Non | Non |
> | validateReservationOrder | Non | Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Non | Non |
> | CdnWebApplicationFirewallPolicies | OUI | OUI |
> | edgenodes | Non | Non |
> | profiles | OUI | OUI |
> | profiles / endpoints | OUI | OUI |
> | profiles / endpoints / customdomains | Non | Non |
> | profiles / endpoints / origins | Non | Non |
> | validateProbe | Non | Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | certificateOrders | OUI | OUI |
> | certificateOrders / certificates | Non | Non |
> | validateCertificateRegistrationInformation | Non | Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | capabilities | Non | Non |
> | domainNames | Non | Non |
> | domainNames / capabilities | Non | Non |
> | domainNames / internalLoadBalancers | Non | Non |
> | domainNames / serviceCertificates | Non | Non |
> | domainNames / slots | Non | Non |
> | domainNames / slots / roles | Non | Non |
> | domainNames / slots / roles / metricDefinitions | Non | Non |
> | domainNames / slots / roles / metrics | Non | Non |
> | moveSubscriptionResources | Non | Non |
> | operatingSystemFamilies | Non | Non |
> | operatingSystems | Non | Non |
> | quotas | Non | Non |
> | resourceTypes | Non | Non |
> | validateSubscriptionMoveAvailability | Non | Non |
> | virtualMachines | Non | Non |
> | virtualMachines / diagnosticSettings | Non | Non |
> | virtualMachines / metricDefinitions | Non | Non |
> | virtualMachines / metrics | Non | Non |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Non | Non |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | capabilities | Non | Non |
> | expressRouteCrossConnections | Non | Non |
> | expressRouteCrossConnections / peerings | Non | Non |
> | gatewaySupportedDevices | Non | Non |
> | networkSecurityGroups | Non | Non |
> | quotas | Non | Non |
> | reservedIps | Non | Non |
> | virtualNetworks | Non | Non |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Non | Non |
> | virtualNetworks / virtualNetworkPeerings | Non | Non |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | capabilities | Non | Non |
> | disks | Non | Non |
> | images | Non | Non |
> | osImages | Non | Non |
> | osPlatformImages | Non | Non |
> | publicImages | Non | Non |
> | quotas | Non | Non |
> | storageAccounts | Non | Non |
> | storageAccounts / blobServices | Non | Non |
> | storageAccounts / fileServices | Non | Non |
> | storageAccounts / metricDefinitions | Non | Non |
> | storageAccounts / metrics | Non | Non |
> | storageAccounts / queueServices | Non | Non |
> | storageAccounts / services | Non | Non |
> | storageAccounts / services / diagnosticSettings | Non | Non |
> | storageAccounts / services / metricDefinitions | Non | Non |
> | storageAccounts / services / metrics | Non | Non |
> | storageAccounts / tableServices | Non | Non |
> | storageAccounts / vmImages | Non | Non |
> | vmImages | Non | Non |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | RateCard | Non | Non |
> | UsageAggregates | Non | Non |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | availabilitySets | OUI | OUI |
> | diskEncryptionSets | OUI | OUI |
> | disks | OUI | OUI |
> | galleries | OUI | OUI |
> | galleries / applications | Non | Non |
> | galleries / applications / versions | Non | Non |
> | galleries / images | Non | Non |
> | galleries / images / versions | Non | Non |
> | hostGroups | OUI | OUI |
> | hostGroups / hosts | OUI | OUI |
> | images | OUI | OUI |
> | proximityPlacementGroups | OUI | OUI |
> | restorePointCollections | OUI | OUI |
> | restorePointCollections / restorePoints | Non | Non |
> | sharedVMImages | OUI | OUI |
> | sharedVMImages / versions | Non | Non |
> | snapshots | OUI | OUI |
> | virtualMachines | OUI | OUI |
> | virtualMachines / extensions | OUI | OUI |
> | virtualMachines / metricDefinitions | Non | Non |
> | virtualMachineScaleSets | OUI | OUI |
> | virtualMachineScaleSets / extensions | Non | Non |
> | virtualMachineScaleSets / networkInterfaces | Non | Non |
> | virtualMachineScaleSets / publicIPAddresses | Non | Non |
> | virtualMachineScaleSets / virtualMachines | Non | Non |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Non | Non |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Non | Non |
> | Balances | Non | Non |
> | Budgets | Non | Non |
> | Charges | Non | Non |
> | CostTags | Non | Non |
> | credits | Non | Non |
> | événements | Non | Non |
> | Prévisions | Non | Non |
> | lots | Non | Non |
> | Marketplaces | Non | Non |
> | Pricesheets | Non | Non |
> | products | Non | Non |
> | ReservationDetails | Non | Non |
> | ReservationRecommendations | Non | Non |
> | ReservationSummaries | Non | Non |
> | ReservationTransactions | Non | Non |
> | Balises | Non | Non |
> | tenants | Non | Non |
> | Termes | Non | Non |
> | UsageDetails | Non | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | containerGroups | OUI | OUI |
> | serviceAssociationLinks | Non | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | registries | OUI | OUI |
> | registries / builds | Non | Non |
> | registries / builds / cancel | Non | Non |
> | registries / builds / getLogLink | Non | Non |
> | registries / buildTasks | OUI | OUI |
> | registries / buildTasks / steps | Non | Non |
> | registries / eventGridFilters | Non | Non |
> | registries / generateCredentials | Non | Non |
> | registries / getBuildSourceUploadUrl | Non | Non |
> | registries / GetCredentials | Non | Non |
> | registries / importImage | Non | Non |
> | registries / queueBuild | Non | Non |
> | registries / regenerateCredential | Non | Non |
> | registries / regenerateCredentials | Non | Non |
> | registries / replications | OUI | OUI |
> | registries / runs | Non | Non |
> | registries / runs / cancel | Non | Non |
> | registries / scheduleRun | Non | Non |
> | registries / scopeMaps | Non | Non |
> | registries / taskRuns | OUI | OUI |
> | registries / tasks | OUI | OUI |
> | registries / tokens | Non | Non |
> | registries / updatePolicies | Non | Non |
> | registries / webhooks | OUI | OUI |
> | registries / webhooks / getCallbackConfig | Non | Non |
> | registries / webhooks / ping | Non | Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | containerServices | OUI | OUI |
> | managedclusters | OUI | OUI |
> | openShiftManagedClusters | OUI | OUI |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | Alertes | Non | Non |
> | BillingAccounts | Non | Non |
> | Budgets | Non | Non |
> | CloudConnectors | Non | Non |
> | Connecteurs | OUI | OUI |
> | Departments | Non | Non |
> | Dimensions | Non | Non |
> | EnrollmentAccounts | Non | Non |
> | Exports | Non | Non |
> | ExternalBillingAccounts | Non | Non |
> | ExternalBillingAccounts / Alerts | Non | Non |
> | ExternalBillingAccounts / Dimensions | Non | Non |
> | ExternalBillingAccounts / Forecast | Non | Non |
> | ExternalBillingAccounts / Query | Non | Non |
> | ExternalSubscriptions | Non | Non |
> | ExternalSubscriptions / Alerts | Non | Non |
> | ExternalSubscriptions / Dimensions | Non | Non |
> | ExternalSubscriptions / Forecast | Non | Non |
> | ExternalSubscriptions / Query | Non | Non |
> | Forecast | Non | Non |
> | Requête | Non | Non |
> | inscription | Non | Non |
> | Reportconfigs | Non | Non |
> | Rapports | Non | Non |
> | Paramètres | Non | Non |
> | showbackRules | Non | Non |
> | Les vues | Non | Non |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | requêtes | Non | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | associations | Non | Non |
> | resourceProviders | OUI | OUI |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | jobs | OUI | OUI |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | OUI | OUI |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | workspaces | OUI | Non |
> | workspaces / virtualNetworkPeerings | Non | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | catalogs | OUI | OUI |
> | datacatalogs | OUI | OUI |
> | datacatalogs / datasources | Non | Non |
> | datacatalogs / datasources / scans | Non | Non |
> | datacatalogs / datasources / scans / datasets | Non | Non |
> | datacatalogs / datasources / scans / triggers | Non | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | dataFactories | OUI | Non |
> | dataFactories / diagnosticSettings | Non | Non |
> | dataFactories / metricDefinitions | Non | Non |
> | dataFactorySchema | Non | Non |
> | factories | OUI | Non |
> | factories / integrationRuntimes | Non | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |
> | accounts / dataLakeStoreAccounts | Non | Non |
> | accounts / storageAccounts | Non | Non |
> | accounts / storageAccounts / containers | Non | Non |
> | accounts / transferAnalyticsUnits | Non | Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |
> | accounts / eventGridFilters | Non | Non |
> | accounts / firewallRules | Non | Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | services | Non | Non |
> | services / projects | Non | Non |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |
> | accounts / shares | Non | Non |
> | accounts / shares / datasets | Non | Non |
> | accounts / shares / invitations | Non | Non |
> | accounts / shares / providersharesubscriptions | Non | Non |
> | accounts / shares / synchronizationSettings | Non | Non |
> | accounts / sharesubscriptions | Non | Non |
> | accounts / sharesubscriptions / consumerSourceDataSets | Non | Non |
> | accounts / sharesubscriptions / datasetmappings | Non | Non |
> | accounts / sharesubscriptions / triggers | Non | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | servers | OUI | OUI |
> | servers / advisors | Non | Non |
> | servers / privateEndpointConnectionProxies | Non | Non |
> | servers / privateEndpointConnections | Non | Non |
> | servers / privateLinkResources | Non | Non |
> | servers / queryTexts | Non | Non |
> | servers / recoverableServers | Non | Non |
> | servers / topQueryStatistics | Non | Non |
> | servers / virtualNetworkRules | Non | Non |
> | servers / waitStatistics | Non | Non |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | servers | OUI | OUI |
> | servers / advisors | Non | Non |
> | servers / privateEndpointConnectionProxies | Non | Non |
> | servers / privateEndpointConnections | Non | Non |
> | servers / privateLinkResources | Non | Non |
> | servers / queryTexts | Non | Non |
> | servers / recoverableServers | Non | Non |
> | servers / topQueryStatistics | Non | Non |
> | servers / virtualNetworkRules | Non | Non |
> | servers / waitStatistics | Non | Non |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | serverGroups | OUI | OUI |
> | servers | OUI | OUI |
> | servers / advisors | Non | Non |
> | servers / keys | Non | Non |
> | servers / privateEndpointConnectionProxies | Non | Non |
> | servers / privateEndpointConnections | Non | Non |
> | servers / privateLinkResources | Non | Non |
> | servers / queryTexts | Non | Non |
> | servers / recoverableServers | Non | Non |
> | servers / topQueryStatistics | Non | Non |
> | servers / virtualNetworkRules | Non | Non |
> | servers / waitStatistics | Non | Non |
> | serversv2 | OUI | OUI |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | artifactSources | OUI | OUI |
> | rollouts | OUI | OUI |
> | serviceTopologies | OUI | OUI |
> | serviceTopologies / services | OUI | OUI |
> | serviceTopologies / services / serviceUnits | OUI | OUI |
> | steps | OUI | OUI |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applicationgroups | OUI | OUI |
> | applicationgroups / applications | Non | Non |
> | applicationgroups / desktops | Non | Non |
> | applicationgroups / startmenuitems | Non | Non |
> | hostpools | OUI | OUI |
> | hostpools / sessionhosts | Non | Non |
> | hostpools / sessionhosts / usersessions | Non | Non |
> | hostpools / usersessions | Non | Non |
> | workspaces | OUI | OUI |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | ElasticPools | OUI | OUI |
> | ElasticPools / IotHubTenants | OUI | OUI |
> | IoTHubs | OUI | OUI |
> | IotHubs / eventGridFilters | Non | Non |
> | ProvisioningServices | OUI | OUI |
> | usages | Non | Non |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | pipelines | OUI | OUI |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | controllers | OUI | OUI |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | labcenters | OUI | OUI |
> | labs | OUI | OUI |
> | labs / environments | OUI | OUI |
> | labs / serviceRunners | OUI | OUI |
> | labs / virtualMachines | OUI | OUI |
> | schedules | OUI | OUI |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Non | Non |
> | databaseAccounts | OUI | OUI |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | domaines | OUI | OUI |
> | domains / domainOwnershipIdentifiers | Non | Non |
> | generateSsoRequest | Non | Non |
> | topLevelDomains | Non | Non |
> | validateDomainRegistrationInformation | Non | Non |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Non | Non |
> | lcsprojects / clouddeployments | Non | Non |
> | lcsprojects / connectors | Non | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | services | OUI | OUI |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | domaines | OUI | OUI |
> | domains / topics | Non | Non |
> | eventSubscriptions | Non | Non |
> | extensionTopics | Non | Non |
> | topics | OUI | OUI |
> | topicTypes | Non | Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | clusters | OUI | OUI |
> | namespaces | OUI | OUI |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / disasterrecoveryconfigs | Non | Non |
> | namespaces / eventhubs | Non | Non |
> | namespaces / eventhubs / authorizationrules | Non | Non |
> | namespaces / eventhubs / consumergroups | Non | Non |
> | namespaces / networkrulesets | Non | Non |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | features | Non | Non |
> | fournisseurs | Non | Non |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | enroll | Non | Non |
> | galleryitems | Non | Non |
> | generateartifactaccessuri | Non | Non |
> | myareas | Non | Non |
> | myareas / areas | Non | Non |
> | myareas / areas / areas | Non | Non |
> | myareas / areas / areas / galleryitems | Non | Non |
> | myareas / areas / galleryitems | Non | Non |
> | myareas / galleryitems | Non | Non |
> | inscription | Non | Non |
> | les ressources | Non | Non |
> | retrieveresourcesbyid | Non | Non |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Non | Non |
> | guestConfigurationAssignments | Non | Non |
> | software | Non | Non |
> | softwareUpdateProfile | Non | Non |
> | softwareUpdates | Non | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | hanaInstances | OUI | OUI |
> | sapMonitors | OUI | OUI |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | OUI | OUI |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | clusters | OUI | OUI |
> | clusters / applications | Non | Non |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | services | OUI | OUI |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | machines | OUI | OUI |
> | machines / extensions | OUI | OUI |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | dataManagers | OUI | OUI |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | components | OUI | OUI |
> | networkScopes | OUI | OUI |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | jobs | OUI | OUI |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Non | Non |
> | diagnosticSettingsCategories | Non | Non |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | appTemplates | Non | Non |
> | IoTApps | OUI | OUI |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | Graph | OUI | OUI |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Non | Non |
> | hsmPools | OUI | OUI |
> | vaults | OUI | OUI |
> | vaults / accessPolicies | Non | Non |
> | vaults / eventGridFilters | Non | Non |
> | vaults / secrets | Non | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | clusters | OUI | OUI |
> | clusters / attacheddatabaseconfigurations | Non | Non |
> | clusters / databases | Non | Non |
> | clusters / databases / dataconnections | Non | Non |
> | clusters / databases / eventhubconnections | Non | Non |
> | clusters / sharedidentities | Non | Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | labaccounts | OUI | OUI |
> | users | Non | Non |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | OUI | OUI |
> | integrationAccounts | OUI | OUI |
> | integrationServiceEnvironments | OUI | OUI |
> | integrationServiceEnvironments / managedApis | OUI | OUI |
> | isolatedEnvironments | OUI | OUI |
> | workflows | OUI | OUI |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | OUI | OUI |
> | webServices | OUI | OUI |
> | Workspaces | OUI | OUI |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | workspaces | OUI | OUI |
> | workspaces / computes | Non | Non |
> | workspaces / eventGridFilters | Non | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | Identities | Non | Non |
> | userAssignedIdentities | OUI | OUI |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Non | Non |
> | registrationAssignments | Non | Non |
> | registrationDefinitions | Non | Non |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | getEntities | Non | Non |
> | managementGroups | Non | Non |
> | les ressources | Non | Non |
> | startTenantBackfill | Non | Non |
> | tenantBackfillStatus | Non | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | OUI | OUI |
> | accounts / eventGridFilters | Non | Non |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | offers | Non | Non |
> | offerTypes | Non | Non |
> | offerTypes / publishers | Non | Non |
> | offerTypes / publishers / offers | Non | Non |
> | offerTypes / publishers / offers / plans | Non | Non |
> | offerTypes / publishers / offers / plans / agreements | Non | Non |
> | offerTypes / publishers / offers / plans / configs | Non | Non |
> | offerTypes / publishers / offers / plans / configs / importImage | Non | Non |
> | privategalleryitems | Non | Non |
> | products | Non | Non |
> | publishers | Non | Non |
> | publishers / offers | Non | Non |
> | publishers / offers / amendments | Non | Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | classicDevServices | OUI | OUI |
> | updateCommunicationPreference | Non | Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | agreements | Non | Non |
> | offertypes | Non | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | mediaservices | OUI | OUI |
> | mediaservices / accountFilters | Non | Non |
> | mediaservices / assets | Non | Non |
> | mediaservices / assets / assetFilters | Non | Non |
> | mediaservices / contentKeyPolicies | Non | Non |
> | mediaservices / eventGridFilters | Non | Non |
> | mediaservices / liveEventOperations | Non | Non |
> | mediaservices / liveEvents | OUI | OUI |
> | mediaservices / liveEvents / liveOutputs | Non | Non |
> | mediaservices / liveOutputOperations | Non | Non |
> | mediaservices / mediaGraphs | Non | Non |
> | mediaservices / streamingEndpointOperations | Non | Non |
> | mediaservices / streamingEndpoints | OUI | OUI |
> | mediaservices / streamingLocators | Non | Non |
> | mediaservices / streamingPolicies | Non | Non |
> | mediaservices / transforms | Non | Non |
> | mediaservices / transforms / jobs | Non | Non |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | appClusters | OUI | OUI |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | OUI | OUI |
> | migrateprojects | OUI | OUI |
> | projects | OUI | OUI |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | OUI | OUI |
> | objectUnderstandingAccounts | OUI | OUI |
> | remoteRenderingAccounts | OUI | OUI |
> | spatialAnchorsAccounts | OUI | OUI |
> | surfaceReconstructionAccounts | OUI | OUI |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | OUI | Non |
> | netAppAccounts / capacityPools | OUI | Non |
> | netAppAccounts / capacityPools / volumes | OUI | Non |
> | netAppAccounts / capacityPools / volumes / mountTargets | OUI | Non |
> | netAppAccounts / capacityPools / volumes / snapshots | OUI | Non |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applicationGateways | OUI | OUI |
> | applicationGatewayWebApplicationFirewallPolicies | OUI | OUI |
> | applicationSecurityGroups | OUI | OUI |
> | azureFirewallFqdnTags | Non | Non |
> | azureFirewalls | OUI | Non |
> | bastionHosts | OUI | OUI |
> | bgpServiceCommunities | Non | Non |
> | connections | OUI | OUI |
> | ddosCustomPolicies | OUI | OUI |
> | ddosProtectionPlans | OUI | OUI |
> | dnsOperationStatuses | Non | Non |
> | dnszones | OUI | OUI |
> | dnszones / A | Non | Non |
> | dnszones / AAAA | Non | Non |
> | dnszones / all | Non | Non |
> | dnszones / CAA | Non | Non |
> | dnszones / CNAME | Non | Non |
> | dnszones / MX | Non | Non |
> | dnszones / NS | Non | Non |
> | dnszones / PTR | Non | Non |
> | dnszones / recordsets | Non | Non |
> | dnszones / SOA | Non | Non |
> | dnszones / SRV | Non | Non |
> | dnszones / TXT | Non | Non |
> | expressRouteCircuits | OUI | OUI |
> | expressRouteCrossConnections | OUI | OUI |
> | expressRouteGateways | OUI | OUI |
> | expressRoutePorts | OUI | OUI |
> | expressRouteServiceProviders | Non | Non |
> | firewallPolicies | OUI | OUI |
> | frontdoors | Oui, mais limitée (voir la [remarque ci-dessous](#frontdoor)) | OUI |
> | frontdoorWebApplicationFirewallManagedRuleSets | Oui, mais limitée (voir la [remarque ci-dessous](#frontdoor)) | Non |
> | frontdoorWebApplicationFirewallPolicies | Oui, mais limitée (voir la [remarque ci-dessous](#frontdoor)) | OUI |
> | getDnsResourceReference | Non | Non |
> | internalNotify | Non | Non |
> | loadBalancers | OUI | Non |
> | localNetworkGateways | OUI | OUI |
> | natGateways | OUI | OUI |
> | networkIntentPolicies | OUI | OUI |
> | networkInterfaces | OUI | OUI |
> | networkProfiles | OUI | OUI |
> | networkSecurityGroups | OUI | OUI |
> | networkWatchers | OUI | Non |
> | networkWatchers / connectionMonitors | OUI | Non |
> | networkWatchers / lenses | OUI | Non |
> | networkWatchers / pingMeshes | OUI | Non |
> | p2sVpnGateways | OUI | OUI |
> | privateDnsOperationStatuses | Non | Non |
> | privateDnsZones | OUI | OUI |
> | privateDnsZones / A | Non | Non |
> | privateDnsZones / AAAA | Non | Non |
> | privateDnsZones / all | Non | Non |
> | privateDnsZones / CNAME | Non | Non |
> | privateDnsZones / MX | Non | Non |
> | privateDnsZones / PTR | Non | Non |
> | privateDnsZones / SOA | Non | Non |
> | privateDnsZones / SRV | Non | Non |
> | privateDnsZones / TXT | Non | Non |
> | privateDnsZones / virtualNetworkLinks | OUI | OUI |
> | privateEndpoints | OUI | OUI |
> | privateLinkServices | OUI | OUI |
> | publicIPAddresses | OUI | OUI |
> | publicIPPrefixes | OUI | OUI |
> | routeFilters | OUI | OUI |
> | routeTables | OUI | OUI |
> | serviceEndpointPolicies | OUI | OUI |
> | trafficManagerGeographicHierarchies | Non | Non |
> | trafficmanagerprofiles | OUI | OUI |
> | trafficmanagerprofiles/heatMaps | Non | Non |
> | trafficManagerUserMetricsKeys | Non | Non |
> | virtualHubs | OUI | OUI |
> | virtualNetworkGateways | OUI | OUI |
> | virtualNetworks | OUI | OUI |
> | virtualNetworkTaps | OUI | OUI |
> | virtualWans | OUI | OUI |
> | vpnGateways | OUI | Non |
> | vpnSites | OUI | OUI |
> | webApplicationFirewallPolicies | OUI | OUI |

<a id="frontdoor" />

> [!NOTE]
> Pour Azure Front Door Service, vous pouvez appliquer des balises lors de la création de la ressource, mais la mise à jour ou l’ajout de balises n’est actuellement pas pris en charge.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | namespaces | OUI | Non |
> | namespaces / notificationHubs | OUI | Non |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | osNamespaces | OUI | OUI |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | HyperVSites | OUI | OUI |
> | ImportSites | OUI | OUI |
> | ServerSites | OUI | OUI |
> | VMwareSites | OUI | OUI |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | clusters | OUI | OUI |
> | périphériques | Non | Non |
> | linkTargets | Non | Non |
> | storageInsightConfigs | Non | Non |
> | workspaces | OUI | OUI |
> | workspaces / dataSources | Non | Non |
> | workspaces / linkedServices | Non | Non |
> | workspaces / query | Non | Non |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | managementassociations | Non | Non |
> | managementconfigurations | OUI | OUI |
> | solutions | OUI | OUI |
> | views | OUI | OUI |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Non | Non |
> | peerAsns | Non | Non |
> | peerings | OUI | OUI |
> | peeringServiceProviders | Non | Non |
> | peeringServices | OUI | OUI |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | policyEvents | Non | Non |
> | policyMetadata | Non | Non |
> | policyStates | Non | Non |
> | policyTrackedResources | Non | Non |
> | remediations | Non | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | consoles | Non | Non |
> | dashboards | OUI | OUI |
> | userSettings | Non | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | OUI | OUI |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | capacities | OUI | OUI |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Non | Non |
> | vaults | OUI | OUI |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | namespaces | OUI | OUI |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / hybridconnections | Non | Non |
> | namespaces / hybridconnections / authorizationrules | Non | Non |
> | namespaces / wcfrelays | Non | Non |
> | namespaces / wcfrelays / authorizationrules | Non | Non |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | accounts | Non | Non |
> | collections | OUI | OUI |
> | collections / applications | Non | Non |
> | collections / securityprincipals | Non | Non |
> | templateImages | Non | Non |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | queries | OUI | OUI |
> | resourceChangeDetails | Non | Non |
> | resourceChanges | Non | Non |
> | les ressources | Non | Non |
> | resourcesHistory | Non | Non |
> | subscriptionsStatus | Non | Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Non | Non |
> | childAvailabilityStatuses | Non | Non |
> | childResources | Non | Non |
> | événements | Non | Non |
> | impactedResources | Non | Non |
> | metadata | Non | Non |
> | Notifications | Non | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | deployments | OUI | Non |
> | deployments / operations | Non | Non |
> | deploymentScripts | OUI | OUI |
> | deploymentScripts / logs | Non | Non |
> | links | Non | Non |
> | notifyResourceJobs | Non | Non |
> | fournisseurs | Non | Non |
> | resourceGroups | OUI | Non |
> | subscriptions | Non | Non |
> | tenants | Non | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applications | OUI | OUI |
> | saasresources | Non | Non |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | jobcollections | OUI | OUI |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Non | Non |
> | searchServices | OUI | OUI |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Non | Non |
> | advancedThreatProtectionSettings | Non | Non |
> | alertes | Non | Non |
> | allowedConnections | Non | Non |
> | applicationWhitelistings | Non | Non |
> | assessmentMetadata | Non | Non |
> | assessments | Non | Non |
> | autoDismissAlertsRules | Non | Non |
> | automations | OUI | OUI |
> | AutoProvisioningSettings | Non | Non |
> | Compliances | Non | Non |
> | dataCollectionAgents | Non | Non |
> | deviceSecurityGroups | Non | Non |
> | discoveredSecuritySolutions | Non | Non |
> | externalSecuritySolutions | Non | Non |
> | InformationProtectionPolicies | Non | Non |
> | iotSecuritySolutions | OUI | OUI |
> | iotSecuritySolutions / analyticsModels | Non | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Non | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Non | Non |
> | jitNetworkAccessPolicies | Non | Non |
> | networkData | Non | Non |
> | stratégies | Non | Non |
> | pricings | Non | Non |
> | regulatoryComplianceStandards | Non | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Non | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Non | Non |
> | securityContacts | Non | Non |
> | securitySolutions | Non | Non |
> | securitySolutionsReferenceData | Non | Non |
> | securityStatuses | Non | Non |
> | securityStatusesSummaries | Non | Non |
> | serverVulnerabilityAssessments | Non | Non |
> | paramètres | Non | Non |
> | subAssessments | Non | Non |
> | tâches | Non | Non |
> | topologies | Non | Non |
> | workspaceSettings | Non | Non |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Non | Non |
> | diagnosticSettingsCategories | Non | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | aggregations | Non | Non |
> | alertRules | Non | Non |
> | alertRuleTemplates | Non | Non |
> | bookmarks | Non | Non |
> | cas | Non | Non |
> | dataConnectors | Non | Non |
> | entities | Non | Non |
> | entityQueries | Non | Non |
> | officeConsents | Non | Non |
> | paramètres | Non | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | namespaces | OUI | Non |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / disasterrecoveryconfigs | Non | Non |
> | namespaces / eventgridfilters | Non | Non |
> | namespaces / networkrulesets | Non | Non |
> | namespaces / queues | Non | Non |
> | namespaces / queues / authorizationrules | Non | Non |
> | namespaces / topics | Non | Non |
> | namespaces / topics / authorizationrules | Non | Non |
> | namespaces / topics / subscriptions | Non | Non |
> | namespaces / topics / subscriptions / rules | Non | Non |
> | premiumMessagingRegions | Non | Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applications | OUI | OUI |
> | clusters | OUI | OUI |
> | clusters / applications | Non | Non |
> | containerGroups | OUI | OUI |
> | containerGroupSets | OUI | OUI |
> | edgeclusters | OUI | OUI |
> | edgeclusters / applications | Non | Non |
> | networks | OUI | OUI |
> | secretstores | OUI | OUI |
> | secretstores / certificates | Non | Non |
> | secretstores / secrets | Non | Non |
> | volumes | OUI | OUI |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applications | OUI | OUI |
> | containerGroups | OUI | OUI |
> | gateways | OUI | OUI |
> | networks | OUI | OUI |
> | secrets | OUI | OUI |
> | volumes | OUI | OUI |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Non | Non |
> | providerRegistrations / resourceTypeRegistrations | Non | Non |
> | rollouts | OUI | OUI |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | SignalR | OUI | OUI |
> | SignalR / eventGridFilters | Non | Non |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | OUI | OUI |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Non | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | OUI | OUI |
> | applications | OUI | OUI |
> | jitRequests | OUI | OUI |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | managedInstances | OUI | OUI |
> | managedInstances / databases | Non | Non |
> | managedInstances / databases / backupShortTermRetentionPolicies | Non | Non |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Non | Non |
> | managedInstances / databases / vulnerabilityAssessments | Non | Non |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Non | Non |
> | managedInstances / encryptionProtector | Non | Non |
> | managedInstances / keys | Non | Non |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Non | Non |
> | managedInstances / vulnerabilityAssessments | Non | Non |
> | servers | OUI | OUI |
> | servers / administrators | Non | Non |
> | servers / communicationLinks | Non | Non |
> | servers / databases | Oui (voir la [remarque ci-dessous](#sqlnote)) | OUI |
> | servers / encryptionProtector | Non | Non |
> | servers / firewallRules | Non | Non |
> | servers / keys | Non | Non |
> | servers / restorableDroppedDatabases | Non | Non |
> | servers / serviceobjectives | Non | Non |
> | servers / tdeCertificates | Non | Non |
> | virtualClusters | Non | Non |

<a id="sqlnote" />

> [!NOTE]
> La base de données MASTER ne prend pas en charge les balises, à la différence d’autres bases de données, comme les bases de données Azure SQL Data Warehouse. Les bases de données Azure SQL Data Warehouse doivent avoir l’état Actif (pas en pause).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | OUI | OUI |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Non | Non |
> | SqlVirtualMachines | OUI | OUI |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | storageAccounts | OUI | OUI |
> | storageAccounts / blobServices | Non | Non |
> | storageAccounts / fileServices | Non | Non |
> | storageAccounts / queueServices | Non | Non |
> | storageAccounts / services | Non | Non |
> | storageAccounts / services / metricDefinitions | Non | Non |
> | storageAccounts / tableServices | Non | Non |
> | usages | Non | Non |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | caches | OUI | OUI |
> | caches / storageTargets | Non | Non |
> | usageModels | Non | Non |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Non | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | OUI | OUI |
> | storageSyncServices / registeredServers | Non | Non |
> | storageSyncServices / syncGroups | Non | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non | Non |
> | storageSyncServices / workflows | Non | Non |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | OUI | OUI |
> | storageSyncServices / registeredServers | Non | Non |
> | storageSyncServices / syncGroups | Non | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non | Non |
> | storageSyncServices / workflows | Non | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | OUI | OUI |
> | storageSyncServices / registeredServers | Non | Non |
> | storageSyncServices / syncGroups | Non | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non | Non |
> | storageSyncServices / workflows | Non | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | managers | OUI | OUI |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Oui (voir la remarque ci-dessous) | OUI |

> [!NOTE]
> Vous ne pouvez pas ajouter une balise lorsque streamingjobs est en cours d’exécution. Arrêtez la ressource pour pouvoir ajouter une balise.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | annuler | Non | Non |
> | CreateSubscription | Non | Non |
> | enable | Non | Non |
> | renommer | Non | Non |
> | SubscriptionDefinitions | Non | Non |
> | SubscriptionOperations | Non | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | environments | OUI | Non |
> | environments / accessPolicies | Non | Non |
> | environments / eventsources | OUI | Non |
> | environments / referenceDataSets | OUI | Non |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | OUI | OUI |
> | dedicatedCloudServices | OUI | OUI |
> | virtualMachines | OUI | OUI |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Non | Non |
> | apiManagementAccounts / apiAcls | Non | Non |
> | apiManagementAccounts / apis | Non | Non |
> | apiManagementAccounts / apis / apiAcls | Non | Non |
> | apiManagementAccounts / apis / connectionAcls | Non | Non |
> | apiManagementAccounts / apis / connections | Non | Non |
> | apiManagementAccounts / apis / connections / connectionAcls | Non | Non |
> | apiManagementAccounts / apis / localizedDefinitions | Non | Non |
> | apiManagementAccounts / connectionAcls | Non | Non |
> | apiManagementAccounts / connections | Non | Non |
> | billingMeters | Non | Non |
> | certificates | OUI | OUI |
> | connectionGateways | OUI | OUI |
> | connections | OUI | OUI |
> | customApis | OUI | OUI |
> | deletedSites | Non | Non |
> | functions | Non | Non |
> | hostingEnvironments | OUI | OUI |
> | hostingEnvironments / multiRolePools | Non | Non |
> | hostingEnvironments / workerPools | Non | Non |
> | publishingUsers | Non | Non |
> | de films | Non | Non |
> | resourceHealthMetadata | Non | Non |
> | runtimes | Non | Non |
> | serverFarms | OUI | OUI |
> | serverFarms / eventGridFilters | Non | Non |
> | sites | OUI | OUI |
> | sites / config  | Non | Non |
> | sites / eventGridFilters | Non | Non |
> | sites / hostNameBindings | Non | Non |
> | sites / networkConfig | Non | Non |
> | sites / premieraddons | OUI | OUI |
> | sites / slots | OUI | OUI |
> | sites / slots / eventGridFilters | Non | Non |
> | sites / slots / hostNameBindings | Non | Non |
> | sites / slots / networkConfig | Non | Non |
> | sourceControls | Non | Non |
> | validate | Non | Non |
> | verifyHostingEnvironmentVnet | Non | Non |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Non | Non |
> | diagnosticSettingsCategories | Non | Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | DeviceServices | OUI | OUI |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Prend en charge les étiquettes | Balise dans le rapport des coûts |
> | ------------- | ----------- | ----------- |
> | components | Non | Non |
> | componentsSummary | Non | Non |
> | monitorInstances | Non | Non |
> | monitorInstancesSummary | Non | Non |
> | monitors | Non | Non |
> | notificationSettings | Non | Non |

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment appliquer des étiquettes aux ressources, consultez [Utiliser des étiquettes pour organiser vos ressources Azure](resource-group-using-tags.md).

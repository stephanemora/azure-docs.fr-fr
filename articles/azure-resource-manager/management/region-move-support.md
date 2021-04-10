---
title: Prise en charge du déplacement des ressources Azure entre les régions
description: Répertorie les types de ressources Azure qui peuvent être déplacés entre les régions Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 9229fca9f98aac4ca628c0bb25c13c9ba1989626
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962592"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Prise en charge du déplacement des ressources Azure entre les régions

Cet article confirme si le déplacement d’un type de ressource Azure vers une autre région Azure est pris en charge. 

Accédez à un espace de noms du fournisseur de ressources :
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
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
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
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
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | domainservices | Non | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | diagnosticsettings | Non |
> | diagnosticsettingscategories | Non |
> | privatelinkforazuread | Non |
> | tenants |  Non |

## <a name="microsoftaddons"></a>microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | supportproviders | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
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
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | configurations | Non | 
> | generaterecommendations | Non |
> | metadata | Non |
> | de films | Non |
> | suppressions | Non | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | actionrules | Non | 
> | alertes | Non | 
> | alertslist | Non | 
> | alertsmetadata | Non | 
> | alertssummary | Non | 
> | alertssummarylist | Non | 
> | smartdetectoralertrules | Non | 
> | smartgroups | Non | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | servers | Non |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | reportfeedback | Non |
> | service |  Oui, (utilisation d’un modèle) <br/><br/> [Déplacer la Gestion des API d'une région vers une autre](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | configurationstores | Non | 
> | configurationstores / eventgridfilters | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | spring | Non | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | apiapps | Oui, (utilisation d’un modèle)<br/><br/> [Déplacer une application App Service vers une autre région](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Non | 
> | gateways | Non | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | attestationproviders | Non | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | classicadministrators | Non | 
> | dataaliases | Non | 
> | denyassignments | Non | 
> | elevateaccess | Non | 
> | findorphanroleassignments | Non | 
> | locks | Non | 
> | autorisations | Non | 
> | policyassignments | Non | 
> | policydefinitions | Non | 
> | policysetdefinitions | Non | 
> | privatelinkassociations | Non | 
> | resourcemanagementprivatelinks | Non | 
> | roleassignments | Non | 
> | roleassignmentsusagemetrics | Non | 
> | roledefinitions | Non | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | automationaccounts | Oui, (utilisation d’un modèle) <br/><br/> [Utilisation de la géoréplication](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts / configurations | Non | 
> | automationaccounts / runbooks | Non | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | Abonnement |
> | ------------- | ----------- | 
> | privateclouds | Non | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | b2cdirectories | Non | 
> | b2ctenants | Non | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | datacontrollers | Non | 
> | hybriddatamanagers | Non | 
> | postgresinstances | Non | 
> | sqlinstances | Non | 
> | sqlmanagedinstances | Non |
> | sqlserverinstances | Non | 
> | sqlserverregistrations | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Non |
> | registrations | Non | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | clusters | Non | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | batchaccounts |  Les comptes Batch ne peuvent pas être déplacés directement d’une région à une autre, mais vous pouvez utiliser un modèle pour exporter un modèle, le modifier et déployer le modèle dans la nouvelle région. <br/><br/> Apprenez-en davantage sur le [déplacement d’un compte Batch d’une région à une autre](../../batch/best-practices.md#moving-batch-accounts-across-regions). |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | billingaccounts | Non | 
> | billingperiods | Non | 
> | billingpermissions | Non | 
> | billingproperty | Non | 
> | billingroleassignments | Non | 
> | billingroledefinitions | Non | 
> | departments | Non | 
> | enrollmentaccounts | Non | 
> | invoices | Non | 
> | transfers | Non | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | mapapis | Non | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | biztalk | Non | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | blockchainmembers | Non <br/><br/> Le réseau blockchain ne peut pas avoir de nœuds dans des régions différentes. 
> | cordamembers | Non |
> | watchers | Non | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | tokenservices | Non |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | blueprintassignments | Non | 
> | blueprints | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | botservices | Non | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | redis | Non | 
> | redisenterprise | Non | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | appliedreservations | Non | 
> | calculateexchange | Non | 
> | calculateprice | Non | 
> | calculatepurchaseprice | Non | 
> | catalogs | Non | 
> | commercialreservationorders | Non | 
> | change | Non |
> | reservationorders | Non | 
> | reservations | Non | 
> | les ressources | Non | 
> | validatereservationorder | Non | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Non |
> | edgenodes | Non
> | profiles | Non | 
> | profiles / endpoints | Non | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | certificateorders | Non | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | capabilities | Non | 
> | domainnames | Non |
> | quotas | Non | 
> | resourcetypes | Non |
> | validatesubscriptionmoveavailability | Non | 
> | virtualmachines | Non 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Non | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | capabilities | Non | 
> | expressroutecrossconnections | Non | 
> | expressroutecrossconnections / peerings | Non | 
> | gatewaysupporteddevices | Non | 
> | networksecuritygroups | Non |
> | quotas | Non |
> | reservedips | Non | 
> | virtualnetworks | Non | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | disks | Non | 
> | images | Non | 
> | osimages | Non | 
> | osplatformimages | Non | 
> | publicimages | Non | 
> | quotas | Non | 
> | storageaccounts | Oui |  
> | vmimages | Non |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | opérations | Non | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 
> | Recherche cognitive | Pris en charge avec étapes manuelles.<br/><br/> Apprenez-en davantage sur le [déplacement de votre service Recherche cognitive Azure vers une autre région](../../search/search-howto-move-across-regions.md).

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | ratecard | Non | 
> | usageaggregates | Non | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | availabilitysets | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des groupes à haute disponibilité. | 
> | diskaccesses | Non |
> | diskencryptionsets | Non | 
> | disks | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des machines virtuelles Azure et des disques associés. | 
> | galleries | Non | 
> | galleries / images | Non | 
> | galleries / images / versions | Non | 
> | hostgroups | Non | 
> | hostgroups / hosts | Non | 
> | images | Non | 
> | proximityplacementgroups | Non | 
> | restorepointcollections | Non | 
> | sharedvmimages | Non | 
> | sharedvmimages / versions | Non | 
> | snapshots | Non | 
> | sshpublickeys | Non |
> | virtualmachines | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des machines virtuelles Azure. | 
> | virtualmachines / extensions | Non | 
> | virtualmachinescalesets | Non | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | aggregatedcost | Non | 
> | balances | Non | 
> | budgets | Non | 
> | charges | Non | 
> | costtags | Non | 
> | credits | Non | 
> | événements | Non | 
> | forecasts | Non | 
> | lots | Non | 
> | marketplaces | Non | 
> | pricesheets | Non | 
> | products | Non | 
> | reservationdetails | Non | 
> | reservationrecommendationdetails | Non | 
> | reservationrecommendations | Non | 
> | reservationsummaries | Non | 
> | reservationtransactions | Non | 
> | tags | Non | 
> | tenants | Non | 
> | terms | Non | 
> | usagedetails | Non | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | containergroups | Non | 
> | serviceassociationlinks | Non |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | registries | Non |  
> | registries / agentpools | Non | 
> | registries / buildtasks | Non |  
> | registries / replications | Non | 
> | registries / tasks | Non |  
> | registries / webhooks | Non | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | containerservices | Non |
> | managedclusters | Non | 
> | openshiftmanagedclusters | Non | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | applications | Non | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | alertes | Non | 
> | billingaccounts | Non | 
> | budgets | Non | 
> | cloudconnectors | Non | 
> | connecteurs | Non | 
> | departments | Non | 
> | dimensions | Non | 
> | enrollmentaccounts | Non | 
> | exports | Non | 
> | externalbillingaccounts | Non | 
> | forecast | Non | 
> | query | Non | 
> | inscription | Non | 
> | reportconfigs | Non | 
> | reports | Non | 
> | paramètres | Non | 
> | showbackrules | Non | 
> | views | Non | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | hubs | Non |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | requêtes | Non | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | associations | Non |
> | resourceproviders | Non | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | jobs | Non | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | availableskus | Non |
> | databoxedgedevices | Non | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | workspaces | Non | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | catalogs | Non | 
> | datacatalogs | Non | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | connectionmanagers | Non | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | packages | Non | 
> | plans | Non | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | datafactories | Non | 
> | factories | Non |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | datalakeaccounts | Non | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | services | Non | 
> | services / projects | Non | 
> | slots | Non | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Non | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | servers | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [Plus d’informations](../../postgresql/howto-move-regions-portal.md)<br/><br/> Si le service est provisionné avec un stockage de sauvegarde géoredondant, vous pouvez utiliser la géorestauration pour effectuer une restauration dans d’autres régions. [Plus d’informations](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | servers | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [Plus d’informations](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | servergroups | Non | 
> | servers | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [En savoir plus](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Non | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | artifactsources | Non | 
> | rollouts | Non |  
> | servicetopologies | Non | 
> | servicetopologies / services | Non |  
> | servicetopologies / services / serviceunits | Non | 
> | steps | Non | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | applicationgroups | Non | 
> | workspaces | Non | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | elasticpools | Non. La ressource n’est pas exposée.
> | elasticpools / iothubtenants | Non. La ressource n’est pas exposée.
> | iothubs | Oui. [En savoir plus](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Non | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | controllers | Non | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | controllers | Non | 
> | Cluster AKS | Non<br/><br/> [Apprenez-en davantage](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sur le déplacement vers une autre région.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | labcenters | Non | 
> | labs | Non | 
> | labs / environments | Non |  
> | labs / servicerunners | Non | 
> | labs / virtualmachines | Non |  
> | schedules | Non |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Oui, en recréant des ressources dans une nouvelle région. [En savoir plus](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | databaseaccounts | Non | 
> | databaseaccounts | Non | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | domaines | Non | 
> | generatessorequest | Non | 
> | topleveldomains | Non | 
> | validatedomainregistrationinformation | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | services | Non |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | domaines | Non | 
> | eventsubscriptions | Non |
> | extensiontopics | Non | 
> | partnernamespaces | Non | 
> | partnerregistrations | Non | 
> | partnertopics | Non | 
> | systemtopics | Non | 
> | topics | Non | 
> | topictypes | Non | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | clusters | Non |  
> | espaces de noms | Oui (avec modèle)<br/><br/> [Déplacer un espace de noms Event Hubs vers une autre région](../../event-hubs/move-across-regions.md) | 
> | sku | Non |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | experimentworkspaces | Non | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | espaces de noms | Non | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | featureproviders | Non | 
> | features | Non | 
> | fournisseurs | Non | 
> | subscriptionfeatureregistrations | Non | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | automanagedaccounts | Non | 
> | automanagedvmconfigurationprofiles | Non | 
> | guestconfigurationassignments | Non | 
> | software | Non | 
> | softwareupdateprofile | Non | 
> | softwareupdates | Non | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | hanainstances | Non | 
> | sapmonitors | Non |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | dedicatedhsms | Non | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | clusters | Non | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | services | Non |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | machines | Non | 
> | machines / extensions | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | datamanagers |  Non | 

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | périphériques | Non | 
> | vnfs | Non | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | components | Non | 
> | networkscopes | Non | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | jobs |  Non | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non. [Plus d’informations](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region) |
> | actiongroups |  Non | 
> | activitylogalerts | Non | 
> | alertrules |  Non | 
> | autoscalesettings |  Non | 
> | baseline | Non |
> | components |  Non |  
> | datacollectionrules | Non | 
> | diagnosticsettings | Non | 
> | diagnosticsettingscategories | Non | 
> | eventcategories | Non | 
> | eventtypes | Non | 
> | extendeddiagnosticsettings | Non |
> | guestdiagnosticsettings | Non | 
> | listmigrationdate | Non | 
> | logdefinitions | Non | 
> | logprofiles | Non | 
> | logs | Non |
> | metricalerts | Non | 
> | metricbaselines | Non | 
> | metricbatch | Non | 
> | metricdefinitions | Non | 
> | metricnamespaces | Non | 
> | Mesures | Non | 
> | migratealertrules | Non |
> | migratetonewpricingmodel | Non | 
> | myworkbooks | Non |
> | notificationgroups | Non | 
> | privatelinkscopes | Non |
> | rollbacktolegacypricingmodel | Non |
> | scheduledqueryrules |  Non | 
> | Topologie | Non |
> | transactions | Non |
> | vminsightsonboardingstatuses | Non |
> | webtests |  Non | 
> | webtests / gettestresultfile | Non |
> | workbooks |  Non |  
> | workbooktemplates | Non |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | apptemplates | Non | 
> | iotapps | Non | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> |  iothub |  Oui (cloner un hub) <br/><br/> [Cloner un hub IoT dans une autre région](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région |
> | ------------- | ----------- | 
> | graph | Non | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | deletedvaults | Non |
> | hsmpools | Non | 
> | managedhsms | Non |
> | vaults |  Non | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | connectedclusters | Non | 
> | registeredsubscriptions | Non | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Non | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | clusters |  Non |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | labaccounts | Non | 
> | users | Non | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non, il s’agit d’un service mondial.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | hostingenvironments | Non | 
> | integrationaccounts |  Non |  
> | integrationserviceenvironments | Non | 
> | integrationserviceenvironments / managedapis | Non |
> | isolatedenvironments | Non | 
> | workflows |  Non |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | commitmentplans |  Non | 
> | webservices |  Non | 
> | workspaces |  Non | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | operationalizationclusters |  Non | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 
> | teamaccounts | Non | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | workspaces | Non | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | configurationassignments | Oui. [En savoir plus](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Oui. [En savoir plus](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Non | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | Identités | Non | 
> | userassignedidentities | Non | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | managednetworks | Non | 
> | managednetworks / managednetworkgroups | Non |
> | managednetworks / managednetworkpeeringpolicies | Non | 
> | notification | Non | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Non | 
> | registrationassignments | Non |
> | registrationdefinitions | Non | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | getentities | Non | 
> | managementgroups | Non | 
> | managementgroups / settings | Non | 
> | les ressources | Non | 
> | starttenantbackfill | Non | 
> | tenantbackfillstatus | Non | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts |  Non, Azure Maps est un service géospatial. 
> | accounts / privateatlases | Non

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | offers | Non | 
> | offertypes | Non | 
> | privategalleryitems | Non | 
> | privatestoreclient | Non | 
> | privatestores | Non | 
> | products | Non | 
> | publishers | Non | 
> | inscription | Non | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | classicdevservices | Non | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | agreements | Non | 
> | offertypes | Non | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | mediaservices |  Non | 
> | mediaservices / liveevents |  Non | 
> | mediaservices / streamingendpoints |  Non | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | appclusters | Non | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | assessmentprojects | Non | 
> | migrateprojects | Non | 
> | movecollections | Non
> | projects | Non | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Non | 
> | objectunderstandingaccounts | Non | 
> | remoterenderingaccounts | Non | 
> | spatialanchorsaccounts | Non | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | netappaccounts | Non | 
> | netappaccounts / capacitypools | Non | 
> | netappaccounts / capacitypools / volumes | Non | 
> | netappaccounts / capacitypools / volumes / mounttargets | Non | 
> | netappaccounts / capacitypools / volumes / snapshots | Non | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | applicationgateways | Non |
> | applicationgatewaywebapplicationfirewallpolicies | Non | 
> | applicationsecuritygroups |  Non |  
> | azurefirewalls |  Non |  
> | bastionhosts | Non | 
> | bgpservicecommunities | Non |
> | connections |  Non | 
> | ddoscustompolicies |  Non | 
> | ddosprotectionplans | Non | 
> | dnszones |  Non | 
> | expressroutecircuits | Non | 
> | expressroutegateways | Non | 
> | expressrouteserviceproviders | Non | 
> | firewallpolicies | Non |
> | frontdoors | Non | 
> | ipallocations | Non |
> | ipgroups | Non |
> | loadbalancers | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des équilibreurs de charge internes et externes. |
> | localnetworkgateways |  Non | 
> | natgateways |  Non | 
> | networkexperimentprofiles | Non |
> | networkintentpolicies |  Non | 
> | networkinterfaces | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des cartes réseau. | 
> | networkprofiles | Non | 
> | networksecuritygroups | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des groupes de sécurité réseau (NGS). | 
> | networkwatchers |  Non |  
> | networkwatchers / connectionmonitors |  Non | 
> | networkwatchers / flowlogs |  Non | 
> | networkwatchers / pingmeshes |  Non | 
> | p2svpngateways | Non | 
> | privatednszones |  Non |  
> | privatednszones / virtualnetworklinks | Non |
> | privatednszonesinternal | Non |
> | privateendpointredirectmaps | Non |
> | privateendpoints | Non | 
> | privatelinkservices | Non | 
> | publicipaddresses | Oui<br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des adresses IP publiques. |
> | publicipprefixes | Non | 
> | routefilters | Non | 
> | routetables |  Non | 
> | securitypartnerproviders | Non |
> | serviceendpointpolicies |  Non | 
> | trafficmanagergeographichierarchies | Non | 
> | trafficmanagerprofiles |  Non | 
> | trafficmanagerusermetricskeys | Non |
> | virtualhubs | Non | 
> | virtualnetworkgateways |  Non |  
> | virtualnetworks |  Non | 
> | virtualnetworktaps | Non | 
> | virtualwans | Non | 
> | vpngateways (Virtual WAN) | Non | 
> | vpnsites (Virtual WAN) | Non | 
> | vpnsites (Virtual WAN) | Non |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | espaces de noms |  Non | 
> | namespaces / notificationhubs |  Non |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | osnamespaces | Non | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | hypervsites | Non | 
> | importsites | Non | 
> | serversites | Non | 
> | vmwaresites | Non | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | clusters | Non | 
> | deletedworkspaces | Non | 
> | linktargets | Non | 
> | storageinsightconfigs | Non |
> | workspaces | Non |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | managementassociations | Non |
> | managementconfigurations |  Non | 
> | solutions | Non |
> | views |  Non | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | legacypeerings | Non | 
> | peerasns | Non | 
> | peeringlocations | Non | 
> | peerings | Non | 
> | peeringservicecountries | Non | 
> | peeringservicelocations | Non | 
> | peeringserviceproviders | Non | 
> | peeringservices | Non | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | policyevents | Non | 
> | policystates | Non | 
> | policytrackedresources | Non | 
> | remediations | Non | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | consoles | Non |
> | dashboards | Non | 
> | usersettings | Non | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | workspacecollections |  Non | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | capacities |  Non | 

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | accounts | Non | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | availableaccounts | Non | 
> | providerregistrations | Non | 
> | rollouts | Non | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | workspaces | Non | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Non |
> | vaults | Non.<br/><br/> Le déplacement de coffres Recovery Services pour Sauvegarde Azure d’une région Azure à une autre n’est pas pris en charge.<br/><br/> Dans les coffres Recovery Services pour Azure Site Recovery, vous pouvez [désactiver et recréer le coffre](../../site-recovery/move-vaults-across-regions.md) dans la région cible. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | openshiftclusters | Non | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | espaces de noms |  Non | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | queries |  Non |  
> | resourcechangedetails | Non | 
> | resourcechanges | Non | 
> | les ressources | Non | 
> | resourceshistory | Non | 
> | subscriptionsstatus | Non | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | childresources | Non | 
> | emergingissues | Non | 
> | événements | Non | 
> | metadata | Non | 
> | Notifications | Non | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région |
> | ------------- | ----------- |
> | deploymentScripts |  Oui<br/><br/>[Déplacer des ressources Microsoft.Resources vers une nouvelle région](microsoft-resources-move-regions.md) |
> | templateSpecs |  Oui<br/><br/>[Déplacer des ressources Microsoft.Resources vers une nouvelle région](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | applications |  Non | 
> | saasresources | Non | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Non |
> | searchservices |  Non | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Non | 
> | advancedthreatprotectionsettings | Non | 
> | alertes | Non | 
> | allowedconnections | Non | 
> | applicationwhitelistings | Non | 
> | assessmentmetadata | Non | 
> | assessments | Non | 
> | autodismissalertsrules | Non | 
> | automations | Non | 
> | autoprovisioningsettings | Non |
> | complianceresults | Non | 
> | compliances | Non | 
> | datacollectionagents | Non | 
> | devicesecuritygroups | Non | 
> | discoveredsecuritysolutions | Non | 
> | externalsecuritysolutions | Non | 
> | informationprotectionpolicies | Non | 
> | iotsecuritysolutions | Non | 
> | iotsecuritysolutions / analyticsmodels | Non | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Non | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Non | 
> | jitnetworkaccesspolicies | Non | 
> | stratégies | Non | 
> | pricings | Non | 
> | regulatorycompliancestandards | Non | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Non | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Non | 
> | securitycontacts | Non | 
> | securitysolutions | Non | 
> | securitysolutionsreferencedata | Non | 
> | securitystatuses | Non | 
> | securitystatusessummaries | Non | 
> | servervulnerabilityassessments | Non | 
> | paramètres | Non | 
> | subassessments | Non |
> | tâches | Non | 
> | topologies | Non | 
> | workspacesettings | Non | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | aggregations | Non | 
> | alertrules | Non | 
> | alertruletemplates | Non | 
> | automationrules | Non |
> | cas | Non | 
> | dataconnectors | Non | 
> | entities | Non | 
> | entityqueries | Non |
> | incidents | Non | 
> | officeconsents | Non | 
> | paramètres | Non | 
> | threatintelligence | Non | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | consoleservices | Non | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | gateways | Non | 
> | nœuds | Non | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | espaces de noms |  Non | 
> | premiummessagingregions | Non | 
> | sku | Non | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | applications | Non | 
> | clusters |  Non |  
> | containergroups | Non | 
> | containergroupsets | Non | 
> | edgeclusters | Non | 
> | managedclusters | Non |
> | networks | Non | 
> | secretstores | Non | 
> | volumes | Non | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | applications |  Non | 
> | containergroups | Non | 
> | gateways |  Non | 
> | networks |  Non | 
> | secrets |  Non | 
> | volumes |  Non |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | rollouts | Non | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | signalr |  Non |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | hybridusebenefits | Non | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | appliancedefinitions | Non | 
> | appliances | Non | 
> | jitrequests | Non | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | instancepools | Non | 
> | locations | Non |
> | managedinstances | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des instances managées entre les régions. | 
> | managedinstances / databases | Oui | 
> | servers | Oui | 
> | servers / databases | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des bases de données entre les régions.<br/><br/> [Apprenez-en davantage](../../resource-mover/tutorial-move-region-sql.md) sur l’utilisation d’Azure Resource Mover pour déplacer des bases de données Azure SQL.  | 
> | servers / elasticpools | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des pools élastiques entre les régions.<br/><br/> [Apprenez-en davantage](../../resource-mover/tutorial-move-region-sql.md) sur l’utilisation d’Azure Resource Mover pour déplacer des pools élastiques Azure SQL.  | 
> | virtualclusters | Oui | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Non |  
> | sqlvirtualmachines |  Non |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | storageaccounts | Oui<br/><br/> [Déplacer un compte Stockage Azure vers une autre région](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | caches | Non | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | storagesyncservices |  Non | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | storagesyncservices | Non | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | storagesyncservices | Non | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | managers | Non | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | clusters | Non |
> | streamingjobs |  Non |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | environments | Non | 
> | instances | Non | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | subscriptions | Non | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | services | Non | 
> | supporttickets | Non | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | workspaces | Non | 
> | workspaces / bigdatapools | Non | 
> | workspaces / sqlpools | Non | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | environments |  Non | 
> | environments / eventsources |  Non |  
> | environments / referencedatasets |  Non | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | stores | Non | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | imagetemplates | Non | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | account |  Non | 
> | account / extension |  Non | 
> | account / project |  Non | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | arczones | Non | 
> | resourcepools | Non | 
> | vcenters | Non | 
> | virtualmachines | Non | 
> | virtualmachinetemplates | Non | 
> | virtualnetworks | Non | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Non | 
> | dedicatedcloudservices | Non | 
> | virtualmachines | Non | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | périphériques | Non | 
> | vnfs | Non | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | accounts | Non | 
> | plans | Non | 
> | registeredsubscriptions | Non |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | availablestacks | Non | 
> | billingmeters | Non | 
> | certificates | Non | 
> | connectiongateways |  Non |  
> | connections |  Non |  
> | customapis |  Non | 
> | deletedsites | Non | 
> | deploymentlocations | Non | 
> | georegions | Non | 
> | hostingenvironments | Non | 
> | kubeenvironments | Non | 
> | publishingusers | Non |
> | de films | Non | 
> | resourcehealthmetadata | Non | 
> | runtimes | Non | 
> | serverfarms | Non |  
> | serverfarms / eventgridfilters | N
> | sites |  Non | 
> | sites / premieraddons |  Non |  
> | sites / slots |  Non |  
> | sourcecontrols | Non |
> | staticsites | Non | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- |
> | deviceservices | Non | 

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | workloads | Non | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Changement de région | 
> | ------------- | ----------- | 
> | components | Non |
> | componentssummary | Non | 
> | monitorinstances | Non | 
> | monitorinstancessummary | Non | 
> | monitors | Non | 
## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](../../resource-mover/overview.md) sur le service de déplacement de ressources.


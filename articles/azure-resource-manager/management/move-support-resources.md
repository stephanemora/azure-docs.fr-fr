---
title: Prise en charge des opérations de déplacement par type de ressource
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539225"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources

Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Il fournit également des informations sur les conditions spéciales à prendre en compte lors du déplacement d’une ressource.

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
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
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
> - [Microsoft.CostManagementExports](#microsoftcostmanagementexports)
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
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
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
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | domainservices | Non | Non |
> | domainservices / oucontainer | Non | Non |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Non | Non |
> | diagnosticsettingscategories | Non | Non |
> | opérations | Non | Non |
> | privatelinkforazuread | Oui | Oui |
> | tenants | Oui | Oui |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | supportproviders | Non | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Non | Non |
> | addsservices | Non | Non |
> | agents | Non | Non |
> | anonymousapiusers | Non | Non |
> | configuration | Non | Non |
> | logs | Non | Non |
> | opérations | Non | Non |
> | reports | Non | Non |
> | servicehealthmetrics | Non | Non |
> | services | Non | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | configurations | Non | Non |
> | generaterecommendations | Non | Non |
> | metadata | Non | Non |
> | opérations | Non | Non |
> | de films | Non | Non |
> | suppressions | Non | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | actionrules | Oui | Oui |
> | alertes | Non | Non |
> | alertslist | Non | Non |
> | alertsmetadata | Non | Non |
> | alertssummary | Non | Non |
> | alertssummarylist | Non | Non |
> | opérations | Non | Non |
> | smartdetectoralertrules | Oui | Oui |
> | smartgroups | Non | Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | servers | Oui | Oui |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Non | Non |
> | checknameavailability | Non | Non |
> | checkservicenameavailability | Non | Non |
> | opérations | Non | Non |
> | reportfeedback | Non | Non |
> | service | Oui | Oui |
> | validateservicename | Non | Non |

> [!IMPORTANT]
> Un service Gestion des API dont la référence SKU définie est Consommation ne peut pas être déplacé.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | configurationstores | Oui | Oui |
> | configurationstores / eventgridfilters | Non | Non |
> | locations | Non | Non |
> | locations / operationsstatus | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatus | Non | Non |
> | opérations | Non | Non |
> | spring | Oui | Oui |
> | spring / apps | Non | Non |
> | spring / apps / deployments | Non | Non |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | apiapps | Non | Non |
> | appidentities | Non | Non |
> | gateways | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkaccess | Non | Non |
> | classicadministrators | Non | Non |
> | dataaliases | Non | Non |
> | denyassignments | Non | Non |
> | elevateaccess | Non | Non |
> | findorphanroleassignments | Non | Non |
> | locks | Non | Non |
> | opérations | Non | Non |
> | operationstatus | Non | Non |
> | autorisations | Non | Non |
> | policyassignments | Non | Non |
> | policydefinitions | Non | Non |
> | policysetdefinitions | Non | Non |
> | privatelinkassociations | Non | Non |
> | provideroperations | Non | Non |
> | resourcemanagementprivatelinks | Non | Non |
> | roleassignments | Non | Non |
> | roleassignmentsusagemetrics | Non | Non |
> | roledefinitions | Non | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Oui | Oui |
> | automationaccounts / configurations | Oui | Oui |
> | automationaccounts / jobs | Non | Non |
> | automationaccounts / privateendpointconnectionproxies | Non | Non |
> | automationaccounts / privateendpointconnections | Non | Non |
> | automationaccounts / privatelinkresources | Non | Non |
> | automationaccounts / runbooks | Oui | Oui |
> | automationaccounts / softwareupdateconfigurations | Non | Non |
> | automationaccounts / webhooks | Non | Non |
> | opérations | Non | Non |

> [!IMPORTANT]
> Les runbooks doivent exister dans le même groupe de ressources que le compte Automation.
>
> Pour plus d'informations, consultez [Déplacer votre compte Azure Automation vers un autre abonnement](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checkquotaavailability | Non | Non |
> | locations / checktrialavailability | Non | Non |
> | opérations | Non | Non |
> | privateclouds | Oui | Oui |
> | privateclouds / clusters | Non | Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Oui | Oui |
> | b2ctenants | Non | Non |
> | checknameavailability | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Non | Non |
> | hybriddatamanagers | Non | Non |
> | opérations | Non | Non |
> | postgresinstances | Non | Non |
> | sqlinstances | Non | Non |
> | sqlmanagedinstances | Non | Non |
> | sqlserverinstances | Non | Non |
> | sqlserverregistrations | Oui | Oui |
> | sqlserverregistrations / sqlservers | Non | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Non | Non |
> | opérations | Non | Non |
> | registrations | Oui | Oui |
> | registrations / customersubscriptions | Non | Non |
> | registrations / products | Non | Non |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Oui | Oui |
> | locations | Non | Non |
> | locations / accountoperationresults | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / quotas | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Non | Non |
> | billingaccounts / agreements | Non | Non |
> | billingaccounts / billingpermissions | Non | Non |
> | billingaccounts / billingprofiles | Non | Non |
> | billingaccounts / billingprofiles / availablebalance | Non | Non |
> | billingaccounts / billingprofiles / billingpermissions | Non | Non |
> | billingaccounts / billingprofiles / billingroleassignments | Non | Non |
> | billingaccounts / billingprofiles / billingroledefinitions | Non | Non |
> | billingaccounts / billingprofiles / billingsubscriptions | Non | Non |
> | billingaccounts / billingprofiles / createbillingroleassignment | Non | Non |
> | billingaccounts / billingprofiles / customers | Non | Non |
> | billingaccounts / billingprofiles / instructions | Non | Non |
> | billingaccounts / billingprofiles / invoices | Non | Non |
> | billingaccounts / billingprofiles / invoices / pricesheet | Non | Non |
> | billingaccounts / billingprofiles / invoices / transactions | Non | Non |
> | billingaccounts / billingprofiles / invoicesections | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / products | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / products / transfer | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / products / updateautorenew | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / transactions | Non | Non |
> | billingaccounts / billingprofiles / invoicesections / transfers | Non | Non |
> | billingaccounts / billingprofiles / patchoperations | Non | Non |
> | billingaccounts / billingprofiles / paymentmethods | Non | Non |
> | billingaccounts / billingprofiles / policies | Non | Non |
> | billingaccounts / billingprofiles / pricesheet | Non | Non |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Non | Non |
> | billingaccounts / billingprofiles / products | Non | Non |
> | billingaccounts / billingprofiles / transactions | Non | Non |
> | billingaccounts / billingroleassignments | Non | Non |
> | billingaccounts / billingroledefinitions | Non | Non |
> | billingaccounts / billingsubscriptions | Non | Non |
> | billingaccounts / billingsubscriptions / invoices | Non | Non |
> | billingaccounts / createbillingroleassignment | Non | Non |
> | billingaccounts / createinvoicesectionoperations | Non | Non |
> | billingaccounts / customers | Non | Non |
> | billingaccounts / customers / billingpermissions | Non | Non |
> | billingaccounts / customers / billingsubscriptions | Non | Non |
> | billingaccounts / customers / initiatetransfer | Non | Non |
> | billingaccounts / customers / policies | Non | Non |
> | billingaccounts / customers / products | Non | Non |
> | billingaccounts / customers / transactions | Non | Non |
> | billingaccounts / customers / transfers | Non | Non |
> | billingaccounts / departments | Non | Non |
> | billingaccounts / enrollmentaccounts | Non | Non |
> | billingaccounts / invoices | Non | Non |
> | billingaccounts / invoicesections | Non | Non |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Non | Non |
> | billingaccounts / invoicesections / billingsubscriptions | Non | Non |
> | billingaccounts / invoicesections / billingsubscriptions / transfer | Non | Non |
> | billingaccounts / invoicesections / elevate | Non | Non |
> | billingaccounts / invoicesections / initiatetransfer | Non | Non |
> | billingaccounts / invoicesections / patchoperations | Non | Non |
> | billingaccounts / invoicesections / productmoveoperations | Non | Non |
> | billingaccounts / invoicesections / products | Non | Non |
> | billingaccounts / invoicesections / products / transfer | Non | Non |
> | billingaccounts / invoicesections / products / updateautorenew | Non | Non |
> | billingaccounts / invoicesections / producttransfersresults | Non | Non |
> | billingaccounts / invoicesections / transactions | Non | Non |
> | billingaccounts / invoicesections / transfers | Non | Non |
> | billingaccounts / lineofcredit | Non | Non |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Non | Non |
> | billingaccounts / operationresults | Non | Non |
> | billingaccounts / patchoperations | Non | Non |
> | billingaccounts / paymentmethods | Non | Non |
> | billingaccounts / products | Non | Non |
> | billingaccounts / transactions | Non | Non |
> | billingperiods | Non | Non |
> | billingpermissions | Non | Non |
> | billingproperty | Non | Non |
> | billingroleassignments | Non | Non |
> | billingroledefinitions | Non | Non |
> | createbillingroleassignment | Non | Non |
> | departments | Non | Non |
> | enrollmentaccounts | Non | Non |
> | invoices | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | operationstatus | Non | Non |
> | transfers | Non | Non |
> | transfers / accepttransfer | Non | Non |
> | transfers / declinetransfer | Non | Non |
> | transfers / operationstatus | Non | Non |
> | transfers / validatetransfer | Non | Non |
> | validateaddress | Non | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Non | Non |
> | mapapis | Non | Non |
> | opérations | Non | Non |
> | updatecommunicationpreference | Non | Non |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | biztalk | Non | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Non | Non |
> | cordamembers | Non | Non |
> | locations | Non | Non |
> | locations / blockchainmemberoperationresults | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / listconsortiums | Non | Non |
> | locations / watcheroperationresults | Non | Non |
> | opérations | Non | Non |
> | watchers | Non | Non |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |
> | tokenservices | Non | Non |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Non | Non |
> | blueprintassignments / assignmentoperations | Non | Non |
> | blueprintassignments / operations | Non | Non |
> | blueprints | Non | Non |
> | blueprints / artifacts | Non | Non |
> | blueprints / versions | Non | Non |
> | blueprints / versions / artifacts | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | botservices | Oui | Oui |
> | botservices / channels | Non | Non |
> | botservices / connections | Non | Non |
> | checknameavailability | Non | Non |
> | listauthserviceproviders | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationsstatus | Non | Non |
> | opérations | Non | Non |
> | redis | Oui | Oui |
> | redis / eventgridfilters | Non | Non |
> | redis / privatelinkresources | Non | Non |
> | redisenterprise | Non | Non |

> [!IMPORTANT]
> Si l’instance du Cache Azure pour Redis est configurée avec un réseau virtuel, l’instance ne peut pas être déplacée vers un autre abonnement. Consultez [Limitations relatives au déplacement réseau](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Non | Non |
> | calculateexchange | Non | Non |
> | calculateprice | Non | Non |
> | calculatepurchaseprice | Non | Non |
> | catalogs | Non | Non |
> | checkoffers | Non | Non |
> | checkpurchasestatus | Non | Non |
> | checkscopes | Non | Non |
> | commercialreservationorders | Non | Non |
> | change | Non | Non |
> | listbenefits | Non | Non |
> | opérations | Non | Non |
> | placepurchaseorder | Non | Non |
> | reservationorders | Non | Non |
> | reservationorders / availablescopes | Non | Non |
> | reservationorders / calculaterefund | Non | Non |
> | reservationorders / merge | Non | Non |
> | reservationorders / reservations | Non | Non |
> | reservationorders / reservations / availablescopes | Non | Non |
> | reservationorders / reservations / revisions | Non | Non |
> | reservationorders / return | Non | Non |
> | reservationorders / split | Non | Non |
> | reservationorders / swap | Non | Non |
> | reservations | Non | Non |
> | les ressources | Non | Non |
> | validatereservationorder | Non | Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Non | Non |
> | cdnwebapplicationfirewallpolicies | Oui | Oui |
> | checknameavailability | Non | Non |
> | checkresourceusage | Non | Non |
> | edgenodes | Non | Non |
> | operationresults | Non | Non |
> | operationresults / profileresults | Non | Non |
> | operationresults / profileresults / endpointresults | Non | Non |
> | operationresults / profileresults / endpointresults / customdomainresults | Non | Non |
> | operationresults / profileresults / endpointresults / origingroupresults | Non | Non |
> | operationresults / profileresults / endpointresults / originresults | Non | Non |
> | opérations | Non | Non |
> | profiles | Oui | Oui |
> | profiles / endpoints | Oui | Oui |
> | profiles / endpoints / customdomains | Non | Non |
> | profiles / endpoints / origingroups | Non | Non |
> | profiles / endpoints / origins | Non | Non |
> | validateprobe | Non | Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | certificateorders | Oui | Oui |
> | certificateorders / certificates | Non | Non |
> | opérations | Non | Non |
> | validatecertificateregistrationinformation | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | capabilities | Non | Non |
> | checkdomainnameavailability | Non | Non |
> | domainnames | Oui | Non |
> | domainnames / capabilities | Non | Non |
> | domainnames / internalloadbalancers | Non | Non |
> | domainnames / servicecertificates | Non | Non |
> | domainnames / slots | Non | Non |
> | domainnames / slots / roles | Non | Non |
> | domainnames / slots / roles / metricdefinitions | Non | Non |
> | domainnames / slots / roles / metrics | Non | Non |
> | movesubscriptionresources | Non | Non |
> | operatingsystemfamilies | Non | Non |
> | operatingsystems | Non | Non |
> | opérations | Non | Non |
> | operationstatuses | Non | Non |
> | quotas | Non | Non |
> | resourcetypes | Non | Non |
> | validatesubscriptionmoveavailability | Non | Non |
> | virtualmachines | Oui | Non |
> | virtualmachines / diagnosticsettings | Non | Non |
> | virtualmachines / metricdefinitions | Non | Non |
> | virtualmachines / metrics | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | capabilities | Non | Non |
> | expressroutecrossconnections | Non | Non |
> | expressroutecrossconnections / peerings | Non | Non |
> | gatewaysupporteddevices | Non | Non |
> | networksecuritygroups | Non | Non |
> | opérations | Non | Non |
> | quotas | Non | Non |
> | reservedips | Non | Non |
> | virtualnetworks | Non | Non |
> | virtualnetworks / remotevirtualnetworkpeeringproxies | Non | Non |
> | virtualnetworks / virtualnetworkpeerings | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | capabilities | Non | Non |
> | checkstorageaccountavailability | Non | Non |
> | disks | Non | Non |
> | images | Non | Non |
> | opérations | Non | Non |
> | osimages | Non | Non |
> | osplatformimages | Non | Non |
> | publicimages | Non | Non |
> | quotas | Non | Non |
> | storageaccounts | Oui | Non |
> | storageaccounts / blobservices | Non | Non |
> | storageaccounts / fileservices | Non | Non |
> | storageaccounts / metricdefinitions | Non | Non |
> | storageaccounts / metrics | Non | Non |
> | storageaccounts / queueservices | Non | Non |
> | storageaccounts / services | Non | Non |
> | storageaccounts / services / diagnosticsettings | Non | Non |
> | storageaccounts / services / metricdefinitions | Non | Non |
> | storageaccounts / services / metrics | Non | Non |
> | storageaccounts / tableservices | Non | Non |
> | storageaccounts / vmimages | Non | Non |
> | vmimages | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | checkdomainavailability | Non | Non |
> | locations | Non | Non |
> | locations / checkskuavailability | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |
> | ratecard | Non | Non |
> | usageaggregates | Non | Non |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Oui | Oui |
> | diskaccesses | Non | Non |
> | diskencryptionsets | Non | Non |
> | disks | Oui | Oui |
> | galleries | Non | Non |
> | galleries / images | Non | Non |
> | galleries / images / versions | Non | Non |
> | hostgroups | Non | Non |
> | hostgroups / hosts | Non | Non |
> | images | Oui | Oui |
> | locations | Non | Non |
> | locations / artifactpublishers | Non | Non |
> | locations / capsoperations | Non | Non |
> | locations / diskoperations | Non | Non |
> | locations / loganalytics | Non | Non |
> | locations / operations | Non | Non |
> | locations / publishers | Non | Non |
> | locations / runcommands | Non | Non |
> | locations / usages | Non | Non |
> | locations / virtualmachines | Non | Non |
> | locations / vmsizes | Non | Non |
> | locations / vsmoperations | Non | Non |
> | opérations | Non | Non |
> | proximityplacementgroups | Oui | Oui |
> | restorepointcollections | Non | Non |
> | restorepointcollections / restorepoints | Non | Non |
> | sharedvmextensions | Non | Non |
> | sharedvmimages | Non | Non |
> | sharedvmimages / versions | Non | Non |
> | snapshots | Oui | Oui |
> | sshpublickeys | Non | Non |
> | virtualmachines | Oui | Oui |
> | virtualmachines / extensions | Oui | Oui |
> | virtualmachines / metricdefinitions | Non | Non |
> | virtualmachines / runcommands | Non | Non |
> | virtualmachinescalesets | Oui | Oui |
> | virtualmachinescalesets / extensions | Non | Non |
> | virtualmachinescalesets / networkinterfaces | Non | Non |
> | virtualmachinescalesets / publicipaddresses | Non | Non |
> | virtualmachinescalesets / virtualmachines | Non | Non |
> | virtualmachinescalesets / virtualmachines / networkinterfaces | Non | Non |

> [!IMPORTANT]
> Consultez [Conseils pour le déplacement de machines virtuelles](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Non | Non |
> | balances | Non | Non |
> | budgets | Non | Non |
> | charges | Non | Non |
> | costtags | Non | Non |
> | credits | Non | Non |
> | événements | Non | Non |
> | forecasts | Non | Non |
> | lots | Non | Non |
> | marketplaces | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | operationstatus | Non | Non |
> | pricesheets | Non | Non |
> | products | Non | Non |
> | reservationdetails | Non | Non |
> | reservationrecommendationdetails | Non | Non |
> | reservationrecommendations | Non | Non |
> | reservationsummaries | Non | Non |
> | reservationtransactions | Non | Non |
> | tags | Non | Non |
> | tenants | Non | Non |
> | terms | Non | Non |
> | usagedetails | Non | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | containergroups | Non | Non |
> | locations | Non | Non |
> | locations / cachedimages | Non | Non |
> | locations / capabilities | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operations | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |
> | serviceassociationlinks | Non | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / authorize | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / setupauth | Non | Non |
> | opérations | Non | Non |
> | registries | Oui | Oui |
> | registries / agentpools | Oui | Oui |
> | registries / agentpools / listqueuestatus | Non | Non |
> | registries / builds | Non | Non |
> | registries / builds / cancel | Non | Non |
> | registries / builds / getloglink | Non | Non |
> | registries / buildtasks | Oui | Oui |
> | registries / buildtasks / listsourcerepositoryproperties | Non | Non |
> | registries / buildtasks / steps | Non | Non |
> | registries / buildtasks / steps / listbuildarguments | Non | Non |
> | registries / eventgridfilters | Non | Non |
> | registries / exportpipelines | Non | Non |
> | registries / generatecredentials | Non | Non |
> | registries / getbuildsourceuploadurl | Non | Non |
> | registries / getcredentials | Non | Non |
> | registries / importimage | Non | Non |
> | registries / importpipelines | Non | Non |
> | registries / listbuildsourceuploadurl | Non | Non |
> | registries / listcredentials | Non | Non |
> | registries / listpolicies | Non | Non |
> | registries / listusages | Non | Non |
> | registries / pipelineruns | Non | Non |
> | registries / privateendpointconnectionproxies | Non | Non |
> | registries / privateendpointconnectionproxies / validate | Non | Non |
> | registries / privateendpointconnections | Non | Non |
> | registries / privatelinkresources | Non | Non |
> | registries / queuebuild | Non | Non |
> | registries / regeneratecredential | Non | Non |
> | registries / regeneratecredentials | Non | Non |
> | registries / replications | Oui | Oui |
> | registries / runs | Non | Non |
> | registries / runs / cancel | Non | Non |
> | registries / runs / listlogsasurl | Non | Non |
> | registries / schedulerun | Non | Non |
> | registries / scopemaps | Non | Non |
> | registries / taskruns | Non | Non |
> | registries / taskruns / listdetails | Non | Non |
> | registries / tasks | Oui | Oui |
> | registries / tasks / listdetails | Non | Non |
> | registries / tokens | Non | Non |
> | registries / updatepolicies | Non | Non |
> | registries / webhooks | Oui | Oui |
> | registries / webhooks / getcallbackconfig | Non | Non |
> | registries / webhooks / listevents | Non | Non |
> | registries / webhooks / ping | Non | Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | containerservices | Non | Non |
> | locations | Non | Non |
> | locations / openshiftclusters | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operations | Non | Non |
> | locations / orchestrators | Non | Non |
> | managedclusters | Non | Non |
> | openshiftmanagedclusters | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applications | Non | Non |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | alertes | Non | Non |
> | billingaccounts | Non | Non |
> | budgets | Non | Non |
> | cloudconnectors | Non | Non |
> | connecteurs | Oui | Oui |
> | departments | Non | Non |
> | dimensions | Non | Non |
> | enrollmentaccounts | Non | Non |
> | exports | Non | Non |
> | externalbillingaccounts | Non | Non |
> | externalbillingaccounts / alerts | Non | Non |
> | externalbillingaccounts / dimensions | Non | Non |
> | externalbillingaccounts / forecast | Non | Non |
> | externalbillingaccounts / query | Non | Non |
> | externalsubscriptions | Non | Non |
> | externalsubscriptions / alerts | Non | Non |
> | externalsubscriptions / dimensions | Non | Non |
> | externalsubscriptions / forecast | Non | Non |
> | externalsubscriptions / query | Non | Non |
> | forecast | Non | Non |
> | opérations | Non | Non |
> | query | Non | Non |
> | inscription | Non | Non |
> | reportconfigs | Non | Non |
> | reports | Non | Non |
> | paramètres | Non | Non |
> | showbackrules | Non | Non |
> | views | Non | Non |

## <a name="microsoftcostmanagementexports"></a>Microsoft.CostManagementExports

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | hubs | Non | Non |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |
> | requêtes | Non | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | associations | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | resourceproviders | Oui | Oui |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | jobs | Non | Non |
> | locations | Non | Non |
> | locations / availableskus | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / regionconfiguration | Non | Non |
> | locations / validateaddress | Non | Non |
> | locations / validateinputs | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableskus | Non | Non |
> | databoxedgedevices | Oui | Oui |
> | databoxedgedevices / checknameavailability | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / getnetworkpolicies | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | workspaces | Non | Non |
> | workspaces / dbworkspaces | Non | Non |
> | workspaces / virtualnetworkpeerings | Non | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | catalogs | Oui | Oui |
> | checknameavailability | Non | Non |
> | datacatalogs | Non | Non |
> | locations | Non | Non |
> | locations / jobs | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Non | Non |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | packages | Non | Non |
> | plans | Non | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Non | Non |
> | checkdatafactorynameavailability | Non | Non |
> | datafactories | Oui | Oui |
> | datafactories / diagnosticsettings | Non | Non |
> | datafactories / metricdefinitions | Non | Non |
> | datafactoryschema | Non | Non |
> | factories | Oui | Oui |
> | factories / integrationruntimes | Non | Non |
> | locations | Non | Non |
> | locations / configurefactoryrepo | Non | Non |
> | locations / getfeaturevalue | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Non | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | accounts / datalakestoreaccounts | Non | Non |
> | accounts / storageaccounts | Non | Non |
> | accounts / storageaccounts / containers | Non | Non |
> | accounts / storageaccounts / containers / listsastokens | Non | Non |
> | locations | Non | Non |
> | locations / capability | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | accounts / eventgridfilters | Non | Non |
> | accounts / firewallrules | Non | Non |
> | locations | Non | Non |
> | locations / capability | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | services | Non | Non |
> | services / projects | Non | Non |
> | slots | Non | Non |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupvaults | Non | Non |
> | locations | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | accounts / shares | Non | Non |
> | accounts / shares / datasets | Non | Non |
> | accounts / shares / invitations | Non | Non |
> | accounts / shares / providersharesubscriptions | Non | Non |
> | accounts / shares / synchronizationsettings | Non | Non |
> | accounts / sharesubscriptions | Non | Non |
> | accounts / sharesubscriptions / consumersourcedatasets | Non | Non |
> | accounts / sharesubscriptions / datasetmappings | Non | Non |
> | accounts / sharesubscriptions / triggers | Non | Non |
> | listinvitations | Non | Non |
> | locations | Non | Non |
> | locations / consumerinvitations | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / rejectinvitation | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / azureasyncoperation | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / performancetiers | Non | Non |
> | locations / privateendpointconnectionazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionoperationresults | Non | Non |
> | locations / privateendpointconnectionproxyazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionproxyoperationresults | Non | Non |
> | locations / recommendedactionsessionsazureasyncoperation | Non | Non |
> | locations / recommendedactionsessionsoperationresults | Non | Non |
> | locations / securityalertpoliciesazureasyncoperation | Non | Non |
> | locations / securityalertpoliciesoperationresults | Non | Non |
> | locations / serverkeyazureasyncoperation | Non | Non |
> | locations / serverkeyoperationresults | Non | Non |
> | opérations | Non | Non |
> | servers | Oui | Oui |
> | servers / advisors | Non | Non |
> | servers / privateendpointconnectionproxies | Non | Non |
> | servers / privateendpointconnections | Non | Non |
> | servers / privatelinkresources | Non | Non |
> | servers / querytexts | Non | Non |
> | servers / recoverableservers | Non | Non |
> | servers / topquerystatistics | Non | Non |
> | servers / virtualnetworkrules | Non | Non |
> | servers / waitstatistics | Non | Non |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / administratorazureasyncoperation | Non | Non |
> | locations / administratoroperationresults | Non | Non |
> | locations / azureasyncoperation | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / performancetiers | Non | Non |
> | locations / privateendpointconnectionazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionoperationresults | Non | Non |
> | locations / privateendpointconnectionproxyazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionproxyoperationresults | Non | Non |
> | locations / recommendedactionsessionsazureasyncoperation | Non | Non |
> | locations / recommendedactionsessionsoperationresults | Non | Non |
> | locations / securityalertpoliciesazureasyncoperation | Non | Non |
> | locations / securityalertpoliciesoperationresults | Non | Non |
> | locations / serverkeyazureasyncoperation | Non | Non |
> | locations / serverkeyoperationresults | Non | Non |
> | opérations | Non | Non |
> | servers | Oui | Oui |
> | servers / advisors | Non | Non |
> | servers / keys | Non | Non |
> | servers / privateendpointconnectionproxies | Non | Non |
> | servers / privateendpointconnections | Non | Non |
> | servers / privatelinkresources | Non | Non |
> | servers / querytexts | Non | Non |
> | servers / recoverableservers | Non | Non |
> | servers / topquerystatistics | Non | Non |
> | servers / virtualnetworkrules | Non | Non |
> | servers / waitstatistics | Non | Non |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / administratorazureasyncoperation | Non | Non |
> | locations / administratoroperationresults | Non | Non |
> | locations / azureasyncoperation | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / performancetiers | Non | Non |
> | locations / privateendpointconnectionazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionoperationresults | Non | Non |
> | locations / privateendpointconnectionproxyazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionproxyoperationresults | Non | Non |
> | locations / recommendedactionsessionsazureasyncoperation | Non | Non |
> | locations / recommendedactionsessionsoperationresults | Non | Non |
> | locations / securityalertpoliciesazureasyncoperation | Non | Non |
> | locations / securityalertpoliciesoperationresults | Non | Non |
> | locations / serverkeyazureasyncoperation | Non | Non |
> | locations / serverkeyoperationresults | Non | Non |
> | opérations | Non | Non |
> | servergroups | Non | Non |
> | servers | Oui | Oui |
> | servers / advisors | Non | Non |
> | servers / keys | Non | Non |
> | servers / privateendpointconnectionproxies | Non | Non |
> | servers / privateendpointconnections | Non | Non |
> | servers / privatelinkresources | Non | Non |
> | servers / querytexts | Non | Non |
> | servers / recoverableservers | Non | Non |
> | servers / topquerystatistics | Non | Non |
> | servers / virtualnetworkrules | Non | Non |
> | servers / waitstatistics | Non | Non |
> | serversv2 | Oui | Oui |
> | singleservers | Oui | Oui |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | artifactsources | Oui | Oui |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | rollouts | Oui | Oui |
> | servicetopologies | Oui | Oui |
> | servicetopologies / services | Oui | Oui |
> | servicetopologies / services / serviceunits | Oui | Oui |
> | steps | Oui | Oui |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Oui | Oui |
> | applicationgroups / applications | Non | Non |
> | applicationgroups / desktops | Non | Non |
> | applicationgroups / startmenuitems | Non | Non |
> | hostpools | Oui | Oui |
> | hostpools / sessionhosts | Non | Non |
> | hostpools / sessionhosts / usersessions | Non | Non |
> | hostpools / usersessions | Non | Non |
> | opérations | Non | Non |
> | workspaces | Oui | Oui |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | checkprovisioningservicenameavailability | Non | Non |
> | elasticpools | Non | Non |
> | elasticpools / iothubtenants | Non | Non |
> | iothubs | Oui | Oui |
> | iothubs / eventgridfilters | Non | Non |
> | iothubs / securitysettings | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | provisioningservices | Oui | Oui |
> | usages | Non | Non |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | pipelines | Oui | Oui |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | controllers | Oui | Oui |
> | controllers / listconnectiondetails | Non | Non |
> | locations | Non | Non |
> | locations / checkcontainerhostmapping | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | labcenters | Non | Non |
> | labs | Oui | Non |
> | labs / environments | Oui | Oui |
> | labs / servicerunners | Oui | Oui |
> | labs / virtualmachines | Oui | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |
> | opérations | Non | Non |
> | schedules | Oui | Oui |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Non | Non |
> | digitaltwinsinstances / operationresults | Non | Non |
> | locations | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Non | Non |
> | databaseaccounts | Oui | Oui |
> | locations | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationsstatus | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Non | Non |
> | domaines | Oui | Oui |
> | domains / domainownershipidentifiers | Non | Non |
> | generatessorequest | Non | Non |
> | listdomainrecommendations | Non | Non |
> | opérations | Non | Non |
> | topleveldomains | Non | Non |
> | validatedomainregistrationinformation | Non | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |
> | services | Oui | Oui |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | domaines | Oui | Oui |
> | domains / topics | Non | Non |
> | eventsubscriptions | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) |
> | extensiontopics | Non | Non |
> | locations | Non | Non |
> | locations / eventsubscriptions | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationsstatus | Non | Non |
> | locations / topictypes | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | operationsstatus | Non | Non |
> | partnernamespaces | Oui | Oui |
> | partnernamespaces / eventchannels | Non | Non |
> | partnerregistrations | Non | Non |
> | partnertopics | Oui | Oui |
> | partnertopics / eventsubscriptions | Non | Non |
> | systemtopics | Oui | Oui |
> | systemtopics / eventsubscriptions | Non | Non |
> | topics | Oui | Oui |
> | topictypes | Non | Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Non | Non |
> | checknameavailability | Non | Non |
> | checknamespaceavailability | Non | Non |
> | clusters | Oui | Oui |
> | locations | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | espaces de noms | Oui | Oui |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / disasterrecoveryconfigs | Non | Non |
> | namespaces / disasterrecoveryconfigs / checknameavailability | Non | Non |
> | namespaces / eventhubs | Non | Non |
> | namespaces / eventhubs / authorizationrules | Non | Non |
> | namespaces / eventhubs / consumergroups | Non | Non |
> | namespaces / networkrulesets | Non | Non |
> | opérations | Non | Non |
> | sku | Non | Non |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Non | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | espaces de noms | Oui | Oui |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | featureproviders | Non | Non |
> | features | Non | Non |
> | opérations | Non | Non |
> | fournisseurs | Non | Non |
> | subscriptionfeatureregistrations | Non | Non |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Non | Non |
> | automanagedvmconfigurationprofiles | Non | Non |
> | guestconfigurationassignments | Non | Non |
> | opérations | Non | Non |
> | software | Non | Non |
> | softwareupdateprofile | Non | Non |
> | softwareupdates | Non | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | hanainstances | Non | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |
> | locations / operationsstatus | Non | Non |
> | opérations | Non | Non |
> | sapmonitors | Oui | Oui |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Non | Non |
> | locations | Non | Non |
> | opérations | Non | Non |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Oui | Oui |
> | clusters / applications | Non | Non |
> | clusters / operationresults | Non | Non |
> | locations | Non | Non |
> | locations / azureasyncoperations | Non | Non |
> | locations / billingspecs | Non | Non |
> | locations / capabilities | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / usages | Non | Non |
> | locations / validatecreaterequest | Non | Non |
> | opérations | Non | Non |

> [!IMPORTANT]
> Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.
>
> Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |
> | services | Oui | Oui |
> | services / privateendpointconnections | Non | Non |
> | services / privatelinkresources | Non | Non |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatus | Non | Non |
> | machines | Oui | Oui |
> | machines / extensions | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | datamanagers | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | périphériques | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | vnfs | Non | Non |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | components | Non | Non |
> | locations | Non | Non |
> | networkscopes | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | jobs | Oui | Oui |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | actiongroups | Oui | Oui |
> | activitylogalerts | Non | Non |
> | alertrules | Oui | Oui |
> | autoscalesettings | Oui | Oui |
> | baseline | Non | Non |
> | calculatebaseline | Non | Non |
> | components | Oui | Oui |
> | components / events | Non | Non |
> | components / linkedstorageaccounts | Non | Non |
> | components / metadata | Non | Non |
> | components / metrics | Non | Non |
> | components / pricingplans | Non | Non |
> | components / query | Non | Non |
> | datacollectionrules | Non | Non |
> | diagnosticsettings | Non | Non |
> | diagnosticsettingscategories | Non | Non |
> | eventcategories | Non | Non |
> | eventtypes | Non | Non |
> | extendeddiagnosticsettings | Non | Non |
> | guestdiagnosticsettings | Non | Non |
> | listmigrationdate | Non | Non |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | logdefinitions | Non | Non |
> | logprofiles | Non | Non |
> | logs | Non | Non |
> | metricalerts | Non | Non |
> | metricbaselines | Non | Non |
> | metricbatch | Non | Non |
> | metricdefinitions | Non | Non |
> | metricnamespaces | Non | Non |
> | Mesures | Non | Non |
> | migratealertrules | Non | Non |
> | migratetonewpricingmodel | Non | Non |
> | myworkbooks | Non | Non |
> | notificationgroups | Non | Non |
> | opérations | Non | Non |
> | privatelinkscopeoperationstatuses | Non | Non |
> | privatelinkscopes | Non | Non |
> | privatelinkscopes / privateendpointconnectionproxies | Non | Non |
> | privatelinkscopes / privateendpointconnections | Non | Non |
> | privatelinkscopes / scopedresources | Non | Non |
> | rollbacktolegacypricingmodel | Non | Non |
> | scheduledqueryrules | Oui | Oui |
> | Topologie | Non | Non |
> | transactions | Non | Non |
> | vminsightsonboardingstatuses | Non | Non |
> | webtests | Oui | Oui |
> | webtests / gettestresultfile | Non | Non |
> | workbooks | Oui | Oui |
> | workbooktemplates | Oui | Oui |

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | apptemplates | Non | Non |
> | checknameavailability | Non | Non |
> | checksubdomainavailability | Non | Non |
> | iotapps | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Oui | Oui |
> | graph | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | deletedvaults | Non | Non |
> | hsmpools | Non | Non |
> | locations | Non | Non |
> | locations / deletedvaults | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / operationresults | Non | Non |
> | managedhsms | Non | Non |
> | opérations | Non | Non |
> | vaults | Oui | Oui |
> | vaults / accesspolicies | Non | Non |
> | vaults / eventgridfilters | Non | Non |
> | vaults / secrets | Non | Non |

> [!IMPORTANT]
> Les coffres de clés utilisés pour le chiffrement de disques ne peuvent pas être déplacés vers des groupes de ressources situés dans le même abonnement ni d’un abonnement à l’autre.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Oui | Oui |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | registeredsubscriptions | Non | Non |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Non | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Oui | Oui |
> | clusters / attacheddatabaseconfigurations | Non | Non |
> | clusters / databases | Non | Non |
> | clusters / databases / dataconnections | Non | Non |
> | clusters / databases / eventhubconnections | Non | Non |
> | clusters / databases / principalassignments | Non | Non |
> | clusters / principalassignments | Non | Non |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | labaccounts | Non | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |
> | opérations | Non | Non |
> | users | Non | Non |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Non | Non |
> | integrationaccounts | Oui | Oui |
> | integrationserviceenvironments | Oui | Non |
> | integrationserviceenvironments / managedapis | Oui | Non |
> | isolatedenvironments | Non | Non |
> | locations | Non | Non |
> | locations / workflows | Non | Non |
> | opérations | Non | Non |
> | workflows | Oui | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Non | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |
> | locations / operationsstatus | Non | Non |
> | opérations | Non | Non |
> | webservices | Oui | Non |
> | workspaces | Oui | Oui |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Non | Non |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |
> | accounts / workspaces | Non | Non |
> | accounts / workspaces / projects | Non | Non |
> | teamaccounts | Non | Non |
> | teamaccounts / workspaces | Non | Non |
> | teamaccounts / workspaces / projects | Non | Non |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / computeoperationsstatus | Non | Non |
> | locations / quotas | Non | Non |
> | locations / updatequotas | Non | Non |
> | locations / usages | Non | Non |
> | locations / vmsizes | Non | Non |
> | locations / workspaceoperationsstatus | Non | Non |
> | opérations | Non | Non |
> | workspaces | Non | Non |
> | workspaces / computes | Non | Non |
> | workspaces / eventgridfilters | Non | Non |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applyupdates | Non | Non |
> | configurationassignments | Non | Non |
> | maintenanceconfigurations | Oui | Oui |
> | updates | Non | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | Identités | Non | Non |
> | opérations | Non | Non |
> | userassignedidentities | Non | Non |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | managednetworks | Non | Non |
> | managednetworks / managednetworkgroups | Non | Non |
> | managednetworks / managednetworkpeeringpolicies | Non | Non |
> | notification | Non | Non |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Non | Non |
> | opérations | Non | Non |
> | operationstatuses | Non | Non |
> | registrationassignments | Non | Non |
> | registrationdefinitions | Non | Non |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | getentities | Non | Non |
> | managementgroups | Non | Non |
> | managementgroups / settings | Non | Non |
> | operationresults | Non | Non |
> | operationresults / asyncoperation | Non | Non |
> | opérations | Non | Non |
> | les ressources | Non | Non |
> | starttenantbackfill | Non | Non |
> | tenantbackfillstatus | Non | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | accounts / eventgridfilters | Non | Non |
> | accounts / privateatlases | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Non | Non |
> | offers | Non | Non |
> | offertypes | Non | Non |
> | offertypes / publishers | Non | Non |
> | offertypes / publishers / offers | Non | Non |
> | offertypes / publishers / offers / plans | Non | Non |
> | offertypes / publishers / offers / plans / agreements | Non | Non |
> | offertypes / publishers / offers / plans / configs | Non | Non |
> | offertypes / publishers / offers / plans / configs / importimage | Non | Non |
> | opérations | Non | Non |
> | privategalleryitems | Non | Non |
> | privatestoreclient | Non | Non |
> | privatestores | Non | Non |
> | privatestores / offers | Non | Non |
> | products | Non | Non |
> | publishers | Non | Non |
> | publishers / offers | Non | Non |
> | publishers / offers / amendments | Non | Non |
> | inscription | Non | Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Non | Non |
> | listcommunicationpreference | Non | Non |
> | opérations | Non | Non |
> | updatecommunicationpreference | Non | Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | agreements | Non | Non |
> | offertypes | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | mediaservices | Oui | Oui |
> | mediaservices / accountfilters | Non | Non |
> | mediaservices / assets | Non | Non |
> | mediaservices / assets / assetfilters | Non | Non |
> | mediaservices / contentkeypolicies | Non | Non |
> | mediaservices / eventgridfilters | Non | Non |
> | mediaservices / liveeventoperations | Non | Non |
> | mediaservices / liveevents | Oui | Oui |
> | mediaservices / liveevents / liveoutputs | Non | Non |
> | mediaservices / liveoutputoperations | Non | Non |
> | mediaservices / streamingendpointoperations | Non | Non |
> | mediaservices / streamingendpoints | Oui | Oui |
> | mediaservices / streaminglocators | Non | Non |
> | mediaservices / streamingpolicies | Non | Non |
> | mediaservices / transforms | Non | Non |
> | mediaservices / transforms / jobs | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | appclusters | Non | Non |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Non | Non |
> | locations | Non | Non |
> | locations / assessmentoptions | Non | Non |
> | locations / checknameavailability | Non | Non |
> | migrateprojects | Non | Non |
> | movecollections | Non | Non |
> | opérations | Non | Non |
> | projects | Non | Non |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Non | Non |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | objectunderstandingaccounts | Non | Non |
> | opérations | Non | Non |
> | remoterenderingaccounts | Oui | Oui |
> | spatialanchorsaccounts | Oui | Oui |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Non | Non |
> | netappaccounts / backuppolicies | Non | Non |
> | netappaccounts / capacitypools | Non | Non |
> | netappaccounts / capacitypools / volumes | Non | Non |
> | netappaccounts / capacitypools / volumes / mounttargets | Non | Non |
> | netappaccounts / capacitypools / volumes / snapshots | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Non | Non |
> | applicationgatewayavailableresponseheaders | Non | Non |
> | applicationgatewayavailableservervariables | Non | Non |
> | applicationgatewayavailablessloptions | Non | Non |
> | applicationgatewayavailablewafrulesets | Non | Non |
> | applicationgateways | Non | Non |
> | applicationgatewaywebapplicationfirewallpolicies | Non | Non |
> | applicationsecuritygroups | Oui | Oui |
> | azurefirewallfqdntags | Non | Non |
> | azurefirewalls | Non | Non |
> | bastionhosts | Non | Non |
> | bgpservicecommunities | Non | Non |
> | checkfrontdoornameavailability | Non | Non |
> | checktrafficmanagernameavailability | Non | Non |
> | connections | Oui | Oui |
> | ddoscustompolicies | Oui | Oui |
> | ddosprotectionplans | Non | Non |
> | dnsoperationresults | Non | Non |
> | dnsoperationstatuses | Non | Non |
> | dnszones | Oui | Oui |
> | dnszones / a | Non | Non |
> | dnszones / aaaa | Non | Non |
> | dnszones / all | Non | Non |
> | dnszones / caa | Non | Non |
> | dnszones / cname | Non | Non |
> | dnszones / mx | Non | Non |
> | dnszones / ns | Non | Non |
> | dnszones / ptr | Non | Non |
> | dnszones / recordsets | Non | Non |
> | dnszones / soa | Non | Non |
> | dnszones / srv | Non | Non |
> | dnszones / txt | Non | Non |
> | expressroutecircuits | Non | Non |
> | expressroutegateways | Non | Non |
> | expressrouteserviceproviders | Non | Non |
> | firewallpolicies | Oui | Oui |
> | frontdooroperationresults | Non | Non |
> | frontdoors | Non | Non |
> | frontdoors / frontendendpoints | Non | Non |
> | frontdoorwebapplicationfirewallmanagedrulesets | Non | Non |
> | frontdoorwebapplicationfirewallpolicies | Non | Non |
> | getdnsresourcereference | Non | Non |
> | internalnotify | Non | Non |
> | ipallocations | Oui | Oui |
> | ipgroups | Oui | Oui |
> | loadbalancers | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
> | localnetworkgateways | Oui | Oui |
> | locations | Non | Non |
> | locations / autoapprovedprivatelinkservices | Non | Non |
> | locations / availabledelegations | Non | Non |
> | locations / availableprivateendpointtypes | Non | Non |
> | locations / availableservicealiases | Non | Non |
> | locations / baremetaltenants | Non | Non |
> | locations / batchnotifyprivateendpointsforresourcemove | Non | Non |
> | locations / batchvalidateprivateendpointsforresourcemove | Non | Non |
> | locations / checkacceleratednetworkingsupport | Non | Non |
> | locations / checkdnsnameavailability | Non | Non |
> | locations / checkprivatelinkservicevisibility | Non | Non |
> | locations / commitinternalazurenetworkmanagerconfiguration | Non | Non |
> | locations / effectiveresourceownership | Non | Non |
> | locations / nfvoperationresults | Non | Non |
> | locations / nfvoperations | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operations | Non | Non |
> | locations / servicetags | Non | Non |
> | locations / setresourceownership | Non | Non |
> | locations / supportedvirtualmachinesizes | Non | Non |
> | locations / usages | Non | Non |
> | locations / validateresourceownership | Non | Non |
> | locations / virtualnetworkavailableendpointservices | Non | Non |
> | natgateways | Non | Non  |
> | networkexperimentprofiles | Non | Non |
> | networkintentpolicies | Oui | Oui |
> | networkinterfaces | Oui | Oui |
> | networkprofiles | Non | Non |
> | networksecuritygroups | Oui | Oui |
> | networkwatchers | Oui | Non |
> | networkwatchers / connectionmonitors | Oui | Non |
> | networkwatchers / flowlogs | Oui | Non |
> | networkwatchers / pingmeshes | Oui | Non |
> | opérations | Non | Non |
> | p2svpngateways | Non | Non |
> | privatednsoperationresults | Non | Non |
> | privatednsoperationstatuses | Non | Non |
> | privatednszones | Oui | Oui |
> | privatednszones / a | Non | Non |
> | privatednszones / aaaa | Non | Non |
> | privatednszones / all | Non | Non |
> | privatednszones / cname | Non | Non |
> | privatednszones / mx | Non | Non |
> | privatednszones / ptr | Non | Non |
> | privatednszones / soa | Non | Non |
> | privatednszones / srv | Non | Non |
> | privatednszones / txt | Non | Non |
> | privatednszones / virtualnetworklinks | Oui | Oui |
> | privatednszonesinternal | Non | Non |
> | privateendpointredirectmaps | Non | Non |
> | privateendpoints | Oui | Oui |
> | privatelinkservices | Non | Non |
> | publicipaddresses | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
> | publicipprefixes | Oui | Oui |
> | routefilters | Non | Non |
> | routetables | Oui | Oui |
> | securitypartnerproviders | Oui | Oui |
> | serviceendpointpolicies | Oui | Oui |
> | trafficmanagergeographichierarchies | Non | Non |
> | trafficmanagerprofiles | Oui | Oui |
> | trafficmanagerprofiles / heatmaps | Non | Non |
> | trafficmanagerusermetricskeys | Non | Non |
> | virtualhubs | Non | Non |
> | virtualnetworkgateways | Oui | Oui |
> | virtualnetworks | Oui | Oui |
> | virtualnetworktaps | Non | Non |
> | virtualrouters | Oui | Oui |
> | virtualwans | Non | Non |
> | vpngateways (Virtual WAN) | Non | Non |
> | vpnserverconfigurations | Non | Non |
> | vpnsites (Virtual WAN) | Non | Non |

> [!IMPORTANT]
> Consultez l’[Aide pour le déplacement du réseau](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | checknamespaceavailability | Non | Non |
> | espaces de noms | Oui | Oui |
> | namespaces / notificationhubs | Oui | Oui |
> | operationresults | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Oui | Oui |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | hypervsites | Non | Non |
> | importsites | Non | Non |
> | opérations | Non | Non |
> | serversites | Non | Non |
> | vmwaresites | Non | Non |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Non | Non |
> | deletedworkspaces | Non | Non |
> | linktargets | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | storageinsightconfigs | Non | Non |
> | workspaces | Oui | Oui |
> | workspaces / datasources | Non | Non |
> | workspaces / linkedservices | Non | Non |
> | workspaces / linkedstorageaccounts | Non | Non |
> | workspaces / metadata | Non | Non |
> | workspaces / query | Non | Non |
> | workspaces / scopedprivatelinkproxies | Non | Non |

> [!IMPORTANT]
> Vérifiez que le déplacement vers un nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Les espaces de travail qui ont un compte Automation lié ne peuvent pas être déplacés. Avant de commencer une opération de déplacement, veillez à dissocier tous les comptes Automation.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | managementassociations | Non | Non |
> | managementconfigurations | Oui | Oui |
> | opérations | Non | Non |
> | solutions | Oui | Oui |
> | views | Oui | Oui |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Non | Non |
> | legacypeerings | Non | Non |
> | opérations | Non | Non |
> | peerasns | Non | Non |
> | peeringlocations | Non | Non |
> | peerings | Non | Non |
> | peeringservicecountries | Non | Non |
> | peeringservicelocations | Non | Non |
> | peeringserviceproviders | Non | Non |
> | peeringservices | Non | Non |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Non | Non |
> | opérations | Non | Non |
> | policyevents | Non | Non |
> | policystates | Non | Non |
> | policytrackedresources | Non | Non |
> | remediations | Non | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | consoles | Non | Non |
> | dashboards | Oui | Oui |
> | locations | Non | Non |
> | locations / consoles | Non | Non |
> | locations / usersettings | Non | Non |
> | opérations | Non | Non |
> | usersettings | Non | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | workspacecollections | Oui | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | capacities | Oui | Oui |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |
> | checknameavailability | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Non | Non |
> | providerregistrations | Non | Non |
> | providerregistrations / resourcetyperegistrations | Non | Non |
> | rollouts | Non | Non |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | workspaces | Non | Non |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Non | Non |
> | locations | Non | Non |
> | locations / allocatedstamp | Non | Non |
> | locations / allocatestamp | Non | Non |
> | locations / backupaadproperties | Non | Non |
> | locations / backupcrossregionrestore | Non | Non |
> | locations / backupcrrjob | Non | Non |
> | locations / backupcrrjobs | Non | Non |
> | locations / backupcrroperationresults | Non | Non |
> | locations / backupcrroperationsstatus | Non | Non |
> | locations / backupprevalidateprotection | Non | Non |
> | locations / backupstatus | Non | Non |
> | locations / backupvalidatefeatures | Non | Non |
> | locations / checknameavailability | Non | Non |
> | opérations | Non | Non |
> | replicationeligibilityresults | Non | Non |
> | vaults | Oui | Oui |

> [!IMPORTANT]
> Voir [Conseils sur le déplacement de Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationsstatus | Non | Non |
> | openshiftclusters | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | espaces de noms | Oui | Oui |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / hybridconnections | Non | Non |
> | namespaces / hybridconnections / authorizationrules | Non | Non |
> | namespaces / privateendpointconnections | Non | Non |
> | namespaces / wcfrelays | Non | Non |
> | namespaces / wcfrelays / authorizationrules | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | opérations | Non | Non |
> | queries | Oui | Oui |
> | resourcechangedetails | Non | Non |
> | resourcechanges | Non | Non |
> | les ressources | Non | Non |
> | resourceshistory | Non | Non |
> | subscriptionsstatus | Non | Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Non | Non |
> | childavailabilitystatuses | Non | Non |
> | childresources | Non | Non |
> | emergingissues | Non | Non |
> | événements | Non | Non |
> | metadata | Non | Non |
> | Notifications | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Non | Non |
> | checkpolicycompliance | Non | Non |
> | checkresourcename | Non | Non |
> | deployments | Non | Non |
> | deployments / operations | Non | Non |
> | deploymentscripts | Non | Non |
> | deploymentscripts / logs | Non | Non |
> | liens | Non | Non |
> | locations | Non | Non |
> | locations / deploymentscriptoperationresults | Non | Non |
> | notifyresourcejobs | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | fournisseurs | Non | Non |
> | resourcegroups | Non | Non |
> | les ressources | Non | Non |
> | subscriptions | Non | Non |
> | subscriptions / locations | Non | Non |
> | subscriptions / operationresults | Non | Non |
> | subscriptions / providers | Non | Non |
> | subscriptions / resourcegroups | Non | Non |
> | subscriptions / resourcegroups / resources | Non | Non |
> | subscriptions / resources | Non | Non |
> | subscriptions / tagnames | Non | Non |
> | subscriptions / tagnames / tagvalues | Non | Non |
> | tags | Non | Non |
> | templatespecs | Non | Non |
> | templatespecs / versions | Non | Non |
> | tenants | Non | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applications | Oui | Non |
> | checkmoderneligibility | Non | Non |
> | checknameavailability | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | saasresources | Non | Non |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | checkservicenameavailability | Non | Non |
> | opérations | Non | Non |
> | resourcehealthmetadata | Non | Non |
> | searchservices | Oui | Oui |

> [!IMPORTANT]
> Vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche dans des régions différentes. Déplacez-les plutôt dans des opérations distinctes.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Non | Non |
> | advancedthreatprotectionsettings | Non | Non |
> | alertes | Non | Non |
> | allowedconnections | Non | Non |
> | applicationwhitelistings | Non | Non |
> | assessmentmetadata | Non | Non |
> | assessments | Non | Non |
> | autodismissalertsrules | Non | Non |
> | automations | Oui | Oui |
> | autoprovisioningsettings | Non | Non |
> | complianceresults | Non | Non |
> | compliances | Non | Non |
> | datacollectionagents | Non | Non |
> | devicesecuritygroups | Non | Non |
> | discoveredsecuritysolutions | Non | Non |
> | externalsecuritysolutions | Non | Non |
> | informationprotectionpolicies | Non | Non |
> | iotsecuritysolutions | Oui | Oui |
> | iotsecuritysolutions / analyticsmodels | Non | Non |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Non | Non |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Non | Non |
> | jitnetworkaccesspolicies | Non | Non |
> | locations | Non | Non |
> | locations / alerts | Non | Non |
> | locations / allowedconnections | Non | Non |
> | locations / applicationwhitelistings | Non | Non |
> | locations / discoveredsecuritysolutions | Non | Non |
> | locations / externalsecuritysolutions | Non | Non |
> | locations / jitnetworkaccesspolicies | Non | Non |
> | locations / securitysolutions | Non | Non |
> | locations / securitysolutionsreferencedata | Non | Non |
> | locations / tasks | Non | Non |
> | locations / topologies | Non | Non |
> | opérations | Non | Non |
> | stratégies | Non | Non |
> | pricings | Non | Non |
> | regulatorycompliancestandards | Non | Non |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Non | Non |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Non | Non |
> | securitycontacts | Non | Non |
> | securitysolutions | Non | Non |
> | securitysolutionsreferencedata | Non | Non |
> | securitystatuses | Non | Non |
> | securitystatusessummaries | Non | Non |
> | servervulnerabilityassessments | Non | Non |
> | paramètres | Non | Non |
> | subassessments | Non | Non |
> | tâches | Non | Non |
> | topologies | Non | Non |
> | workspacesettings | Non | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | aggregations | Non | Non |
> | alertrules | Non | Non |
> | alertruletemplates | Non | Non |
> | automationrules | Non | Non |
> | bookmarks | Non | Non |
> | cas | Non | Non |
> | dataconnectors | Non | Non |
> | dataconnectorscheckrequirements | Non | Non |
> | entities | Non | Non |
> | entityqueries | Non | Non |
> | incidents | Non | Non |
> | officeconsents | Non | Non |
> | opérations | Non | Non |
> | paramètres | Non | Non |
> | threatintelligence | Non | Non |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | consoleservices | Non | Non |
> | locations | Non | Non |
> | locations / consoleservices | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | gateways | Non | Non |
> | nœuds | Non | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | checknamespaceavailability | Non | Non |
> | locations | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | espaces de noms | Oui | Oui |
> | namespaces / authorizationrules | Non | Non |
> | namespaces / disasterrecoveryconfigs | Non | Non |
> | namespaces / disasterrecoveryconfigs / checknameavailability | Non | Non |
> | namespaces / eventgridfilters | Non | Non |
> | namespaces / networkrulesets | Non | Non |
> | namespaces / queues | Non | Non |
> | namespaces / queues / authorizationrules | Non | Non |
> | namespaces / topics | Non | Non |
> | namespaces / topics / authorizationrules | Non | Non |
> | namespaces / topics / subscriptions | Non | Non |
> | namespaces / topics / subscriptions / rules | Non | Non |
> | opérations | Non | Non |
> | premiummessagingregions | Non | Non |
> | sku | Non | Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applications | Non | Non |
> | clusters | Oui | Oui |
> | clusters / applications | Non | Non |
> | containergroups | Non | Non |
> | containergroupsets | Non | Non |
> | edgeclusters | Non | Non |
> | locations | Non | Non |
> | locations / clusterversions | Non | Non |
> | locations / environments | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operations | Non | Non |
> | managedclusters | Non | Non |
> | networks | Non | Non |
> | opérations | Non | Non |
> | secretstores | Non | Non |
> | volumes | Non | Non |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applications | Oui | Oui |
> | containergroups | Non | Non |
> | gateways | Oui | Oui |
> | locations | Non | Non |
> | locations / applicationoperations | Non | Non |
> | locations / gatewayoperations | Non | Non |
> | locations / networkoperations | Non | Non |
> | locations / secretoperations | Non | Non |
> | locations / volumeoperations | Non | Non |
> | networks | Oui | Oui |
> | opérations | Non | Non |
> | secrets | Oui | Oui |
> | volumes | Oui | Oui |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | rollouts | Non | Non |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operationstatuses | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |
> | signalr | Oui | Oui |
> | signalr / eventgridfilters | Non | Non |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Non | Non |
> | applications | Non | Non |
> | jitrequests | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | instancepools | Non | Non |
> | locations | Oui | Oui |
> | locations / administratorazureasyncoperation | Non | Non |
> | locations / administratoroperationresults | Non | Non |
> | locations / auditingsettingsazureasyncoperation | Non | Non |
> | locations / auditingsettingsoperationresults | Non | Non |
> | locations / capabilities | Non | Non |
> | locations / databaseazureasyncoperation | Non | Non |
> | locations / databaseoperationresults | Non | Non |
> | locations / databaserestoreazureasyncoperation | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / deletevirtualnetworkorsubnetsazureasyncoperation | Non | Non |
> | locations / deletevirtualnetworkorsubnetsoperationresults | Non | Non |
> | locations / dnsaliasasyncoperation | Non | Non |
> | locations / dnsaliasoperationresults | Non | Non |
> | locations / elasticpoolazureasyncoperation | Non | Non |
> | locations / elasticpooloperationresults | Non | Non |
> | locations / encryptionprotectorazureasyncoperation | Non | Non |
> | locations / encryptionprotectoroperationresults | Non | Non |
> | locations / extendedauditingsettingsazureasyncoperation | Non | Non |
> | locations / extendedauditingsettingsoperationresults | Non | Non |
> | locations / failovergroupazureasyncoperation | Non | Non |
> | locations / failovergroupoperationresults | Non | Non |
> | locations / firewallrulesazureasyncoperation | Non | Non |
> | locations / firewallrulesoperationresults | Non | Non |
> | locations / instancefailovergroupazureasyncoperation | Non | Non |
> | locations / instancefailovergroupoperationresults | Non | Non |
> | locations / instancefailovergroups | Non | Non |
> | locations / instancepoolazureasyncoperation | Non | Non |
> | locations / instancepooloperationresults | Non | Non |
> | locations / jobagentazureasyncoperation | Non | Non |
> | locations / jobagentoperationresults | Non | Non |
> | locations / longtermretentionbackupazureasyncoperation | Non | Non |
> | locations / longtermretentionbackupoperationresults | Non | Non |
> | locations / longtermretentionbackups | Non | Non |
> | locations / longtermretentionmanagedinstancebackupazureasyncoperation | Non | Non |
> | locations / longtermretentionmanagedinstancebackupoperationresults | Non | Non |
> | locations / longtermretentionmanagedinstancebackups | Non | Non |
> | locations / longtermretentionmanagedinstances | Non | Non |
> | locations / longtermretentionpolicyazureasyncoperation | Non | Non |
> | locations / longtermretentionpolicyoperationresults | Non | Non |
> | locations / longtermretentionservers | Non | Non |
> | locations / manageddatabaseazureasyncoperation | Non | Non |
> | locations / manageddatabasecompleterestoreazureasyncoperation | Non | Non |
> | locations / manageddatabasecompleterestoreoperationresults | Non | Non |
> | locations / manageddatabaseoperationresults | Non | Non |
> | locations / manageddatabaserestoreazureasyncoperation | Non | Non |
> | locations / manageddatabaserestoreoperationresults | Non | Non |
> | locations / managedinstanceazureasyncoperation | Non | Non |
> | locations / managedinstanceencryptionprotectorazureasyncoperation | Non | Non |
> | locations / managedinstanceencryptionprotectoroperationresults | Non | Non |
> | locations / managedinstancekeyazureasyncoperation | Non | Non |
> | locations / managedinstancekeyoperationresults | Non | Non |
> | locations / managedinstancelongtermretentionpolicyazureasyncoperation | Non | Non |
> | locations / managedinstancelongtermretentionpolicyoperationresults | Non | Non |
> | locations / managedinstanceoperationresults | Non | Non |
> | locations / managedinstancetdecertazureasyncoperation | Non | Non |
> | locations / managedinstancetdecertoperationresults | Non | Non |
> | locations / managedserversecurityalertpoliciesazureasyncoperation | Non | Non |
> | locations / managedserversecurityalertpoliciesoperationresults | Non | Non |
> | locations / managedshorttermretentionpolicyazureasyncoperation | Non | Non |
> | locations / managedshorttermretentionpolicyoperationresults | Non | Non |
> | locations / notifyazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionoperationresults | Non | Non |
> | locations / privateendpointconnectionproxyazureasyncoperation | Non | Non |
> | locations / privateendpointconnectionproxyoperationresults | Non | Non |
> | locations / securityalertpoliciesazureasyncoperation | Non | Non |
> | locations / securityalertpoliciesoperationresults | Non | Non |
> | locations / serveradministratorazureasyncoperation | Non | Non |
> | locations / serveradministratoroperationresults | Non | Non |
> | locations / serverazureasyncoperation | Non | Non |
> | locations / serverkeyazureasyncoperation | Non | Non |
> | locations / serverkeyoperationresults | Non | Non |
> | locations / serveroperationresults | Non | Non |
> | locations / shorttermretentionpolicyazureasyncoperation | Non | Non |
> | locations / shorttermretentionpolicyoperationresults | Non | Non |
> | locations / syncagentoperationresults | Non | Non |
> | locations / syncdatabaseids | Non | Non |
> | locations / syncgroupoperationresults | Non | Non |
> | locations / syncmemberoperationresults | Non | Non |
> | locations / tdecertazureasyncoperation | Non | Non |
> | locations / tdecertoperationresults | Non | Non |
> | locations / usages | Non | Non |
> | locations / virtualclusterazureasyncoperation | Non | Non |
> | locations / virtualclusteroperationresults | Non | Non |
> | locations / virtualnetworkrulesazureasyncoperation | Non | Non |
> | locations / virtualnetworkrulesoperationresults | Non | Non |
> | locations / vulnerabilityassessmentscanazureasyncoperation | Non | Non |
> | locations / vulnerabilityassessmentscanoperationresults | Non | Non |
> | managedinstances | Non | Non |
> | managedinstances / administrators | Non | Non |
> | managedinstances / databases | Non | Non |
> | managedinstances / databases / backuplongtermretentionpolicies | Non | Non |
> | managedinstances / databases / vulnerabilityassessments | Non | Non |
> | managedinstances / metricdefinitions | Non | Non |
> | managedinstances / metrics | Non | Non |
> | managedinstances / recoverabledatabases | Non | Non |
> | managedinstances / tdecertificates | Non | Non |
> | managedinstances / vulnerabilityassessments | Non | Non |
> | opérations | Non | Non |
> | servers | Oui | Oui |
> | servers / administratoroperationresults | Non | Non |
> | servers / administrators | Non | Non |
> | servers / advisors | Non | Non |
> | servers / aggregateddatabasemetrics | Non | Non |
> | servers / auditingpolicies | Non | Non |
> | servers / auditingsettings | Non | Non |
> | servers / automatictuning | Non | Non |
> | servers / communicationlinks | Non | Non |
> | servers / connectionpolicies | Non | Non |
> | servers / databases | Oui | Oui |
> | servers / databases / advisors | Non | Non |
> | servers / databases / auditingpolicies | Non | Non |
> | servers / databases / auditingsettings | Non | Non |
> | servers / databases / auditrecords | Non | Non |
> | servers / databases / automatictuning | Non | Non |
> | servers / databases / backuplongtermretentionpolicies | Non | Non |
> | servers / databases / backupshorttermretentionpolicies | Non | Non |
> | servers / databases / connectionpolicies | Non | Non |
> | servers / databases / datamaskingpolicies | Non | Non |
> | servers / databases / datamaskingpolicies / rules | Non | Non |
> | servers / databases / extensions | Non | Non |
> | servers / databases / geobackuppolicies | Non | Non |
> | servers / databases / metricdefinitions | Non | Non |
> | servers / databases / metrics | Non | Non |
> | servers / databases / recommendedsensitivitylabels | Non | Non |
> | servers / databases / securityalertpolicies | Non | Non |
> | servers / databases / syncgroups | Non | Non |
> | servers / databases / syncgroups / syncmembers | Non | Non |
> | servers / databases / topqueries | Non | Non |
> | servers / databases / topqueries / querytext | Non | Non |
> | servers / databases / transparentdataencryption | Non | Non |
> | servers / databases / vulnerabilityassessment | Non | Non |
> | servers / databases / vulnerabilityassessments | Non | Non |
> | servers / databases / vulnerabilityassessmentscans | Non | Non |
> | servers / databases / vulnerabilityassessmentsettings | Non | Non |
> | servers / databases / workloadgroups | Non | Non |
> | servers / databasesecuritypolicies | Non | Non |
> | servers / disasterrecoveryconfiguration | Non | Non |
> | servers / dnsaliases | Non | Non |
> | servers / elasticpoolestimates | Non | Non |
> | servers / elasticpools | Oui | Oui |
> | servers / elasticpools / advisors | Non | Non |
> | servers / elasticpools / metricdefinitions | Non | Non |
> | servers / elasticpools / metrics | Non | Non |
> | servers / encryptionprotector | Non | Non |
> | servers / extendedauditingsettings | Non | Non |
> | servers / failovergroups | Non | Non |
> | servers / import | Non | Non |
> | servers / importexportoperationresults | Non | Non |
> | servers / jobaccounts | Oui | Oui |
> | servers / jobagents | Oui | Oui |
> | servers / jobagents / jobs | Non | Non |
> | servers / jobagents / jobs / executions | Non | Non |
> | servers / jobagents / jobs / steps | Non | Non |
> | servers / keys | Non | Non |
> | servers / operationresults | Non | Non |
> | servers / recommendedelasticpools | Non | Non |
> | servers / recoverabledatabases | Non | Non |
> | servers / restorabledroppeddatabases | Non | Non |
> | servers / securityalertpolicies | Non | Non |
> | servers / serviceobjectives | Non | Non |
> | servers / syncagents | Non | Non |
> | servers / tdecertificates | Non | Non |
> | servers / usages | Non | Non |
> | servers / virtualnetworkrules | Non | Non |
> | servers / vulnerabilityassessments | Non | Non |
> | virtualclusters | Oui | Oui |

> [!IMPORTANT]
> La base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées. Ce comportement s’applique aux bases de données Azure SQL Database et Azure Synapse Analytics.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / availabilitygrouplisteneroperationresults | Non | Non |
> | locations / operationtypes | Non | Non |
> | locations / sqlvirtualmachinegroupoperationresults | Non | Non |
> | locations / sqlvirtualmachineoperationresults | Non | Non |
> | opérations | Non | Non |
> | sqlvirtualmachinegroups | Oui | Oui |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Non | Non |
> | sqlvirtualmachines | Oui | Oui |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | locations | Non | Non |
> | locations / asyncoperations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |
> | storageaccounts | Oui | Oui |
> | storageaccounts / blobservices | Non | Non |
> | storageaccounts / fileservices | Non | Non |
> | storageaccounts / listaccountsas | Non | Non |
> | storageaccounts / listservicesas | Non | Non |
> | storageaccounts / queueservices | Non | Non |
> | storageaccounts / services | Non | Non |
> | storageaccounts / services / metricdefinitions | Non | Non |
> | storageaccounts / tableservices | Non | Non |
> | usages | Non | Non |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | caches | Non | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / checknameavailability | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operations | Non | Non |
> | locations / workflows | Non | Non |
> | opérations | Non | Non |
> | storagesyncservices | Oui | Oui |
> | storagesyncservices / registeredservers | Non | Non |
> | storagesyncservices / syncgroups | Non | Non |
> | storagesyncservices / syncgroups / cloudendpoints | Non | Non |
> | storagesyncservices / syncgroups / serverendpoints | Non | Non |
> | storagesyncservices / workflows | Non | Non |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Non | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Non | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | managers | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | clusters | Non | Non |
> | locations | Non | Non |
> | locations / quotas | Non | Non |
> | opérations | Non | Non |
> | streamingjobs | Oui | Oui |

> [!IMPORTANT]
> Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | environments | Non | Non |
> | environments / eventsources | Non | Non |
> | instances | Non | Non |
> | instances / environments | Non | Non |
> | instances / environments / eventsources | Non | Non |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | annuler | Non | Non |
> | createsubscription | Non | Non |
> | enable | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | renommer | Non | Non |
> | subscriptiondefinitions | Non | Non |
> | subscriptionoperations | Non | Non |
> | subscriptions | Non | Non |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | operationresults | Non | Non |
> | opérations | Non | Non |
> | operationsstatus | Non | Non |
> | services | Non | Non |
> | services / problemclassifications | Non | Non |
> | supporttickets | Non | Non |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Non | Non |
> | opérations | Non | Non |
> | workspaces | Oui | Oui |
> | workspaces / bigdatapools | Oui | Oui |
> | workspaces / operationresults | Non | Non |
> | workspaces / operationstatuses | Non | Non |
> | workspaces / sqlpools | Oui | Oui |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | environments | Oui | Oui |
> | environments / accesspolicies | Non | Non |
> | environments / eventsources | Oui | Oui |
> | environments / referencedatasets | Oui | Oui |
> | opérations | Non | Non |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | stores | Oui | Oui |
> | stores / accesspolicies | Non | Non |
> | stores / services | Non | Non |
> | stores / services / tokens | Non | Non |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Non | Non |
> | imagetemplates / runoutputs | Non | Non |
> | locations | Non | Non |
> | locations / operations | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | account | Non | Non |
> | account / extension | Non | Non |
> | account / project | Non | Non |
> | checknameavailability | Non | Non |
> | opérations | Non | Non |

> [!IMPORTANT]
> Pour modifier l’abonnement pour Azure DevOps, consultez [Modifier l’abonnement Azure utilisé pour la facturation](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | arczones | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | resourcepools | Non | Non |
> | vcenters | Non | Non |
> | virtualmachines | Non | Non |
> | virtualmachinetemplates | Non | Non |
> | virtualnetworks | Non | Non |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Non | Non |
> | dedicatedcloudservices | Non | Non |
> | locations | Non | Non |
> | locations / availabilities | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / privateclouds | Non | Non |
> | locations / privateclouds / resourcepools | Non | Non |
> | locations / privateclouds / virtualmachinetemplates | Non | Non |
> | locations / privateclouds / virtualnetworks | Non | Non |
> | locations / usages | Non | Non |
> | opérations | Non | Non |
> | virtualmachines | Non | Non |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | périphériques | Non | Non |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | vnfs | Non | Non |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |
> | opérations | Non | Non |
> | plans | Non | Non |
> | registeredsubscriptions | Non | Non |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | availablestacks | Non | Non |
> | billingmeters | Non | Non |
> | certificates | Non | Oui |
> | checknameavailability | Non | Non |
> | connectiongateways | Oui | Oui |
> | connections | Oui | Oui |
> | customapis | Oui | Oui |
> | deletedsites | Non | Non |
> | deploymentlocations | Non | Non |
> | georegions | Non | Non |
> | hostingenvironments | Non | Non |
> | hostingenvironments / eventgridfilters | Non | Non |
> | hostingenvironments / multirolepools | Non | Non |
> | hostingenvironments / workerpools | Non | Non |
> | ishostingenvironmentnameavailable | Non | Non |
> | ishostnameavailable | Non | Non |
> | isusernameavailable | Non | Non |
> | kubeenvironments | Oui | Oui |
> | listsitesassignedtohostname | Non | Non |
> | locations | Non | Non |
> | locations / apioperations | Non | Non |
> | locations / connectiongatewayinstallations | Non | Non |
> | locations / deletedsites | Non | Non |
> | locations / deletevirtualnetworkorsubnets | Non | Non |
> | locations / extractapidefinitionfromwsdl | Non | Non |
> | locations / getnetworkpolicies | Non | Non |
> | locations / listwsdlinterfaces | Non | Non |
> | locations / managedapis | Non | Non |
> | locations / operationresults | Non | Non |
> | locations / operations | Non | Non |
> | locations / runtimes | Non | Non |
> | opérations | Non | Non |
> | publishingusers | Non | Non |
> | de films | Non | Non |
> | resourcehealthmetadata | Non | Non |
> | runtimes | Non | Non |
> | serverfarms | Oui | Oui |
> | serverfarms / eventgridfilters | Non | Non |
> | sites | Oui | Oui |
> | sites / eventgridfilters | Non | Non |
> | sites / hostnamebindings | Non | Non |
> | sites / networkconfig | Non | Non |
> | sites / premieraddons | Oui | Oui |
> | sites / slots | Oui | Oui |
> | sites / slots / eventgridfilters | Non | Non |
> | sites / slots / hostnamebindings | Non | Non |
> | sites / slots / networkconfig | Non | Non |
> | sourcecontrols | Non | Non |
> | staticsites | Non | Non |
> | validate | Non | Non |
> | verifyhostingenvironmentvnet | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | multipleactivationkeys | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | deviceservices | Non | Non |
> | opérations | Non | Non |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | locations | Non | Non |
> | locations / operationstatuses | Non | Non |
> | opérations | Non | Non |
> | workloads | Non | Non |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement |
> | ------------- | ----------- | ---------- |
> | components | Non | Non |
> | componentssummary | Non | Non |
> | monitorinstances | Non | Non |
> | monitorinstancessummary | Non | Non |
> | monitors | Non | Non |
> | notificationsettings | Non | Non |
> | opérations | Non | Non |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](move-resource-group-and-subscription.md).

Pour obtenir les mêmes données qu'un fichier de valeurs séparées par des virgules, téléchargez [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

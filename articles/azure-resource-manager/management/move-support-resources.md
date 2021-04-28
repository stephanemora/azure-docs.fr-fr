---
title: Prise en charge des opérations de déplacement par type de ressource
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources, un nouvel abonnement ou une nouvelle région.
ms.topic: conceptual
ms.date: 04/23/2021
ms.openlocfilehash: 90fd683a12dcc9f9d63003f1fd87332db5f2b9aa
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945986"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources

Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Il fournit également des informations sur les conditions spéciales à prendre en compte lors du déplacement d’une ressource.

> [!IMPORTANT]
> Dans la plupart des cas, une ressource enfant ne peut pas être déplacée indépendamment de sa ressource parente. Les ressources enfants ont un type de ressource au format `<resource-provider-namespace>/<parent-resource>/<child-resource>`. Par exemple, `Microsoft.ServiceBus/namespaces/queues` est une ressource enfant de `Microsoft.ServiceBus/namespaces`. Lorsque vous déplacez la ressource parente, la ressource enfant est automatiquement déplacée avec elle. Si vous ne voyez pas de ressource enfant dans cet article, vous pouvez supposer qu’elle est déplacée avec la ressource parente. Si la ressource parente ne prend pas en charge le déplacement, la ressource enfant ne peut pas être déplacée.

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
> - [Microsoft.IoTHub](#microsoftiothub)
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
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | Non | Non |  Non |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | Non | Non | Non |
> | diagnosticsettingscategories | Non | Non | Non |
> | privatelinkforazuread | Oui | Oui | Non |
> | tenants | Oui | Oui | Non |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | Non | Non | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | Non | Non | Non |
> | addsservices | Non | Non | Non |
> | agents | Non | Non | Non |
> | anonymousapiusers | Non | Non | Non |
> | configuration | Non | Non | Non |
> | logs | Non | Non | Non |
> | reports | Non | Non | Non |
> | servicehealthmetrics | Non | Non | Non |
> | services | Non | Non | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | configurations | Non | Non | Non |
> | generaterecommendations | Non | Non | Non |
> | metadata | Non | Non | Non |
> | de films | Non | Non | Non |
> | suppressions | Non | Non | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Oui | Oui | Non |
> | alertes | Non | Non | Non |
> | alertslist | Non | Non | Non |
> | alertsmetadata | Non | Non | Non |
> | alertssummary | Non | Non | Non |
> | alertssummarylist | Non | Non | Non |
> | smartdetectoralertrules | Oui | Oui | Non |
> | smartgroups | Non | Non | Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Oui | Oui | Non |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Un service Gestion des API dont la référence SKU définie est Consommation ne peut pas être déplacé.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | Non | Non | Non |
> | service | Oui | Oui | Oui, (utilisation d’un modèle) <br/><br/> [Déplacer la Gestion des API d'une région vers une autre](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Oui | Oui | Non |
> | configurationstores / eventgridfilters | Non | Non | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | spring | Oui | Oui | Non |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | Non | Non | Oui, (utilisation d’un modèle)<br/><br/> [Déplacer une application App Service vers une autre région](../../app-service/manage-move-across-regions.md) |
> | appidentities | Non | Non | Non |
> | gateways | Non | Non | Non |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Oui | Oui | Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | Non | Non | Non |
> | dataaliases | Non | Non | Non |
> | denyassignments | Non | Non | Non |
> | elevateaccess | Non | Non | Non |
> | findorphanroleassignments | Non | Non | Non |
> | locks | Non | Non | Non |
> | autorisations | Non | Non | Non |
> | policyassignments | Non | Non | Non |
> | policydefinitions | Non | Non | Non |
> | policysetdefinitions | Non | Non | Non |
> | privatelinkassociations | Non | Non | Non |
> | resourcemanagementprivatelinks | Non | Non | Non |
> | roleassignments | Non | Non | Non |
> | roleassignmentsusagemetrics | Non | Non | Non |
> | roledefinitions | Non | Non | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Les runbooks doivent exister dans le même groupe de ressources que le compte Automation.
>
> Pour plus d'informations, consultez [Déplacer votre compte Azure Automation vers un autre abonnement](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Oui | Oui | Oui, (utilisation d’un modèle) <br/><br/> [Utilisation de la géoréplication](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts / configurations | Oui | Oui | Non |
> | automationaccounts / runbooks | Oui | Oui | Non |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Oui | Oui | Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Oui | Oui | Non |
> | b2ctenants | Non | Non | Non |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollers | Non | Non | Non |
> | hybriddatamanagers | Non | Non | Non |
> | postgresinstances | Non | Non | Non |
> | sqlinstances | Non | Non | Non |
> | sqlmanagedinstances | Non | Non | Non |
> | sqlserverinstances | Non | Non | Non |
> | sqlserverregistrations | Oui | Oui | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | Non | Non | Non |
> | registrations | Oui | Oui | Non |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Non | Non | Non |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Oui | Oui | Les comptes Batch ne peuvent pas être déplacés directement d’une région à une autre, mais vous pouvez utiliser un modèle pour exporter un modèle, le modifier et déployer le modèle dans la nouvelle région. <br/><br/> Apprenez-en davantage sur le [déplacement d’un compte Batch d’une région à une autre](../../batch/best-practices.md#moving-batch-accounts-across-regions). |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | Non | Non | Non |
> | billingperiods | Non | Non | Non |
> | billingpermissions | Non | Non | Non |
> | billingproperty | Non | Non | Non |
> | billingroleassignments | Non | Non | Non |
> | billingroledefinitions | Non | Non | Non |
> | departments | Non | Non | Non |
> | enrollmentaccounts | Non | Non | Non |
> | invoices | Non | Non | Non |
> | transfers | Non | Non | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | Non | Non | Non |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | Non | Non | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | Non | Non | Non <br/><br/> Le réseau blockchain ne peut pas avoir de nœuds dans des régions différentes. |
> | cordamembers | Non | Non | Non |
> | watchers | Non | Non | Non |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | Non | Non | Non |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | Non | Non | Non |
> | blueprints | Non | Non | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Oui | Oui | Non |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Si l’instance du Cache Azure pour Redis est configurée avec un réseau virtuel, l’instance ne peut pas être déplacée vers un autre abonnement. Consultez [Limitations relatives au déplacement réseau](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | redis | Oui | Oui | Non |
> | redisenterprise | Non | Non | Non |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | Non | Non | Non |
> | calculateexchange | Non | Non | Non |
> | calculateprice | Non | Non | Non |
> | calculatepurchaseprice | Non | Non | Non |
> | catalogs | Non | Non | Non |
> | commercialreservationorders | Non | Non | Non |
> | change | Non | Non | Non |
> | reservationorders | Non | Non | Non |
> | reservations | Non | Non | Non |
> | les ressources | Non | Non | Non |
> | validatereservationorder | Non | Non | Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | Non | Non | Non |
> | cdnwebapplicationfirewallpolicies | Oui | Oui | Non |
> | edgenodes | Non | Non | Non |
> | profiles | Oui | Oui | Non |
> | profiles / endpoints | Oui | Oui | Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Oui | Oui | Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | Non | Non | Non |
> | domainnames | Oui | Non | Non |
> | quotas | Non | Non | Non |
> | resourcetypes | Non | Non | Non |
> | validatesubscriptionmoveavailability | Non | Non | Non |
> | virtualmachines | Oui | Oui | Non |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | Non | Non | Non |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | capabilities | Non | Non | Non |
> | expressroutecrossconnections | Non | Non | Non |
> | expressroutecrossconnections / peerings | Non | Non | Non |
> | gatewaysupporteddevices | Non | Non | Non |
> | networksecuritygroups | Non | Non | Non |
> | quotas | Non | Non | Non |
> | reservedips | Non | Non | Non |
> | virtualnetworks | Non | Non | Non |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | disks | Non | Non | Non |
> | images | Non | Non | Non |
> | osimages | Non | Non | Non |
> | osplatformimages | Non | Non | Non |
> | publicimages | Non | Non | Non |
> | quotas | Non | Non | Non |
> | storageaccounts | Oui | Non | Oui |
> | vmimages | Non | Non | Non |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | opérations | Non | Non | Non |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non |
> | Recherche cognitive | Oui | Oui | Pris en charge avec étapes manuelles.<br/><br/> Apprenez-en davantage sur le [déplacement de votre service Recherche cognitive Azure vers une autre région](../../search/search-howto-move-across-regions.md). |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | Non | Non | Non |
> | usageaggregates | Non | Non | Non |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Consultez [Conseils pour le déplacement de machines virtuelles](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Oui | Oui |  Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des groupes à haute disponibilité. |
> | diskaccesses | Non | Non | Non |
> | diskencryptionsets | Non | Non | Non |
> | disks | Oui | Oui | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des machines virtuelles Azure et des disques associés. |
> | galleries | Non | Non | Non |
> | galleries / images | Non | Non | Non |
> | galleries / images / versions | Non | Non | Non |
> | hostgroups | Non | Non | Non |
> | hostgroups / hosts | Non | Non | Non |
> | images | Oui | Oui | Non |
> | proximityplacementgroups | Oui | Oui | Non |
> | restorepointcollections | Non | Non | Non |
> | restorepointcollections / restorepoints | Non | Non | Non |
> | sharedvmextensions | Non | Non | Non |
> | sharedvmimages | Non | Non | Non |
> | sharedvmimages / versions | Non | Non | Non |
> | snapshots | Oui | Oui | Non |
> | sshpublickeys | Non | Non | Non |
> | virtualmachines | Oui | Oui | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des machines virtuelles Azure. |
> | virtualmachines / extensions | Oui | Oui | Non |
> | virtualmachinescalesets | Oui | Oui | Non |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | Non | Non | Non |
> | balances | Non | Non | Non |
> | budgets | Non | Non | Non |
> | charges | Non | Non | Non |
> | costtags | Non | Non | Non |
> | credits | Non | Non | Non |
> | événements | Non | Non | Non |
> | forecasts | Non | Non | Non |
> | lots | Non | Non | Non |
> | marketplaces | Non | Non | Non |
> | pricesheets | Non | Non | Non |
> | products | Non | Non | Non |
> | reservationdetails | Non | Non | Non |
> | reservationrecommendationdetails | Non | Non | Non |
> | reservationrecommendations | Non | Non | Non |
> | reservationsummaries | Non | Non | Non |
> | reservationtransactions | Non | Non | Non |
> | tags | Non | Non | Non |
> | tenants | Non | Non | Non |
> | terms | Non | Non | Non |
> | usagedetails | Non | Non | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | Non | Non | Non |
> | serviceassociationlinks | Non | Non | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | registries | Oui | Oui | Non |
> | registries / agentpools | Oui | Oui | Non |
> | registries / buildtasks | Oui | Oui | Non |
> | registries / replications | Oui | Oui | Non |
> | registries / tasks | Oui | Oui | Non |
> | registries / webhooks | Oui | Oui | Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | Non | Non | Non |
> | managedclusters | Non | Non | Non |
> | openshiftmanagedclusters | Non | Non | Non |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Non | Non | Non |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | alertes | Non | Non | Non |
> | billingaccounts | Non | Non | Non |
> | budgets | Non | Non | Non |
> | cloudconnectors | Non | Non | Non |
> | connecteurs | Oui | Oui | Non |
> | departments | Non | Non | Non |
> | dimensions | Non | Non | Non |
> | enrollmentaccounts | Non | Non | Non |
> | exports | Non | Non | Non |
> | externalbillingaccounts | Non | Non | Non |
> | forecast | Non | Non | Non |
> | query | Non | Non | Non |
> | inscription | Non | Non | Non |
> | reportconfigs | Non | Non | Non |
> | reports | Non | Non | Non |
> | paramètres | Non | Non | Non |
> | showbackrules | Non | Non | Non |
> | views | Non | Non | Non |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | hubs | Non | Non | Non |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | requêtes | Non | Non | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | associations | Non | Non | Non |
> | resourceproviders | Oui | Oui | Non |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | Non | Non | Non |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | Non | Non | Non |
> | databoxedgedevices | Non | Non | Non |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Non | Non | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | catalogs | Oui | Oui | Non |
> | datacatalogs | Non | Non | Non |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | Non | Non | Non |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | packages | Non | Non | Non |
> | plans | Non | Non | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | datafactories | Oui | Oui | Non |
> | factories | Oui | Oui | Non |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | Non | Non | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Non | Non | Non |
> | services / projects | Non | Non | Non |
> | slots | Non | Non | Non |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | Non | Non | Non |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | servers | Oui | Oui | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [Plus d’informations](../../postgresql/howto-move-regions-portal.md)<br/><br/> Si le service est provisionné avec un stockage de sauvegarde géoredondant, vous pouvez utiliser la géorestauration pour effectuer une restauration dans d’autres régions. [Plus d’informations](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage)

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Non | Non | Non |
> | servers | Oui | Oui | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [Plus d’informations](../../mysql/howto-move-regions-portal.md)

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | Non | Non | Non |
> | servergroups | Non | Non | Non |
> | servers | Oui | Oui | Vous pouvez utiliser un réplica en lecture sur plusieurs régions pour déplacer un serveur existant. [Plus d’informations](../../postgresql/howto-move-regions-portal.md)
> | serversv2 | Oui | Oui | Non |
> | singleservers | Oui | Oui | Non |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Oui | Oui | Non |
> | rollouts | Oui | Oui | Non |
> | servicetopologies | Oui | Oui | Non |
> | servicetopologies / services | Oui | Oui | Non |
> | servicetopologies / services / serviceunits | Oui | Oui | Non |
> | steps | Oui | Oui | Non |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Oui | Oui | Non |
> | hostpools | Oui | Oui | Non |
> | workspaces | Oui | Oui | Non |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | Non | Non | Non. La ressource n’est pas exposée. |
> | elasticpools / iothubtenants | Non | Non | Non. La ressource n’est pas exposée. |
> | iothubs | Oui | Oui | Oui. [En savoir plus](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Oui | Oui | Non |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | pipelines | Oui | Oui | Non |
> | controllers | **En attente** | **En attente** | Non |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | controllers | Oui | Oui | Non |
> | Cluster AKS | **En attente** | **En attente** | Non<br/><br/> [Apprenez-en davantage](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sur le déplacement vers une autre région.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | Non | Non | Non |
> | labs | Oui | Non | Non |
> | labs / environments | Oui | Oui | Non |
> | labs / servicerunners | Oui | Oui | Non |
> | labs / virtualmachines | Oui | Non | Non |
> | schedules | Oui | Oui | Non |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | Non | Non | Oui, en recréant des ressources dans une nouvelle région. [En savoir plus](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | Non | Non | Non |
> | databaseaccounts | Oui | Oui | Non |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | domaines | Oui | Oui | Non |
> | generatessorequest | Non | Non | Non |
> | topleveldomains | Non | Non | Non |
> | validatedomainregistrationinformation | Non | Non | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Oui | Oui | Non |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | domaines | Oui | Oui | Non |
> | eventsubscriptions | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) | Non |
> | extensiontopics | Non | Non | Non |
> | partnernamespaces | Oui | Oui | Non |
> | partnerregistrations | Non | Non | Non |
> | partnertopics | Oui | Oui | Non |
> | systemtopics | Oui | Oui | Non |
> | topics | Oui | Oui | Non |
> | topictypes | Non | Non | Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Oui | Oui | Non |
> | espaces de noms | Oui | Oui | Oui (avec modèle)<br/><br/> [Déplacer un espace de noms Event Hubs vers une autre région](../../event-hubs/move-across-regions.md) |
> | sku | Non | Non | Non |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | Non | Non | Non |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | espaces de noms | Oui | Oui | Non |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | Non | Non | Non |
> | features | Non | Non | Non |
> | fournisseurs | Non | Non | Non |
> | subscriptionfeatureregistrations | Non | Non | Non |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | Non | Non | Non |
> | automanagedvmconfigurationprofiles | Non | Non | Non |
> | guestconfigurationassignments | Non | Non | Non |
> | software | Non | Non | Non |
> | softwareupdateprofile | Non | Non | Non |
> | softwareupdates | Non | Non | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | Non | Non | Non |
> | sapmonitors | Non | Non | Non |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | Non | Non | Non |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.
>
> Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Oui | Oui | Non |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Oui | Oui | Non |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | machines | Oui | Oui | Non |
> | machines / extensions | Oui | Oui | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagers | Oui | Oui | Non |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | périphériques | Non | Non | Non |
> | vnfs | Non | Non | Non |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | components | Non | Non | Non |
> | networkscopes | Non | Non | Non |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | jobs | Oui | Oui | Non |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non. [Plus d’informations](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region) |
> | actiongroups | Oui | Oui | Non |
> | activitylogalerts | Non | Non | Non |
> | alertrules | Oui | Oui | Non |
> | autoscalesettings | Oui | Oui | Non |
> | baseline | Non | Non | Non |
> | components | Oui | Oui | Non |
> | datacollectionrules | Non | Non | Non |
> | diagnosticsettings | Non | Non | Non |
> | diagnosticsettingscategories | Non | Non | Non |
> | eventcategories | Non | Non | Non |
> | eventtypes | Non | Non | Non |
> | extendeddiagnosticsettings | Non | Non | Non |
> | guestdiagnosticsettings | Non | Non | Non |
> | listmigrationdate | Non | Non | Non |
> | logdefinitions | Non | Non | Non |
> | logprofiles | Non | Non | Non |
> | logs | Non | Non | Non |
> | metricalerts | Non | Non | Non |
> | metricbaselines | Non | Non | Non |
> | metricbatch | Non | Non | Non |
> | metricdefinitions | Non | Non | Non |
> | metricnamespaces | Non | Non | Non |
> | Mesures | Non | Non | Non |
> | migratealertrules | Non | Non | Non |
> | migratetonewpricingmodel | Non | Non | Non |
> | myworkbooks | Non | Non | Non |
> | notificationgroups | Non | Non | Non |
> | privatelinkscopes | Non | Non | Non |
> | rollbacktolegacypricingmodel | Non | Non | Non |
> | scheduledqueryrules | Oui | Oui | Non |
> | Topologie | Non | Non | Non |
> | transactions | Non | Non | Non |
> | vminsightsonboardingstatuses | Non | Non | Non |
> | webtests | Oui | Oui | Non |
> | webtests / gettestresultfile | Non | Non | Non |
> | workbooks | Oui | Oui | Non |
> | workbooktemplates | Oui | Oui | Non |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | Non | Non | Non |
> | iotapps | Oui | Oui | Non |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | Oui | Oui | Oui (cloner un hub) <br/><br/> [Cloner un hub IoT dans une autre région](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | graph | Oui | Oui | Non |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> Les coffres de clés utilisés pour le chiffrement de disques ne peuvent pas être déplacés vers des groupes de ressources situés dans le même abonnement ni d’un abonnement à l’autre.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | Non | Non | Non |
> | hsmpools | Non | Non | Non |
> | managedhsms | Non | Non | Non |
> | vaults | Oui | Oui | Non |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Oui | Oui | Non |
> | registeredsubscriptions | Non | Non | Non |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | Non | Non | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Oui | Oui | Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | Non | Non | Non |
> | users | Non | Non | Non |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non, il s’agit d’un service mondial. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | Non | Non | Non |
> | integrationaccounts | Oui | Oui | Non |
> | integrationserviceenvironments | Oui | Non | Non |
> | integrationserviceenvironments / managedapis | Oui | Non | Non |
> | isolatedenvironments | Non | Non | Non |
> | workflows | Oui | Oui | Non |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | Non | Non | Non |
> | webservices | Oui | Non | Non |
> | workspaces | Oui | Oui | Non |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | Non | Non | Non |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |
> | teamaccounts | Non | Non | Non |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Non | Non | Non |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | Non | Non | Oui. [En savoir plus](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Oui | Oui | Oui. [En savoir plus](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Non | Non | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | Identités | Non | Non | Non |
> | userassignedidentities | Non | Non | Non |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | Non | Non | Non |
> | managednetworks / managednetworkgroups | Non | Non | Non |
> | managednetworks / managednetworkpeeringpolicies | Non | Non | Non |
> | notification | Non | Non | Non |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | Non | Non | Non |
> | registrationassignments | Non | Non | Non |
> | registrationdefinitions | Non | Non | Non |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | Non | Non | Non |
> | managementgroups | Non | Non | Non |
> | managementgroups / settings | Non | Non | Non |
> | les ressources | Non | Non | Non |
> | starttenantbackfill | Non | Non | Non |
> | tenantbackfillstatus | Non | Non | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Oui | Oui | Non, Azure Maps est un service géospatial. |
> | accounts / privateatlases | Oui | Oui | Non |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | offers | Non | Non | Non |
> | offertypes | Non | Non | Non |
> | privategalleryitems | Non | Non | Non |
> | privatestoreclient | Non | Non | Non |
> | privatestores | Non | Non | Non |
> | products | Non | Non | Non |
> | publishers | Non | Non | Non |
> | inscription | Non | Non | Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | Non | Non | Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | agreements | Non | Non | Non |
> | offertypes | Non | Non | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | mediaservices | Oui | Oui | Non |
> | mediaservices / liveevents | Oui | Oui | Non |
> | mediaservices / streamingendpoints | Oui | Oui | Non |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | Non | Non | Non |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | Non | Non | Non |
> | migrateprojects | Non | Non | Non |
> | movecollections | Non | Non | Non |
> | projects | Non | Non | Non |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | Non | Non | Non |
> | objectunderstandingaccounts | Non | Non | Non |
> | remoterenderingaccounts | Oui | Oui | Non |
> | spatialanchorsaccounts | Oui | Oui | Non |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | Non | Non | Non |
> | netappaccounts / capacitypools | Non | Non | Non |
> | netappaccounts / capacitypools / volumes | Non | Non | Non |
> | netappaccounts / capacitypools / volumes / mounttargets | Non | Non | Non |
> | netappaccounts / capacitypools / volumes / snapshots | Non | Non | Non |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Consultez l’[Aide pour le déplacement du réseau](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | Non | Non | Non |
> | applicationgatewaywebapplicationfirewallpolicies | Non | Non | Non |
> | applicationsecuritygroups | Oui | Oui | Non |
> | azurefirewalls | Non | Non | Non |
> | bastionhosts | Non | Non | Non |
> | bgpservicecommunities | Non | Non | Non |
> | connections | Oui | Oui | Non |
> | ddoscustompolicies | Oui | Oui | Non |
> | ddosprotectionplans | Non | Non | Non |
> | dnszones | Oui | Oui | Non |
> | expressroutecircuits | Non | Non | Non |
> | expressroutegateways | Non | Non | Non |
> | expressrouteserviceproviders | Non | Non | Non |
> | firewallpolicies | Oui | Oui | Non |
> | frontdoors | Non | Non | Non |
> | ipallocations | Oui | Oui | Non |
> | ipgroups | Oui | Oui | Non |
> | loadbalancers | Oui - Référence SKU de base<br> Oui - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des équilibreurs de charge internes et externes. |
> | localnetworkgateways | Oui | Oui | Non |
> | natgateways | Non | Non | Non |
> | networkexperimentprofiles | Non | Non | Non |
> | networkintentpolicies | Oui | Oui | Non |
> | networkinterfaces | Oui | Oui | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des cartes réseau. |
> | networkprofiles | Non | Non | Non |
> | networksecuritygroups | Oui | Oui | Oui <br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des groupes de sécurité réseau (NGS). |
> | networkwatchers | Non | Non | Non |
> | networkwatchers / connectionmonitors | Oui | Non | Non |
> | networkwatchers / flowlogs | Oui | Non | Non |
> | networkwatchers / pingmeshes | Oui | Non | Non |
> | p2svpngateways | Non | Non | Non |
> | privatednszones | Oui | Oui | Non |
> | privatednszones / virtualnetworklinks | Oui | Oui | Non |
> | privatednszonesinternal | Non | Non | Non |
> | privateendpointredirectmaps | Non | Non | Non |
> | privateendpoints | Non | Non | Non |
> | privatelinkservices | Non | Non | Non |
> | publicipaddresses | Oui - Référence SKU de base<br>Oui - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui<br/><br/> Utilisez [Azure Resource Mover](../../resource-mover/tutorial-move-region-virtual-machines.md) pour déplacer des adresses IP publiques. |
> | publicipprefixes | Oui | Oui | Non |
> | routefilters | Non | Non | Non |
> | routetables | Oui | Oui | Non |
> | securitypartnerproviders | Oui | Oui | Non |
> | serviceendpointpolicies | Oui | Oui | Non |
> | trafficmanagergeographichierarchies | Non | Non | Non |
> | trafficmanagerprofiles | Oui | Oui | Non |
> | trafficmanagerprofiles / heatmaps | Non | Non | Non |
> | trafficmanagerusermetricskeys | Non | Non | Non |
> | virtualhubs | Non | Non | Non |
> | virtualnetworkgateways | Oui | Oui | Non |
> | virtualnetworks | Oui | Oui | Non |
> | virtualnetworktaps | Non | Non | Non |
> | virtualrouters | Oui | Oui | Non |
> | virtualwans | Non | Non |
> | vpngateways (Virtual WAN) | Non | Non | Non |
> | vpnserverconfigurations | Non | Non | Non |
> | vpnsites (Virtual WAN) | Non | Non | Non |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | espaces de noms | Oui | Oui | Non |
> | namespaces / notificationhubs | Oui | Oui | Non |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Oui | Oui | Non |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | Non | Non | Non |
> | importsites | Non | Non | Non |
> | serversites | Non | Non | Non |
> | vmwaresites | Non | Non | Non |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Vérifiez que le déplacement vers un nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Les espaces de travail qui ont un compte Automation lié ne peuvent pas être déplacés. Avant de commencer une opération de déplacement, veillez à dissocier tous les comptes Automation.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Non | Non | Non |
> | deletedworkspaces | Non | Non | Non |
> | linktargets | Non | Non | Non |
> | storageinsightconfigs | Non | Non | Non |
> | workspaces | Oui | Oui | Non |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | Non | Non | Non |
> | managementconfigurations | Oui | Oui | Non |
> | solutions | Oui | Oui | Non |
> | views | Oui | Oui | Non |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | Non | Non | Non |
> | peerasns | Non | Non | Non |
> | peeringlocations | Non | Non | Non |
> | peerings | Non | Non | Non |
> | peeringservicecountries | Non | Non | Non |
> | peeringservicelocations | Non | Non | Non |
> | peeringserviceproviders | Non | Non | Non |
> | peeringservices | Non | Non | Non |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | Non | Non | Non |
> | policystates | Non | Non | Non |
> | policytrackedresources | Non | Non | Non |
> | remediations | Non | Non | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | consoles | Non | Non | Non |
> | dashboards | Oui | Oui | Non |
> | usersettings | Non | Non | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Oui | Oui | Non |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | capacities | Oui | Oui | Non |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | Non | Non | Non |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | **En attente** | **En attente** | Non |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | Non | Non | Non |
> | providerregistrations | Non | Non | Non |
> | rollouts | Non | Non | Non |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Non | Non | Non |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Voir [Conseils sur le déplacement de Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | Non | Non | Non |
> | vaults | Oui | Oui | Non.<br/><br/> Le déplacement de coffres Recovery Services pour Sauvegarde Azure d’une région Azure à une autre n’est pas pris en charge.<br/><br/> Dans les coffres Recovery Services pour Azure Site Recovery, vous pouvez [désactiver et recréer le coffre](../../site-recovery/move-vaults-across-regions.md) dans la région cible. |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | Non | Non | Non |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | espaces de noms | Oui | Oui | Non |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | queries | Oui | Oui | Non |
> | resourcechangedetails | Non | Non | Non |
> | resourcechanges | Non | Non | Non |
> | les ressources | Non | Non | Non |
> | resourceshistory | Non | Non | Non |
> | subscriptionsstatus | Non | Non | Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | Non | Non | Non |
> | emergingissues | Non | Non | Non |
> | événements | Non | Non | Non |
> | metadata | Non | Non | Non |
> | Notifications | Non | Non | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | deployments | Non | Non | Non |
> | deploymentscripts | Non | Non | Oui<br/><br/>[Déplacer des ressources Microsoft.Resources vers une nouvelle région](microsoft-resources-move-regions.md) |
> | deploymentscripts / logs | Non | Non | Non |
> | liens | Non | Non | Non |
> | fournisseurs | Non | Non | Non |
> | resourcegroups | Non | Non | Non |
> | les ressources | Non | Non | Non |
> | subscriptions | Non | Non | Non |
> | tags | Non | Non | Non |
> | templatespecs | Non | Non | Oui<br/><br/>[Déplacer des ressources Microsoft.Resources vers une nouvelle région](microsoft-resources-move-regions.md) |
> | templatespecs / versions | Non | Non | Non |
> | tenants | Non | Non | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Oui | Non | Non |
> | saasresources | Non | Non | Non |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche dans des régions différentes. Déplacez-les plutôt dans des opérations distinctes.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | Non | Non | Non |
> | searchservices | Oui | Oui | Non |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | Non | Non | Non |
> | advancedthreatprotectionsettings | Non | Non | Non |
> | alertes | Non | Non | Non |
> | allowedconnections | Non | Non | Non |
> | applicationwhitelistings | Non | Non | Non |
> | assessmentmetadata | Non | Non | Non |
> | assessments | Non | Non | Non |
> | autodismissalertsrules | Non | Non | Non |
> | automations | Oui | Oui | Non |
> | autoprovisioningsettings | Non | Non | Non |
> | complianceresults | Non | Non | Non |
> | compliances | Non | Non | Non |
> | datacollectionagents | Non | Non | Non |
> | devicesecuritygroups | Non | Non | Non |
> | discoveredsecuritysolutions | Non | Non | Non |
> | externalsecuritysolutions | Non | Non | Non |
> | informationprotectionpolicies | Non | Non | Non |
> | iotsecuritysolutions | Oui | Oui | Non |
> | iotsecuritysolutions / analyticsmodels | Non | Non | Non |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Non | Non | Non |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Non | Non | Non |
> | jitnetworkaccesspolicies | Non | Non | Non |
> | stratégies | Non | Non | Non |
> | pricings | Non | Non | Non |
> | regulatorycompliancestandards | Non | Non | Non |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Non | Non | Non |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Non | Non | Non |
> | securitycontacts | Non | Non | Non |
> | securitysolutions | Non | Non | Non |
> | securitysolutionsreferencedata | Non | Non | Non |
> | securitystatuses | Non | Non | Non |
> | securitystatusessummaries | Non | Non | Non |
> | servervulnerabilityassessments | Non | Non | Non |
> | paramètres | Non | Non | Non |
> | subassessments | Non | Non | Non |
> | tâches | Non | Non | Non |
> | topologies | Non | Non | Non |
> | workspacesettings | Non | Non | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregations | Non | Non | Non |
> | alertrules | Non | Non | Non |
> | alertruletemplates | Non | Non | Non |
> | automationrules | Non | Non | Non |
> | bookmarks | Non | Non | Non |
> | cas | Non | Non | Non |
> | dataconnectors | Non | Non | Non |
> | entities | Non | Non | Non |
> | entityqueries | Non | Non | Non |
> | incidents | Non | Non | Non |
> | officeconsents | Non | Non | Non |
> | paramètres | Non | Non | Non |
> | threatintelligence | Non | Non | Non |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | Non | Non | Non |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | gateways | Non | Non | Non |
> | nœuds | Non | Non | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | espaces de noms | Oui | Oui | Non |
> | premiummessagingregions | Non | Non | Non |
> | sku | Non | Non | Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Non | Non | Non |
> | clusters | Oui | Oui | Non |
> | containergroups | Non | Non | Non |
> | containergroupsets | Non | Non | Non |
> | edgeclusters | Non | Non | Non |
> | managedclusters | Non | Non | Non |
> | networks | Non | Non | Non |
> | secretstores | Non | Non | Non |
> | volumes | Non | Non | Non |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applications | Oui | Oui | Non |
> | containergroups | Non | Non | Non |
> | gateways | Oui | Oui | Non |
> | networks | Oui | Oui | Non |
> | secrets | Oui | Oui | Non |
> | volumes | Oui | Oui | Non |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | rollouts | Non | Non | Non |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | signalr | Oui | Oui | Non |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | Non | Non | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | Non | Non | Non |
> | applications | Non | Non | Non |
> | jitrequests | Non | Non | Non |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> La base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées. Ce comportement s’applique aux bases de données Azure SQL Database et Azure Synapse Analytics.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | Non | Non | Non |
> | locations | Oui | Oui | Non |
> | managedinstances | Non | Non | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des instances managées entre les régions. |
> | managedinstances / databases | Non | Non | Oui |
> | servers | Oui | Oui |Oui |
> | servers / databases | Oui | Oui | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des bases de données entre les régions.<br/><br/> [Apprenez-en davantage](../../resource-mover/tutorial-move-region-sql.md) sur l’utilisation d’Azure Resource Mover pour déplacer des bases de données Azure SQL.  |
> | servers / databases / backuplongtermretentionpolicies | Oui | Oui | Non |
> | servers / elasticpools | Oui | Oui | Oui <br/><br/> [Apprenez-en davantage](../../azure-sql/database/move-resources-across-regions.md) sur le déplacement des pools élastiques entre les régions.<br/><br/> [Apprenez-en davantage](../../resource-mover/tutorial-move-region-sql.md) sur l’utilisation d’Azure Resource Mover pour déplacer des pools élastiques Azure SQL.  |
> | servers / jobaccounts | Oui | Oui | Non |
> | servers / jobagents | Oui | Oui | Non |
> | virtualclusters | Oui | Oui | Oui |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Oui | Oui | Non |
> | sqlvirtualmachines | Oui | Oui | Non |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Oui | Oui | Oui<br/><br/> [Déplacer un compte Stockage Azure vers une autre région](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | caches | Non | Non | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Oui | Oui | Non |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Non | Non | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Non | Non | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | managers | Non | Non | Non |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | clusters | Non | Non | Non |
> | streamingjobs | Oui | Oui | Non |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | Non | Non | Non |
> | instances | Non | Non | Non |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | subscriptions | Non | Non | Non |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Non | Non | Non |
> | supporttickets | Non | Non | Non |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workspaces | Non | Non | Non |
> | workspaces / bigdatapools | Non | Non | Non |
> | workspaces / sqlpools | Non | Non | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | environments | Oui | Oui | Non |
> | environments / eventsources | Oui | Oui | Non |
> | environments / referencedatasets | Oui | Oui | Non |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | stores | Oui | Oui | Non |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | Non | Non | Non |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!IMPORTANT]
> Pour modifier l’abonnement pour Azure DevOps, consultez [Modifier l’abonnement Azure utilisé pour la facturation](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | account | Non | Non | Non |
> | account / extension | Non | Non | Non |
> | account / project | Non | Non | Non |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | Non | Non | Non |
> | resourcepools | Non | Non | Non |
> | vcenters | Non | Non | Non |
> | virtualmachines | Non | Non | Non |
> | virtualmachinetemplates | Non | Non | Non |
> | virtualnetworks | Non | Non | Non |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | Non | Non | Non |
> | dedicatedcloudservices | Non | Non | Non |
> | virtualmachines | Non | Non | Non |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | périphériques | Non | Non | Non |
> | vnfs | Non | Non | Non |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Non | Non | Non |
> | plans | Non | Non | Non |
> | registeredsubscriptions | Non | Non | Non |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | Non | Non | Non |
> | billingmeters | Non | Non | Non |
> | certificates | Non | Oui | Non |
> | connectiongateways | Oui | Oui | Non |
> | connections | Oui | Oui | Non |
> | customapis | Oui | Oui | Non |
> | deletedsites | Non | Non | Non |
> | deploymentlocations | Non | Non | Non |
> | georegions | Non | Non | Non |
> | hostingenvironments | Non | Non | Non |
> | kubeenvironments | Oui | Oui | Non |
> | publishingusers | Non | Non | Non |
> | de films | Non | Non | Non |
> | resourcehealthmetadata | Non | Non | Non |
> | runtimes | Non | Non | Non |
> | serverfarms | Oui | Oui | Non |
> | serverfarms / eventgridfilters | Non | Non | Non |
> | sites | Oui | Oui | Non |
> | sites / premieraddons | Oui | Oui | Non |
> | sites / slots | Oui | Oui | Non |
> | sourcecontrols | Non | Non | Non |
> | staticsites | Non | Non | Non |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | Non | Non | Non |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | Non | Non | Non |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | workloads | Non | Non | Non |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Abonnement | Changement de région |
> | ------------- | ----------- | ---------- | ----------- |
> | components | Non | Non | Non |
> | componentssummary | Non | Non | Non |
> | monitorinstances | Non | Non | Non |
> | monitorinstancessummary | Non | Non | Non |
> | monitors | Non | Non | Non |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](move-resource-group-and-subscription.md).
- [En savoir plus](../../resource-mover/overview.md) sur le service de déplacement de ressources.
- Pour obtenir les mêmes données qu'un fichier de valeurs séparées par des virgules, téléchargez [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

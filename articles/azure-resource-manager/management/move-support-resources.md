---
title: Prise en charge des opérations de déplacement par type de ressource
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: da08775ed6c694b95ecec452507f94638091db0c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261056"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources
Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Il fournit également des informations sur les conditions spéciales à prendre en compte lors du déplacement d’une ressource.

Accédez à un espace de noms du fournisseur de ressources :
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
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
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Non | Non |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tenants | Non | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurations | Non | Non |
> | de films | Non | Non |
> | suppressions | Non | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Oui | Oui |
> | alertes | Non | Non |
> | alertssummary | Non | Non |
> | smartdetectoralertrules | Oui | Oui |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Oui | Oui |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | service | Oui | Oui |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Oui | Oui |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spring | Oui | Oui |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Non | Non |
> | appidentities | Non | Non |
> | gateways | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checkaccess | Non | Non |
> | denyassignments | Non | Non |
> | findorphanroleassignments | Non | Non |
> | locks | Non | Non |
> | autorisations | Non | Non |
> | policyassignments | Non | Non |
> | policydefinitions | Non | Non |
> | policysetdefinitions | Non | Non |
> | roleassignments | Non | Non |
> | roleassignmentsusagemetrics | Non | Non |
> | roledefinitions | Non | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Oui | Oui |
> | automationaccounts / configurations | Oui | Oui |
> | automationaccounts / runbooks | Oui | Oui |

> [!IMPORTANT]
> Les runbooks doivent exister dans le même groupe de ressources que le compte Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Oui | Oui |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Non | Non |
> | postgresinstances | Non | Non |
> | sqlbigdataclusters | Non | Non |
> | sqlinstances | Non | Non |
> | sqlserverregistrations | Oui | Oui |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrations | Oui | Oui |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Oui | Oui |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Non | Non |
> | fileservers | Non | Non |
> | jobs | Non | Non |
> | workspaces | Non | Non |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingperiods | Non | Non |
> | billingpermissions | Non | Non |
> | billingroleassignments | Non | Non |
> | billingroledefinitions | Non | Non |
> | createbillingroleassignment | Non | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Non | Non |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Non | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Non | Non |
> | watchers | Non | Non |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Non | Non |
> | blueprints | Non | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Oui | Oui |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Oui | Oui |

> [!IMPORTANT]
> Si l’instance du Cache Azure pour Redis est configurée avec un réseau virtuel, l’instance ne peut pas être déplacée vers un autre abonnement. Consultez [Limitations relatives au déplacement réseau](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Oui | Oui |
> | profiles | Oui | Oui |
> | profiles / endpoints | Oui | Oui |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Oui | Oui |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainnames | Oui | Non |
> | virtualmachines | Oui | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Non | Non |
> | reservedips | Non | Non |
> | virtualnetworks | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Oui | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Oui | Oui |
> | diskencryptionsets | Non | Non |
> | disks | Oui | Oui |
> | galleries | Non | Non |
> | galleries / images | Non | Non |
> | galleries / images / versions | Non | Non |
> | hostgroups | Non | Non |
> | hostgroups / hosts | Non | Non |
> | images | Oui | Oui |
> | proximityplacementgroups | Non | Non |
> | restorepointcollections | Non | Non |
> | sharedvmimages | Non | Non |
> | sharedvmimages / versions | Non | Non |
> | snapshots | Oui | Oui |
> | virtualmachines | Oui | Oui |
> | virtualmachines / extensions | Oui | Oui |
> | virtualmachinescalesets | Oui | Oui |

> [!IMPORTANT]
> Consultez [Conseils pour le déplacement de machines virtuelles](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
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
> | operationstatus | Non | Non |
> | pricesheets | Non | Non |
> | products | Non | Non |
> | reservationdetails | Non | Non |
> | reservationrecommendations | Non | Non |
> | reservationsummaries | Non | Non |
> | reservationtransactions | Non | Non |
> | tags | Non | Non |
> | tenants | Non | Non |
> | terms | Non | Non |
> | usagedetails | Non | Non |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Non | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Non | Non |
> | serviceassociationlinks | Non | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Oui | Oui |
> | registries / buildtasks | Oui | Oui |
> | registries / replications | Oui | Oui |
> | registries / taskruns | Oui | Oui |
> | registries / tasks | Oui | Oui |
> | registries / webhooks | Oui | Oui |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Non | Non |
> | managedclusters | Non | Non |
> | openshiftmanagedclusters | Non | Non |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Non | Non |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alertes | Non | Non |
> | budgets | Non | Non |
> | connecteurs | Oui | Oui |
> | dimensions | Non | Non |
> | exports | Non | Non |
> | externalsubscriptions | Non | Non |
> | forecast | Non | Non |
> | query | Non | Non |
> | reportconfigs | Non | Non |
> | reports | Non | Non |
> | showbackrules | Non | Non |
> | views | Non | Non |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Non | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | Non | Non |
> | resourceproviders | Oui | Oui |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Non | Non |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Non | Non |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Non | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Oui | Oui |
> | datacatalogs | Non | Non |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Non | Non |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | packages | Non | Non |
> | plans | Non | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Oui | Oui |
> | factories | Oui | Oui |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Non | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Non | Non |
> | services / projects | Non | Non |
> | slots | Non | Non |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | Non | Non |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Oui | Oui |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Oui | Oui |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Non | Non |
> | servers | Oui | Oui |
> | serversv2 | Oui | Oui |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Oui | Oui |
> | rollouts | Oui | Oui |
> | servicetopologies | Oui | Oui |
> | servicetopologies / services | Oui | Oui |
> | servicetopologies / services / serviceunits | Oui | Oui |
> | steps | Oui | Oui |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Non | Non |
> | elasticpools / iothubtenants | Non | Non |
> | iothubs | Oui | Oui |
> | provisioningservices | Oui | Oui |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Oui | Oui |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controllers | Oui | Oui |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Non | Non |
> | labs | Oui | Non |
> | labs / environments | Oui | Oui |
> | labs / servicerunners | Oui | Oui |
> | labs / virtualmachines | Oui | Non |
> | schedules | Oui | Oui |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Oui | Oui |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domaines | Oui | Oui |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Oui | Oui |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domaines | Oui | Oui |
> | eventSubscriptions | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) |
> | eventsubscriptions | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) | Non (non déplaçable indépendamment, mais déplacé automatiquement avec la ressource abonnée) |
> | extensiontopics | Non | Non |
> | topics | Oui | Oui |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Oui | Oui |
> | espaces de noms | Oui | Oui |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Non | Non |
> | software | Non | Non |
> | softwareupdateprofile | Non | Non |
> | softwareupdates | Non | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Non | Non |
> | sapmonitors | Oui | Oui |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Oui | Oui |

> [!IMPORTANT]
> Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.
>
> Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Oui | Oui |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | Oui | Oui |
> | machines / extensions | Non | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Oui | Oui |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Oui | Oui |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Oui | Oui |
> | activitylogalerts | Non | Non |
> | alertrules | Oui | Oui |
> | automatedexportsettings | Non | Non |
> | autoscalesettings | Oui | Oui |
> | baseline | Non | Non |
> | calculatebaseline | Non | Non |
> | components | Oui | Oui |
> | diagnosticsettings | Non | Non |
> | diagnosticsettingscategories | Non | Non |
> | eventtypes | Non | Non |
> | extendeddiagnosticsettings | Non | Non |
> | logdefinitions | Non | Non |
> | logs | Non | Non |
> | metricalerts | Non | Non |
> | metricbaselines | Non | Non |
> | metricdefinitions | Non | Non |
> | metricnamespaces | Non | Non |
> | Mesures | Non | Non |
> | myworkbooks | Non | Non |
> | scheduledqueryrules | Oui | Oui |
> | Topologie | Non | Non |
> | transactions | Non | Non |
> | vminsightsonboardingstatuses | Non | Non |
> | webtests | Oui | Oui |
> | workbooks | Oui | Oui |
> | workbooktemplates | Oui | Oui |

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Oui | Oui |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Oui | Oui |
> | graph | Oui | Oui |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | vaults | Oui | Oui |

> [!IMPORTANT]
> Les coffres de clés utilisés pour le chiffrement de disques ne peuvent pas être déplacés vers des groupes de ressources situés dans le même abonnement ni d’un abonnement à l’autre.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Non | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Oui | Oui |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Non | Non |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Non | Non |
> | integrationaccounts | Oui | Oui |
> | integrationserviceenvironments | Oui | Non |
> | integrationserviceenvironments / managedapis | Oui | Non |
> | isolatedenvironments | Non | Non |
> | workflows | Oui | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Oui | Oui |
> | webservices | Oui | Non |
> | workspaces | Oui | Oui |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Non | Non |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |
> | accounts / workspaces | Non | Non |
> | accounts / workspaces / projects | Non | Non |
> | teamaccounts | Non | Non |
> | teamaccounts / workspaces | Non | Non |
> | teamaccounts / workspaces / projects | Non | Non |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Non | Non |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Non | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Identités | Non | Non |
> | userassignedidentities | Non | Non |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Non | Non |
> | registrationdefinitions | Non | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Non | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Oui | Oui |
> | mediaservices / liveevents | Oui | Oui |
> | mediaservices / streamingendpoints | Oui | Oui |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Non | Non |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Oui | Oui |
> | migrateprojects | Oui | Oui |
> | projects | Non | Non |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Non | Non |
> | netappaccounts / backuppolicies | Non | Non |
> | netappaccounts / capacitypools | Non | Non |
> | netappaccounts / capacitypools / volumes | Non | Non |
> | netappaccounts / capacitypools / volumes / mounttargets | Non | Non |
> | netappaccounts / capacitypools / volumes / snapshots | Non | Non |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Non | Non |
> | applicationgatewaywebapplicationfirewallpolicies | Non | Non |
> | applicationsecuritygroups | Oui | Oui |
> | azurefirewalls | Oui | Oui |
> | bastionhosts | Non | Non |
> | connections | Oui | Oui |
> | ddoscustompolicies | Oui | Oui |
> | ddosprotectionplans | Non | Non |
> | dnszones | Oui | Oui |
> | expressroutecircuits | Non | Non |
> | expressroutegateways | Non | Non |
> | frontdoors | Non | Non |
> | frontdoorwebapplicationfirewallpolicies | Non | Non |
> | loadbalancers | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
> | localnetworkgateways | Oui | Oui |
> | networkexperimentprofiles | Oui | Oui |
> | networkintentpolicies | Oui | Oui |
> | networkinterfaces | Oui | Oui |
> | networkprofiles | Non | Non |
> | networksecuritygroups | Oui | Oui |
> | networkwatchers | Oui | Oui |
> | networkwatchers / connectionmonitors | Oui | Oui |
> | networkwatchers / flowlogs | Oui | Oui |
> | networkwatchers / lenses | Oui | Oui |
> | networkwatchers / pingmeshes | Oui | Oui |
> | p2svpngateways | Non | Non |
> | privatednszones | Oui | Oui |
> | privatednszones / virtualnetworklinks | Oui | Oui |
> | privateendpointredirectmaps | Non | Non |
> | privateendpoints | Non | Non |
> | privatelinkservices | Non | Non |
> | publicipaddresses | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
> | publicipprefixes | Oui | Oui |
> | routefilters | Non | Non |
> | routetables | Oui | Oui |
> | serviceendpointpolicies | Oui | Oui |
> | trafficmanagerprofiles | Oui | Oui |
> | virtualhubs | Non | Non |
> | virtualnetworkgateways | Oui | Oui |
> | virtualnetworks | Oui | Oui |
> | virtualnetworktaps | Non | Non |
> | virtualrouters | Oui | Oui |
> | virtualwans | Non | Non |
> | vpngateways (Virtual WAN) | Non | Non |
> | vpnserverconfigurations | Non | Non |
> | vpnsites (Virtual WAN) | Non | Non |
> | webapplicationfirewallpolicies | Oui | Oui |

> [!IMPORTANT]
> Consultez l’[Aide pour le déplacement du réseau](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | espaces de noms | Oui | Oui |
> | namespaces / notificationhubs | Oui | Oui |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Oui | Oui |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Non | Non |
> | workspaces | Oui | Oui |

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Non | Non |
> | managementconfigurations | Oui | Oui |
> | solutions | Oui | Oui |
> | views | Oui | Oui |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | peerings | Oui | Oui |
> | peeringservices | Non | Non |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | Non | Non |
> | policystates | Non | Non |
> | policytrackedresources | Non | Non |
> | remediations | Non | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dashboards | Oui | Oui |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Non | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Oui | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Oui | Oui |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Non | Non |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Non | Non |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Non | Non |
> | replicationeligibilityresults | Non | Non |
> | vaults | Oui | Oui |

> [!IMPORTANT]
> Voir [Conseils sur le déplacement de Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | espaces de noms | Oui | Oui |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | queries | Oui | Oui |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Non | Non |
> | childavailabilitystatuses | Non | Non |
> | childresources | Non | Non |
> | événements | Non | Non |
> | Notifications | Non | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Non | Non |
> | liens | Non | Non |
> | tags | Non | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Oui | Non |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobcollections | Oui | Oui |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Oui | Oui |

> [!IMPORTANT]
> Vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche dans des régions différentes. Déplacez-les plutôt dans des opérations distinctes.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Non | Non |
> | advancedthreatprotectionsettings | Non | Non |
> | assessmentmetadata | Non | Non |
> | assessments | Non | Non |
> | automations | Oui | Oui |
> | complianceresults | Non | Non |
> | compliances | Non | Non |
> | datacollectionagents | Non | Non |
> | datacollectionresults | Non | Non |
> | devicesecuritygroups | Non | Non |
> | informationprotectionpolicies | Non | Non |
> | iotsecuritysolutions | Oui | Oui |
> | servervulnerabilityassessments | Non | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | Non | Non |
> | alertrules | Non | Non |
> | alertruletemplates | Non | Non |
> | bookmarks | Non | Non |
> | cas | Non | Non |
> | dataconnectors | Non | Non |
> | entities | Non | Non |
> | entityqueries | Non | Non |
> | officeconsents | Non | Non |
> | paramètres | Non | Non |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Non | Non |
> | nœuds | Non | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | espaces de noms | Oui | Oui |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Non | Non |
> | clusters | Oui | Oui |
> | clusters / applications | Non | Non |
> | containergroups | Non | Non |
> | containergroupsets | Non | Non |
> | edgeclusters | Non | Non |
> | networks | Non | Non |
> | secretstores | Non | Non |
> | volumes | Non | Non |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applications | Oui | Oui |
> | gateways | Oui | Oui |
> | networks | Oui | Oui |
> | secrets | Oui | Oui |
> | volumes | Oui | Oui |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Non | Non |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Oui | Oui |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Non | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Non | Non |
> | applications | Non | Non |
> | jitrequests | Non | Non |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Non | Non |
> | managedinstances | Non | Non |
> | managedinstances / databases | Non | Non |
> | servers | Oui | Oui |
> | servers / databases | Oui | Oui |
> | servers / elasticpools | Oui | Oui |
> | virtualclusters | Oui | Oui |

> [!IMPORTANT]
> La base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées. Ce comportement s’applique aux bases de données Azure SQL Database et Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Oui | Oui |
> | sqlvirtualmachines | Oui | Oui |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Non | Non |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Oui | Oui |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Oui | Oui |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Non | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Non | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Non | Non |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Oui | Oui |

> [!IMPORTANT]
> Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Non | Non |
> | environments / eventsources | Non | Non |
> | instances | Non | Non |
> | instances / environments | Non | Non |
> | instances / environments / eventsources | Non | Non |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | createsubscription | Non | Non |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | createsupportticket | Non | Non |
> | supporttickets | Non | Non |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Non | Non |
> | les ressources | Non | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Oui | Oui |
> | environments / eventsources | Oui | Oui |
> | environments / referencedatasets | Oui | Oui |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Oui | Oui |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Non | Non |
> | dedicatedcloudservices | Non | Non |
> | virtualmachines | Non | Non |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Oui | Oui |
> | plans | Oui | Oui |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificates | Non | Oui |
> | connectiongateways | Oui | Oui |
> | connections | Oui | Oui |
> | customapis | Oui | Oui |
> | hostingenvironments | Non | Non |
> | serverfarms | Oui | Oui |
> | sites | Oui | Oui |
> | sites / premieraddons | Oui | Oui |
> | sites / slots | Oui | Oui |
> | staticsites | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Non | Non |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Non | Non |
> | monitorinstances | Non | Non |
> | monitors | Non | Non |
> | notificationsettings | Non | Non |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](move-resource-group-and-subscription.md).

Pour obtenir les mêmes données qu'un fichier de valeurs séparées par des virgules, téléchargez [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

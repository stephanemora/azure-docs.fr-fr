---
title: Prise en charge de l’opération de déplacement par type de ressource Azure
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226817"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources
Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Il fournit également des informations sur les conditions spéciales à prendre en compte lors du déplacement d’une ressource.

Accédez à un espace de noms du fournisseur de ressources :
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
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
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
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
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Non | Non |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tenants | Non | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | OUI | OUI |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| service | OUI | OUI |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | OUI | OUI |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Non | Non |
| appidentities | Non | Non |
| gateways | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Non | Non |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | OUI | OUI |
| automationaccounts/configurations | OUI | OUI |
| automationaccounts/runbooks | OUI | OUI |

> [!IMPORTANT]
> Les runbooks doivent exister dans le même groupe de ressources que le compte Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | OUI | OUI |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registrations | OUI | OUI |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Non | Non |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | OUI | OUI |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Non | Non |
| fileservers | Non | Non |
| jobs | Non | Non |
| workspaces | Non | Non |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Non | Non |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | OUI | OUI |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | OUI | OUI |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Non | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | OUI | OUI |

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| redis | OUI | OUI |

> [!IMPORTANT]
> Si l’instance du Cache Azure pour Redis est configurée avec un réseau virtuel, l’instance ne peut pas être déplacée vers un autre abonnement. Consultez [Limitations de déplacement des réseaux virtuels](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | OUI | OUI |
| profiles/endpoints | OUI | OUI |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | OUI | OUI |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | OUI | Non |
| virtualmachines | OUI | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Non | Non |
| reservedips | Non | Non |
| virtualnetworks | Non | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | OUI | Non |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement de déploiement classique](./move-limitations/classic-model-move-limitations.md). Les ressources de déploiement classiques peuvent être déplacées entre abonnements avec une opération spécifique à ce scénario.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | OUI | OUI |
| disks | OUI | OUI |
| galleries | Non | Non |
| galleries/images | Non | Non |
| galleries/images/versions | Non | Non |
| hostgroups | Non | Non |
| hostgroups/hosts | Non | Non |
| images | OUI | OUI |
| proximityplacementgroups | Non | Non |
| restorepointcollections | Non | Non |
| sharedvmimages | Non | Non |
| sharedvmimages/versions | Non | Non |
| snapshots | OUI | OUI |
| virtualmachines | OUI | OUI |
| virtualmachines/extensions | OUI | OUI |
| virtualmachinescalesets | OUI | OUI |

> [!IMPORTANT]
> Consultez [Conseils pour le déplacement de machines virtuelles](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Non | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Non | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registries | OUI | OUI |
| registries/buildtasks | OUI | OUI |
| registries/replications | OUI | OUI |
| registries/tasks | OUI | OUI |
| registries/webhooks | OUI | OUI |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Non | Non |
| managedclusters | Non | Non |
| openshiftmanagedclusters | Non | Non |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | OUI | OUI |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connecteurs | OUI | OUI |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | OUI | OUI |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Non | Non |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Non | Non |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Non | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catalogs | OUI | OUI |
| datacatalogs | Non | Non |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Non | Non |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| packages | Non | Non |
| plans | Non | Non |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | OUI | OUI |
| factories | OUI | OUI |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Non | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Non | Non |
| services/projects | Non | Non |
| slots | Non | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Non | Non |
| servers | OUI | OUI |
| serversv2 | OUI | OUI |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | OUI | OUI |
| rollouts | OUI | OUI |
| servicetopologies | OUI | OUI |
| servicetopologies/services | OUI | OUI |
| servicetopologies/services/serviceunits | OUI | OUI |
| steps | OUI | OUI |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Non | Non |
| elasticpools/iothubtenants | Non | Non |
| iothubs | OUI | OUI |
| provisioningservices | OUI | OUI |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| controllers | Non | Non |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Non | Non |
| labs | OUI | Non |
| labs/environments | OUI | OUI |
| labs/servicerunners | OUI | OUI |
| labs/virtualmachines | OUI | Non |
| schedules | OUI | OUI |

## <a name="microsoftdns"></a>microsoft.dns
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Non | Non |
| dnszones/a | Non | Non |
| dnszones/aaaa | Non | Non |
| dnszones/cname | Non | Non |
| dnszones/mx | Non | Non |
| dnszones/ptr | Non | Non |
| dnszones/srv | Non | Non |
| dnszones/txt | Non | Non |
| trafficmanagerprofiles | Non | Non |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | OUI | OUI |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domaines | OUI | OUI |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | OUI | OUI |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domaines | OUI | OUI |
| topics | OUI | OUI |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |
| namespaces | OUI | OUI |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | OUI | OUI |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |

> [!IMPORTANT]
> Vous pouvez déplacer des clusters HDInsight vers un nouvel abonnement ou groupe de ressources. Toutefois, vous ne pouvez pas déplacer sur différents abonnements les ressources réseau liées au cluster HDInsight (par exemple le réseau virtuel, une carte réseau ou un équilibrage de charge). En outre, vous ne pouvez pas déplacer vers un nouveau groupe de ressources une carte réseau connectée à une machine virtuelle pour le cluster.
>
> Lorsque vous déplacez un cluster HDInsight vers un nouvel abonnement, déplacez tout d’abord les autres ressources (le compte de stockage, par exemple). Puis, déplacez le cluster HDInsight par lui-même.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | OUI | OUI |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| machines | Non | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | OUI | OUI |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | OUI | OUI |

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |
| actiongroups | OUI | OUI |
| activitylogalerts | Non | Non |
| alertrules | OUI | OUI |
| autoscalesettings | OUI | OUI |
| components | OUI | OUI |
| guestdiagnosticsettings | Non | Non |
| metricalerts | Non | Non |
| notificationgroups | Non | Non |
| notificationrules | Non | Non |
| scheduledqueryrules | OUI | OUI |
| webtests | OUI | OUI |
| workbooks | OUI | OUI |

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | OUI | OUI |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | OUI | OUI |
| graph | OUI | OUI |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Non | Non |
| vaults | OUI | OUI |

> [!IMPORTANT]
> Les coffres de clés utilisés pour le chiffrement de disques ne peuvent pas être déplacés vers des groupes de ressources situés dans le même abonnement ni d’un abonnement à l’autre.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Non | Non |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Non | Non |
| integrationaccounts | OUI | OUI |
| integrationserviceenvironments | Non | Non |
| isolatedenvironments | Non | Non |
| workflows | OUI | OUI |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | OUI | OUI |
| webservices | OUI | Non |
| workspaces | OUI | OUI |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | OUI | OUI |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |
| accounts/workspaces | Non | Non |
| accounts/workspaces/projects | Non | Non |
| teamaccounts | Non | Non |
| teamaccounts/workspaces | Non | Non |
| teamaccounts/workspaces/projects | Non | Non |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Non | Non |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Non | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Non | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Non | Non |

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mediaservices | OUI | OUI |
| mediaservices/liveevents | OUI | OUI |
| mediaservices/streamingendpoints | OUI | OUI |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Non | Non |
| migrateprojects | Non | Non |
| projects | Non | Non |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Non | Non |
| netappaccounts/capacitypools | Non | Non |
| netappaccounts/capacitypools/volumes | Non | Non |
| netappaccounts/capacitypools/volumes/mounttargets | Non | Non |
| netappaccounts/capacitypools/volumes/snapshots | Non | Non |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Non | Non |
| applicationgatewaywebapplicationfirewallpolicies | Non | Non |
| applicationsecuritygroups | OUI | OUI |
| azurefirewalls | OUI | OUI |
| bastionhosts | Non | Non |
| connections | OUI | OUI |
| ddoscustompolicies | OUI | OUI |
| ddosprotectionplans | Non | Non |
| dnszones | OUI | OUI |
| expressroutecircuits | Non | Non |
| expressroutecrossconnections | Non | Non |
| expressroutegateways | Non | Non |
| expressrouteports | Non | Non |
| frontdoors | Non | Non |
| frontdoorwebapplicationfirewallpolicies | Non | Non |
| loadbalancers | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
| localnetworkgateways | OUI | OUI |
| natgateways | OUI | OUI |
| networkintentpolicies | OUI | OUI |
| networkinterfaces | OUI | OUI |
| networkprofiles | Non | Non |
| networksecuritygroups | OUI | OUI |
| networkwatchers | OUI | OUI |
| networkwatchers/connectionmonitors | OUI | OUI |
| networkwatchers/lenses | OUI | OUI |
| networkwatchers/pingmeshes | OUI | OUI |
| p2svpngateways | Non | Non |
| privatednszones | OUI | OUI |
| privatednszones/virtualnetworklinks | OUI | OUI |
| privateendpoints | Non | Non |
| privatelinkservices | Non | Non |
| publicipaddresses | Oui - Référence SKU de base<br>Non - Référence SKU Standard | Oui - Référence SKU de base<br>Non - Référence SKU Standard |
| publicipprefixes | OUI | OUI |
| routefilters | Non | Non |
| routetables | OUI | OUI |
| securegateways | OUI | OUI |
| serviceendpointpolicies | OUI | OUI |
| trafficmanagerprofiles | OUI | OUI |
| virtualhubs | Non | Non |
| virtualnetworkgateways | OUI | OUI |
| virtualnetworks | OUI | OUI |
| virtualnetworktaps | Non | Non |
| virtualwans | Non | Non |
| vpngateways | Non | Non |
| vpnsites | Non | Non |
| webapplicationfirewallpolicies | OUI | OUI |

> [!IMPORTANT]
> Consultez [Conseils sur le déplacement de réseaux virtuels](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |
| namespaces/notificationhubs | OUI | OUI |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | OUI | OUI |

> [!IMPORTANT]
> Vérifiez que le déplacement vers le nouvel abonnement ne dépasse pas les [quotas d’abonnement](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | OUI | OUI |
| solutions | OUI | OUI |
| views | OUI | OUI |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| peerings | Non | Non |

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | OUI | OUI |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Non | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | OUI | OUI |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| capacities | OUI | OUI |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Non | Non |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| vaults | OUI | OUI |

> [!IMPORTANT]
> Voir [Conseils sur le déplacement de Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | OUI | Non |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| flows | OUI | OUI |
| jobcollections | OUI | OUI |

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | OUI | OUI |

> [!IMPORTANT]
> Vous ne pouvez pas déplacer simultanément plusieurs ressources de recherche dans des régions différentes. Déplacez-les plutôt dans des opérations distinctes.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | OUI | OUI |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gateways | Non | Non |
| nœuds | Non | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Non | Non |
| clusters | OUI | OUI |
| containergroups | Non | Non |
| containergroupsets | Non | Non |
| edgeclusters | Non | Non |
| networks | Non | Non |
| secretstores | Non | Non |
| volumes | Non | Non |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | OUI | OUI |
| containergroups | Non | Non |
| gateways | OUI | OUI |
| networks | OUI | OUI |
| secrets | OUI | OUI |
| volumes | OUI | OUI |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | OUI | OUI |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Non | Non |

> [!IMPORTANT]
> Voir [Conseils sur le déplacement de Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Non | Non |
| appliances | Non | Non |
| applicationdefinitions | Non | Non |
| applications | Non | Non |
| jitrequests | Non | Non |

## <a name="microsoftsql"></a>Microsoft.Sql
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Non | Non |
| managedinstances | Non | Non |
| managedinstances/databases | Non | Non |
| servers | OUI | OUI |
| servers/databases | OUI | OUI |
| servers/elasticpools | OUI | OUI |
| virtualclusters | OUI | OUI |

> [!IMPORTANT]
> La base de données et le serveur doivent résider dans le même groupe de ressources. Lorsque vous déplacez un serveur SQL, toutes ses bases de données sont également déplacées. Ce comportement s’applique aux bases de données Azure SQL Database et Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | OUI | OUI |
| sqlvirtualmachines | OUI | OUI |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Non | Non |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | OUI | OUI |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| caches | Non | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | OUI | OUI |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Non | Non |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Non | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managers | Non | Non |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | OUI | OUI |

> [!IMPORTANT]
> Les tâches Stream Analytics ne peuvent pas être déplacées lorsqu’elles sont en cours d’exécution.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Non | Non |
| environments/eventsources | Non | Non |
| instances | Non | Non |
| instances/environments | Non | Non |
| instances/environments/eventsources | Non | Non |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Non | Non |
| les ressources | Non | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | OUI | OUI |
| environments/eventsources | OUI | OUI |
| environments/referencedatasets | OUI | OUI |

## <a name="microsofttoken"></a>Microsoft.Token
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| stores | Non | Non |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Non | Non |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | OUI | OUI |
| account/extension | OUI | OUI |
| account/project | OUI | OUI |

> [!IMPORTANT]
> Pour modifier l’abonnement pour Azure DevOps, consultez [Modifier l’abonnement Azure utilisé pour la facturation](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | OUI | OUI |
| dedicatedcloudservices | OUI | OUI |
| virtualmachines | OUI | OUI |

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificates | Non | OUI |
| connectiongateways | OUI | OUI |
| connections | OUI | OUI |
| customapis | OUI | OUI |
| hostingenvironments | Non | Non |
| serverfarms | OUI | OUI |
| sites | OUI | OUI |
| sites/premieraddons | OUI | OUI |
| sites/slots | OUI | OUI |

> [!IMPORTANT]
> Consultez les [Conseils de déplacement d’App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Non | Non |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Type de ressource | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Non | Non |
| hostpools | Non | Non |
| workspaces | Non | Non |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

Pour obtenir les mêmes données qu'un fichier de valeurs séparées par des virgules, téléchargez [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

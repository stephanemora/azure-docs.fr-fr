---
title: Prise en charge de l’opération de déplacement par type de ressource Azure
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 2/13/2019
ms.author: tomfitz
ms.openlocfilehash: f82c3adc0049c7386206fb4be10d3e634627a25a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236887"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources
Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Même si un type de ressource prend en charge l’opération de déplacement, il peut exister des conditions qui empêchent la ressource d’être déplacée. Pour plus d’informations sur les conditions qui affectent les opérations de déplacement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

Pour obtenir les mêmes données qu'un fichier de valeurs séparées par des virgules, téléchargez [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domainservices | Non  | Non  |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| tenants | Non  | Non  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| service | OUI | OUI |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| apiapps | Non  | Non  |
| appidentities | Non  | Non  |
| gateways | Non  | Non  |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| policyassignments | Non  | Non  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| automationaccounts | OUI | OUI |
| automationaccounts/configurations | OUI | OUI |
| automationaccounts/runbooks | OUI | OUI |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| b2cdirectories | OUI | OUI |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| registrations | OUI | OUI |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| backupvault | Non  | Non  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| batchaccounts | OUI | OUI |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Non  | Non  |
| fileservers | Non  | Non  |
| jobs | Non  | Non  |
| workspaces | Non  | Non  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| mapapis | Non  | Non  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| biztalk | OUI | OUI |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| blockchainmembers | Non  | Non  |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| blueprintassignments | Non  | Non  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| botservices | OUI | OUI |

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| redis | OUI | OUI |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| profiles | OUI | OUI |
| profiles/endpoints | OUI | OUI |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| certificateorders | OUI | OUI |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domainnames | OUI | Non  |
| virtualmachines | OUI | Non  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Non  | Non  |
| reservedips | Non  | Non  |
| virtualnetworks | Non  | Non  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | OUI | Non  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| availabilitysets | OUI | OUI |
| disks | OUI | OUI |
| galleries | Non  | Non  |
| galleries/images | Non  | Non  |
| galleries/images/versions | Non  | Non  |
| images | OUI | OUI |
| proximityplacementgroups | Non  | Non  |
| restorepointcollections | Non  | Non  |
| sharedvmimages | Non  | Non  |
| sharedvmimages/versions | Non  | Non  |
| snapshots | OUI | OUI |
| virtualmachines | OUI | OUI |
| virtualmachines/extensions | OUI | OUI |
| virtualmachinescalesets | OUI | OUI |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| containergroups | Non  | Non  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| containergroups | Non  | Non  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| registries | OUI | OUI |
| registries/buildtasks | OUI | OUI |
| registries/replications | Non  | Non  |
| registries/tasks | OUI | OUI |
| registries/webhooks | OUI | OUI |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| containerservices | Non  | Non  |
| managedclusters | Non  | Non  |
| openshiftmanagedclusters | Non  | Non  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | OUI | OUI |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| connecteurs | OUI | OUI |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hubs | OUI | OUI |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| jobs | Non  | Non  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Non  | Non  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Non  | Non  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| catalogs | OUI | OUI |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| connectionmanagers | Non  | Non  |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| packages | Non  | Non  |
| plans | Non  | Non  |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| datafactories | OUI | OUI |
| factories | OUI | OUI |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Non  | Non  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| services | Non  | Non  |
| services/projects | Non  | Non  |
| slots | Non  | Non  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servers | OUI | OUI |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servergroups | Non  | Non  |
| servers | OUI | OUI |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| artifactsources | Non  | Non  |
| rollouts | Non  | Non  |
| servicetopologies | Non  | Non  |
| servicetopologies/services | Non  | Non  |
| servicetopologies/services/serviceunits | Non  | Non  |
| steps | Non  | Non  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| elasticpools | Non  | Non  |
| elasticpools/iothubtenants | Non  | Non  |
| iothubs | OUI | OUI |
| provisioningservices | OUI | OUI |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| controllers | Non  | Non  |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| labcenters | Non  | Non  |
| labs | OUI | Non  |
| labs/servicerunners | OUI | OUI |
| labs/virtualmachines | OUI | Non  |
| schedules | Non  | Non  |

## <a name="microsoftdns"></a>microsoft.dns
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| dnszones | Non  | Non  |
| dnszones/a | Non  | Non  |
| dnszones/aaaa | Non  | Non  |
| dnszones/cname | Non  | Non  |
| dnszones/mx | Non  | Non  |
| dnszones/ptr | Non  | Non  |
| dnszones/srv | Non  | Non  |
| dnszones/txt | Non  | Non  |
| trafficmanagerprofiles | Non  | Non  |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| databaseaccounts | OUI | OUI |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domaines | OUI | OUI |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domaines | OUI | OUI |
| topics | OUI | OUI |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |
| namespaces | OUI | OUI |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hanainstances | OUI | OUI |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| datamanagers | OUI | OUI |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| jobs | OUI | OUI |

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |
| actiongroups | OUI | OUI |
| activitylogalerts | Non  | Non  |
| alertrules | OUI | OUI |
| autoscalesettings | OUI | OUI |
| components | OUI | OUI |
| guestdiagnosticsettings | Non  | Non  |
| metricalerts | Non  | Non  |
| notificationgroups | Non  | Non  |
| notificationrules | Non  | Non  |
| scheduledqueryrules | Non  | Non  |
| webtests | OUI | OUI |
| workbooks | OUI | OUI |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| iotapps | OUI | OUI |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| checknameavailability | OUI | OUI |
| graph | OUI | OUI |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hsmpools | Non  | Non  |
| vaults | OUI | OUI |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | OUI | OUI |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| labaccounts | OUI | OUI |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hostingenvironments | Non  | Non  |
| integrationaccounts | OUI | OUI |
| integrationserviceenvironments | Non  | Non  |
| isolatedenvironments | Non  | Non  |
| workflows | OUI | OUI |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| commitmentplans | OUI | OUI |
| webservices | OUI | Non  |
| workspaces | OUI | OUI |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| operationalizationclusters | OUI | OUI |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |
| accounts/workspaces | OUI | OUI |
| accounts/workspaces/projects | OUI | OUI |
| teamaccounts | OUI | OUI |
| teamaccounts/workspaces | OUI | OUI |
| teamaccounts/workspaces/projects | OUI | OUI |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hostingaccounts | Non  | Non  |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Non  | Non  |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| userassignedidentities | OUI | OUI |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | OUI | OUI |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| classicdevservices | Non  | Non  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| mediaservices | OUI | OUI |
| mediaservices/liveevents | OUI | OUI |
| mediaservices/streamingendpoints | OUI | OUI |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| assessmentprojects | Non  | Non  |
| migrateprojects | Non  | Non  |
| projects | Non  | Non  |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| netappaccounts | Non  | Non  |
| netappaccounts/capacitypools | Non  | Non  |
| netappaccounts/capacitypools/volumes | Non  | Non  |
| netappaccounts/capacitypools/volumes/mounttargets | Non  | Non  |
| netappaccounts/capacitypools/volumes/snapshots | Non  | Non  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applicationgateways | Non  | Non  |
| applicationsecuritygroups | OUI | OUI |
| azurefirewalls | OUI | OUI |
| bastionhosts | Non  | Non  |
| connections | OUI | OUI |
| ddoscustompolicies | OUI | OUI |
| ddosprotectionplans | Non  | Non  |
| dnszones | OUI | OUI |
| expressroutecircuits | Non  | Non  |
| expressroutecrossconnections | Non  | Non  |
| expressroutegateways | Non  | Non  |
| expressrouteports | Non  | Non  |
| frontdoors | OUI | OUI |
| frontdoorwebapplicationfirewallpolicies | OUI | OUI |
| interfaceendpoints | Non  | Non  |
| loadbalancers | OUI | OUI |
| localnetworkgateways | OUI | OUI |
| natgateways | OUI | OUI |
| networkintentpolicies | OUI | OUI |
| networkinterfaces | OUI | OUI |
| networkprofiles | Non  | Non  |
| networksecuritygroups | OUI | OUI |
| networkwatchers | OUI | OUI |
| networkwatchers/connectionmonitors | OUI | OUI |
| networkwatchers/lenses | OUI | OUI |
| networkwatchers/pingmeshes | OUI | OUI |
| p2svpngateways | Non  | Non  |
| privatelinkservices | Non  | Non  |
| publicipaddresses | OUI | OUI |
| publicipprefixes | OUI | OUI |
| routefilters | Non  | Non  |
| routetables | OUI | OUI |
| securegateways | Non  | Non  |
| serviceendpointpolicies | OUI | OUI |
| trafficmanagerprofiles | OUI | OUI |
| virtualhubs | Non  | Non  |
| virtualnetworkgateways | OUI | OUI |
| virtualnetworks | OUI | OUI |
| virtualnetworktaps | Non  | Non  |
| virtualwans | Non  | Non  |
| vpngateways | Non  | Non  |
| vpnsites | OUI | OUI |
| webapplicationfirewallpolicies | OUI | OUI |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |
| namespaces/notificationhubs | OUI | OUI |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | OUI | OUI |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| managementconfigurations | OUI | OUI |
| solutions | OUI | OUI |
| views | OUI | OUI |

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| dashboards | OUI | OUI |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| rootresources | Non  | Non  |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspacecollections | OUI | OUI |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| capacities | OUI | OUI |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| vaults | OUI | OUI |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | OUI | Non  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| flows | OUI | OUI |
| jobcollections | OUI | OUI |

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| searchservices | OUI | OUI |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| gateways | Non  | Non  |
| nœuds | Non  | Non  |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | OUI | OUI |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Non  | Non  |
| clusters | OUI | OUI |
| containergroups | Non  | Non  |
| containergroupsets | Non  | Non  |
| edgeclusters | Non  | Non  |
| networks | Non  | Non  |
| secretstores | Non  | Non  |
| volumes | Non  | Non  |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | OUI | OUI |
| containergroups | Non  | Non  |
| gateways | OUI | OUI |
| networks | OUI | OUI |
| secrets | OUI | OUI |
| volumes | OUI | OUI |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| signalr | OUI | OUI |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Non  | Non  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Non  | Non  |
| appliances | Non  | Non  |
| applicationdefinitions | Non  | Non  |
| applications | Non  | Non  |
| jitrequests | Non  | Non  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| managedinstances | OUI | OUI |
| managedinstances/databases | OUI | OUI |
| servers | OUI | OUI |
| servers/databases | OUI | OUI |
| servers/elasticpools | OUI | OUI |
| servers/jobaccounts | Non  | Non  |
| servers/jobagents | Non  | Non  |
| virtualclusters | OUI | OUI |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | OUI | OUI |
| sqlvirtualmachines | OUI | OUI |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| dwvm | Non  | Non  |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | OUI | OUI |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | OUI | OUI |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Non  | Non  |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Non  | Non  |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| managers | Non  | Non  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| streamingjobs | OUI | OUI |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| environments | Non  | Non  |
| environments/eventsources | Non  | Non  |
| instances | Non  | Non  |
| instances/environments | Non  | Non  |
| instances/environments/eventsources | Non  | Non  |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| providerregistrations | Non  | Non  |
| les ressources | Non  | Non  |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| environments | OUI | OUI |
| environments/eventsources | OUI | OUI |
| environments/referencedatasets | OUI | OUI |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| imagetemplates | Non  | Non  |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| account | OUI | OUI |
| account/extension | OUI | OUI |
| account/project | OUI | OUI |

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| certificates | Non  | OUI |
| connectiongateways | OUI | OUI |
| connections | OUI | OUI |
| customapis | OUI | OUI |
| hostingenvironments | Non  | Non  |
| serverfarms | OUI | OUI |
| sites | OUI | OUI |
| sites/premieraddons | OUI | OUI |
| sites/slots | OUI | OUI |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| deviceservices | OUI | OUI |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

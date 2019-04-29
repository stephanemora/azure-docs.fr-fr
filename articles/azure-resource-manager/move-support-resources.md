---
title: Prise en charge de l’opération de déplacement par type de ressource Azure
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: d44b1bf778c7ec9551e2fd30f67083f8dded22d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729288"
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
| servers | Oui | Oui |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| service | Oui | Oui |

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
| automationaccounts | Oui | Oui |
| automationaccounts/configurations | Oui | Oui |
| automationaccounts/runbooks | Oui | Oui |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| b2cdirectories | Oui | Oui |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| registrations | Oui | Oui |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| backupvault | Non  | Non  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| batchaccounts | Oui | Oui |

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
| biztalk | Oui | Oui |

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
| botservices | Oui | Oui |

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| redis | Oui | Oui |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| profiles | Oui | Oui |
| profiles/endpoints | Oui | Oui |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| certificateorders | Oui | Oui |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domainnames | Oui | Non  |
| virtualmachines | Oui | Non  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Non  | Non  |
| reservedips | Non  | Non  |
| virtualnetworks | Non  | Non  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | Oui | Non  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| availabilitysets | Oui | Oui |
| disks | Oui | Oui |
| galleries | Non  | Non  |
| galleries/images | Non  | Non  |
| galleries/images/versions | Non  | Non  |
| images | Oui | Oui |
| proximityplacementgroups | Non  | Non  |
| restorepointcollections | Non  | Non  |
| sharedvmimages | Non  | Non  |
| sharedvmimages/versions | Non  | Non  |
| snapshots | Oui | Oui |
| virtualmachines | Oui | Oui |
| virtualmachines/extensions | Oui | Oui |
| virtualmachinescalesets | Oui | Oui |

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
| registries | Oui | Oui |
| registries/buildtasks | Oui | Oui |
| registries/replications | Non  | Non  |
| registries/tasks | Oui | Oui |
| registries/webhooks | Oui | Oui |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| containerservices | Non  | Non  |
| managedclusters | Non  | Non  |
| openshiftmanagedclusters | Non  | Non  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Oui | Oui |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| connecteurs | Oui | Oui |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hubs | Oui | Oui |

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
| catalogs | Oui | Oui |

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
| datafactories | Oui | Oui |
| factories | Oui | Oui |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Non  | Non  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| services | Non  | Non  |
| services/projects | Non  | Non  |
| slots | Non  | Non  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servers | Oui | Oui |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servers | Oui | Oui |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| servergroups | Non  | Non  |
| servers | Oui | Oui |

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
| iothubs | Oui | Oui |
| provisioningservices | Oui | Oui |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| controllers | Non  | Non  |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| labcenters | Non  | Non  |
| labs | Oui | Non  |
| labs/servicerunners | Oui | Oui |
| labs/virtualmachines | Oui | Non  |
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
| databaseaccounts | Oui | Oui |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domaines | Oui | Oui |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| domaines | Oui | Oui |
| topics | Oui | Oui |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Oui | Oui |
| namespaces | Oui | Oui |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hanainstances | Oui | Oui |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Oui | Oui |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| datamanagers | Oui | Oui |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| jobs | Oui | Oui |

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |
| actiongroups | Oui | Oui |
| activitylogalerts | Non  | Non  |
| alertrules | Oui | Oui |
| autoscalesettings | Oui | Oui |
| components | Oui | Oui |
| guestdiagnosticsettings | Non  | Non  |
| metricalerts | Non  | Non  |
| notificationgroups | Non  | Non  |
| notificationrules | Non  | Non  |
| scheduledqueryrules | Non  | Non  |
| webtests | Oui | Oui |
| workbooks | Oui | Oui |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| iotapps | Oui | Oui |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| checknameavailability | Oui | Oui |
| graph | Oui | Oui |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hsmpools | Non  | Non  |
| vaults | Oui | Oui |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| clusters | Oui | Oui |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| labaccounts | Non  | Non  |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| hostingenvironments | Non  | Non  |
| integrationaccounts | Oui | Oui |
| integrationserviceenvironments | Non  | Non  |
| isolatedenvironments | Non  | Non  |
| workflows | Oui | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| commitmentplans | Oui | Oui |
| webservices | Oui | Non  |
| workspaces | Oui | Oui |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Oui | Oui |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |
| accounts/workspaces | Oui | Oui |
| accounts/workspaces/projects | Oui | Oui |
| teamaccounts | Oui | Oui |
| teamaccounts/workspaces | Oui | Oui |
| teamaccounts/workspaces/projects | Oui | Oui |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

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
| userassignedidentities | Oui | Oui |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Oui | Oui |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| classicdevservices | Non  | Non  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| mediaservices | Oui | Oui |
| mediaservices/liveevents | Oui | Oui |
| mediaservices/streamingendpoints | Oui | Oui |

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
| applicationsecuritygroups | Oui | Oui |
| azurefirewalls | Oui | Oui |
| bastionhosts | Non  | Non  |
| connections | Oui | Oui |
| ddoscustompolicies | Oui | Oui |
| ddosprotectionplans | Non  | Non  |
| dnszones | Oui | Oui |
| expressroutecircuits | Non  | Non  |
| expressroutecrossconnections | Non  | Non  |
| expressroutegateways | Non  | Non  |
| expressrouteports | Non  | Non  |
| frontdoors | Oui | Oui |
| frontdoorwebapplicationfirewallpolicies | Oui | Oui |
| interfaceendpoints | Non  | Non  |
| loadbalancers | Oui | Oui |
| localnetworkgateways | Oui | Oui |
| natgateways | Oui | Oui |
| networkintentpolicies | Oui | Oui |
| networkinterfaces | Oui | Oui |
| networkprofiles | Non  | Non  |
| networksecuritygroups | Oui | Oui |
| networkwatchers | Oui | Oui |
| networkwatchers/connectionmonitors | Oui | Oui |
| networkwatchers/lenses | Oui | Oui |
| networkwatchers/pingmeshes | Oui | Oui |
| p2svpngateways | Non  | Non  |
| privatelinkservices | Non  | Non  |
| publicipaddresses | Oui | Oui |
| publicipprefixes | Oui | Oui |
| routefilters | Non  | Non  |
| routetables | Oui | Oui |
| securegateways | Non  | Non  |
| serviceendpointpolicies | Oui | Oui |
| trafficmanagerprofiles | Oui | Oui |
| virtualhubs | Non  | Non  |
| virtualnetworkgateways | Oui | Oui |
| virtualnetworks | Oui | Oui |
| virtualnetworktaps | Non  | Non  |
| virtualwans | Non  | Non  |
| vpngateways | Non  | Non  |
| vpnsites | Oui | Oui |
| webapplicationfirewallpolicies | Oui | Oui |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Oui | Oui |
| namespaces/notificationhubs | Oui | Oui |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspaces | Oui | Oui |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| managementconfigurations | Oui | Oui |
| solutions | Oui | Oui |
| views | Oui | Oui |

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| dashboards | Oui | Oui |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| rootresources | Non  | Non  |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| workspacecollections | Oui | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| capacities | Oui | Oui |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| accounts | Non  | Non  |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| vaults | Oui | Oui |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Oui | Oui |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Oui | Non  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| flows | Oui | Oui |
| jobcollections | Oui | Oui |

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| searchservices | Oui | Oui |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| gateways | Non  | Non  |
| nœuds | Non  | Non  |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| namespaces | Oui | Oui |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Non  | Non  |
| clusters | Oui | Oui |
| containergroups | Non  | Non  |
| containergroupsets | Non  | Non  |
| edgeclusters | Non  | Non  |
| networks | Non  | Non  |
| secretstores | Non  | Non  |
| volumes | Non  | Non  |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| applications | Oui | Oui |
| containergroups | Non  | Non  |
| gateways | Oui | Oui |
| networks | Oui | Oui |
| secrets | Oui | Oui |
| volumes | Oui | Oui |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| signalr | Oui | Oui |

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
| managedinstances | Oui | Oui |
| managedinstances/databases | Oui | Oui |
| servers | Oui | Oui |
| servers/databases | Oui | Oui |
| servers/elasticpools | Oui | Oui |
| servers/jobaccounts | Non  | Non  |
| servers/jobagents | Non  | Non  |
| virtualclusters | Oui | Oui |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Oui | Oui |
| sqlvirtualmachines | Oui | Oui |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| dwvm | Non  | Non  |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storageaccounts | Oui | Oui |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| storagesyncservices | Oui | Oui |

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
| streamingjobs | Oui | Oui |

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
| environments | Oui | Oui |
| environments/eventsources | Oui | Oui |
| environments/referencedatasets | Oui | Oui |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| imagetemplates | Non  | Non  |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| account | Oui | Oui |
| account/extension | Oui | Oui |
| account/project | Oui | Oui |

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| certificates | Non  | Oui |
| connectiongateways | Oui | Oui |
| connections | Oui | Oui |
| customapis | Oui | Oui |
| hostingenvironments | Non  | Non  |
| serverfarms | Oui | Oui |
| sites | Oui | Oui |
| sites/premieraddons | Oui | Oui |
| sites/slots | Oui | Oui |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | ----------- | ---------- |
| deviceservices | Oui | Oui |

## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

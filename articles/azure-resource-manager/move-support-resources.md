---
title: Prise en charge de l’opération de déplacement par type de ressource Azure | Microsoft Docs
description: Liste les types de ressources Azure qui peuvent être déplacés vers un nouveau groupe de ressources ou un nouvel abonnement.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 6f1869b83f46f97d0c54eb874a8879521a43b1e2
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53387061"
---
# <a name="move-operation-support-for-resources"></a>Prise en charge de l’opération de déplacement pour les ressources

Cet article indique si un type de ressource Azure donné prend en charge l’opération de déplacement. Même si un type de ressource prend en charge l’opération de déplacement, il peut exister des conditions qui empêchent la ressource d’être déplacée. Pour plus d’informations sur les conditions qui affectent les opérations de déplacement, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

## <a name="find-resource-provider-and-resource-type"></a>Rechercher le fournisseur de ressources et le type de ressource

Pour déterminer si une ressource peut être déplacée, vous devez rechercher son fournisseur de ressources et son type de ressource.

Pour PowerShell, utilisez la commande suivante :

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

Le type de ressource est retourné au format `<resource-provider>/<resource-type-name>`. Ainsi, la valeur `Microsoft.OperationalInsights/workspaces` a comme fournisseur de ressources **Microsoft.OperationalInsights** et comme nom de type de ressource **workspaces**.

Après avoir trouvé le fournisseur de ressources et le type de ressource, utilisez les tableaux de cet article pour déterminer si le type de ressource prend en charge l’opération de déplacement.

## <a name="microsoftaad"></a>Microsoft.AAD

| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | --------------- | ----------- |
| domainservices | Non  | Non  |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| servers | Oui | Oui |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | --------------- | ----------- |
| service | Oui | Oui |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | --------------- | ----------- |
| policyassignments | Non  | Non  |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| automationaccounts | Oui | Oui |
| automationaccounts/configurations | Oui | Oui |
| automationaccounts/runbooks | Oui | Oui |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| b2cdirectories | Oui | Oui |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| registrations | Oui | Oui |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| backupvault | Non  | Non  |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| batchaccounts | Oui | Oui |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| mapapis | Non  | Non  |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| biztalk | Oui | Oui |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| blueprintassignments | Non  | Non  |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| botservices | Oui | Oui |

## <a name="microsoftcache"></a>Microsoft.Cache
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| redis | Oui | Oui |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| profils | Oui | Oui |
| profiles/endpoints | Oui | Oui |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| certificateorders | Oui | Oui |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| domainnames | Oui | Non  |
| virtualmachines | Oui | Non  |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Non  | Non  |
| reservedips | Non  | Non  |
| virtualnetworks | Non  | Non  |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| storageaccounts | Oui | Non  |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| availabilitysets | Oui | Oui |
| disks | Oui | Oui |
| galleries | Non  | Non  |
| galleries/images | Non  | Non  |
| galleries/images/versions | Non  | Non  |
| images | Oui | Oui |
| restorepointcollections | Non  | Non  |
| sharedvmimages | Non  | Non  |
| sharedvmimages/versions | Non  | Non  |
| snapshots | Oui | Oui |
| virtualmachines | Oui | Oui |
| virtualmachines/extensions | Oui | Oui |
| virtualmachinescalesets | Oui | Oui |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| containergroups | Non  | Non  |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| containergroups | Non  | Non  |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| registries | Oui | Oui |
| registries/buildtasks | Oui | Oui |
| registries/replications | Non  | Non  |
| registries/tasks | Oui | Oui |
| registries/webhooks | Oui | Oui |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| containerservices | Non  | Non  |
| managedclusters | Non  | Non  |
| openshiftmanagedclusters | Non  | Non  |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| web | Oui | Oui |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| connecteurs | Oui | Oui |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| hubs | Oui | Oui |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| jobs | Non  | Non  |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Non  | Non  |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| workspaces | Non  | Non  |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| catalogs | Oui | Oui |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| datafactories | Oui | Oui |
| factories | Oui | Oui |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Non  | Non  |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| services | Non  | Non  |
| services/projects | Non  | Non  |
| slots | Non  | Non  |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| servers | Non  | Non  |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| servers | Oui | Oui |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| servergroups | Non  | Non  |
| servers | Oui | Oui |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| artifactsources | Non  | Non  |
| rollouts | Non  | Non  |
| servicetopologies | Non  | Non  |
| servicetopologies/services | Non  | Non  |
| servicetopologies/services/serviceunits | Non  | Non  |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| iothubs | Oui | Oui |
| provisioningservices | Oui | Oui |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| labcenters | Non  | Non  |
| labs | Oui | Non  |
| labs/servicerunners | Oui | Oui |
| labs/virtualmachines | Oui | Non  |
| schedules | Non  | Non  |

## <a name="microsoftdns"></a>microsoft.dns
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
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
| ------------- | -------------- | ------------ |
| databaseaccounts | Oui | Oui |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| domaines | Oui | Oui |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| topics | Oui | Oui |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| clusters | Oui | Oui |
| namespaces | Oui | Oui |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| hanainstances | Oui | Oui |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Non  | Non  |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| clusters | Oui | Oui |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| jobs | Oui | Oui |

## <a name="microsoftinsights"></a>microsoft.insights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| actiongroups | Oui | Oui |
| activitylogalerts | Non  | Non  |
| alertrules | Oui | Oui |
| autoscalesettings | Oui | Oui |
| components | Oui | Oui |
| metricalerts | Non  | Non  |
| scheduledqueryrules | Oui | Oui |
| webtests | Oui | Oui |
| workbooks | Oui | Oui |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| iotapps | Oui | Oui |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| vaults | Oui | Oui |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| labaccounts | Oui | Oui |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| integrationaccounts | Oui | Oui |
| workflows | Oui | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| commitmentplans | Oui | Oui |
| webservices | Oui | Non  |
| workspaces | Oui | Oui |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Oui | Oui |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |
| accounts/workspaces | Oui | Oui |
| accounts/workspaces/projects | Oui | Oui |
| teamaccounts | Oui | Oui |
| teamaccounts/workspaces | Oui | Oui |
| teamaccounts/workspaces/projects | Oui | Oui |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| workspaces | Oui | Oui |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| userassignedidentities | Oui | Oui |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| accounts | Oui | Oui |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| classicdevservices | Non  | Non  |

## <a name="microsoftmedia"></a>Microsoft.Media
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| mediaservices | Oui | Oui |
| mediaservices/liveevents | Oui | Oui |
| mediaservices/streamingendpoints | Oui | Oui |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| projects | Non  | Non  |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| applicationgateways | Non  | Non  |
| applicationsecuritygroups | Oui | Oui |
| azurefirewalls | Oui | Oui |
| connections | Oui | Oui |
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
| networkintentpolicies | Oui | Oui |
| networkinterfaces | Oui | Oui |
| networkprofiles | Non  | Non  |
| networksecuritygroups | Oui | Oui |
| networkwatchers | Oui | Oui |
| networkwatchers/connectionmonitors | Oui | Oui |
| networkwatchers/lenses | Oui | Oui |
| networkwatchers/pingmeshes | Oui | Oui |
| publicipaddresses | Oui | Oui |
| publicipprefixes | Oui | Oui |
| routefilters | Non  | Non  |
| routetables | Oui | Oui |
| serviceendpointpolicies | Oui | Oui |
| trafficmanagerprofiles | Oui | Oui |
| virtualhubs | Oui | Oui |
| virtualnetworkgateways | Oui | Oui |
| virtualnetworks | Oui | Oui |
| virtualnetworktaps | Non  | Non  |
| virtualwans | Oui | Oui |
| vpngateways | Oui | Oui |
| vpnsites | Oui | Oui |
| webapplicationfirewallpolicies | Oui | Oui |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| namespaces | Oui | Oui |
| namespaces/notificationhubs | Oui | Oui |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| workspaces | Oui | Oui |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| managementconfigurations | Oui | Oui |
| solutions | Oui | Oui |
| vues | Oui | Oui |

## <a name="microsoftportal"></a>Microsoft.Portal
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| dashboards | Oui | Oui |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| workspacecollections | Oui | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| capacities | Oui | Oui |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| vaults | Oui | Oui |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| namespaces | Oui | Oui |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| web | Oui | Non  |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| flows | Oui | Oui |
| jobcollections | Oui | Oui |

## <a name="microsoftsearch"></a>Microsoft.Search
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| searchservices | Oui | Oui |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| namespaces | Oui | Oui |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| clusters | Oui | Oui |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| web | Oui | Oui |
| networks | Oui | Oui |
| volumes | Oui | Oui |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| signalr | Oui | Oui |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Non  | Non  |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Non  | Non  |
| appliances | Non  | Non  |
| applicationdefinitions | Non  | Non  |
| web | Non  | Non  |

## <a name="microsoftsql"></a>Microsoft.Sql
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| managedinstances | Oui | Oui |
| managedinstances/databases | Oui | Oui |
| servers | Oui | Oui |
| servers/databases | Oui | Oui |
| servers/elasticpools | Oui | Oui |
| virtualclusters | Oui | Oui |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| storageaccounts | Oui | Oui |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| storagesyncservices | Oui | Oui |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| managers | Non  | Non  |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| streamingjobs | Oui | Oui |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| environments | Oui | Oui |
| environments/eventsources | Oui | Oui |
| environments/referencedatasets | Oui | Oui |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| compte | Oui | Oui |
| account/extension | Oui | Oui |
| account/project | Oui | Oui |

## <a name="microsoftweb"></a>Microsoft.Web
| Type de ressource | Groupe de ressources | Abonnement |
| ------------- | -------------- | ------------ |
| certificates | Non  | Oui |
| classicmobileservices | Non  | Non  |
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
| ------------- | -------------- | ------------ |
| deviceservices | Oui | Oui |


## <a name="third-party-services"></a>Services tiers

Actuellement, les services tiers ne prennent pas en charge l’opération de déplacement. Ces fournisseurs de ressources sont :

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).

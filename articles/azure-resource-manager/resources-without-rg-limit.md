---
title: Ressources Azure sans la limite de nombre de 800
description: Répertorie les types de ressources Azure qui peuvent avoir plus de 800 instances dans un groupe de ressources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tomfitz
ms.openlocfilehash: 0f9db618b69ff692350f333b8a4a4354f4980169
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249132"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Ressources non limitées à 800 instances par groupe de ressources

Par défaut, vous pouvez déployer jusqu’à 800 instances d’un type de ressource dans chaque groupe de ressources. Toutefois, certains types de ressources sont exemptés de la limite de 800 instances. Cet article répertorie les types de ressources Azure qui peuvent avoir plus de 800 instances dans un groupe de ressources. Tous les autres types de ressources sont limités à 800 instances.

Pour certains types de ressources, vous devez contacter le support pour que la limite d’instance de 800 soit supprimée. Ces types de ressources sont mentionnés dans cet article.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices : par défaut, limité à 800 instances. Cette limite peut être augmentée en contactant le support.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* images
* snapshots
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses : par défaut, limité à 800 instances. Cette limite peut être augmentée en contactant le support.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* publicIPAddresses : par défaut, limité à 800 instances. Cette limite peut être augmentée en contactant le support.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* applications
* containerGroups
* gateways
* networks
* secrets
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Étapes suivantes

Pour une liste complète des quotas et des limites, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](../azure-subscription-service-limits.md).

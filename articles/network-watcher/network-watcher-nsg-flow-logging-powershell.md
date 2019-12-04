---
title: Gérer des journaux de flux NSG - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Cette page explique comment gérer les journaux d’activité des flux de groupe de sécurité réseau dans Azure Network Watcher avec PowerShell
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 162e499ba44557db388484639c21877fd8b852bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277962"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configuration des journaux d’activité des flux de groupe de sécurité réseau avec PowerShell

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interface de ligne de commande Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Les journaux de flux de groupe de sécurité réseau désignent une fonctionnalité de Network Watcher qui vous permet d’afficher des informations sur le trafic IP entrant et sortant d’un groupe de sécurité réseau. Ces journaux de flux sont écrits au format json et affichent les flux entrants et sortants en fonction de règles, de la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), et de l’autorisation ou du refus du trafic.

## <a name="register-insights-provider"></a>Inscription du fournisseur Insights

Pour que les journaux de flux puissent correctement fonctionner, le fournisseur **Microsoft.Insights** doit être inscrit. Si vous ne savez pas si le fournisseur **Microsoft.Insights** est inscrit, exécutez le script suivant.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Activer les journaux de flux de groupe de sécurité réseau et Traffic Analytics

La commande d’activation des journaux de flux est illustrée dans l’exemple suivant :

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Le compte de stockage que vous spécifiez ne peut pas avoir de règles réseau configurées qui limitent l’accès réseau pour les services Microsoft ou des réseaux virtuels spécifiques uniquement. Le compte de stockage peut être dans le même abonnement Azure que le groupe de sécurité réseau pour lequel vous activez le journal de flux, ou dans un autre abonnement. Si vous utilisez des abonnements différents, ils doivent tous deux être associés au même locataire Azure Active Directory. Le compte que vous utilisez pour chaque abonnement doit avoir les [autorisations nécessaires](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Désactiver Traffic Analytics et les journaux de flux de groupe de sécurité réseau

L’exemple suivant permet de désactiver l’analytique du trafic et les journaux de flux :

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Télécharger un journal de flux

L’emplacement de stockage d’un journal de flux est défini au moment de la création. L’Explorateur Stockage Microsoft Azure est un outil très pratique pour accéder à ces journaux de flux enregistrés dans un compte de stockage. Vous pouvez le télécharger ici : https://storageexplorer.com/

Si un compte de stockage est spécifié, les fichiers journaux de flux sont enregistrés dans un compte de stockage à l’emplacement suivant :

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
> [!IMPORTANT]
> Un problème empêche actuellement la suppression automatique des [journaux de flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md) pour Network Watcher du stockage Blob en fonction des paramètres de stratégie de conservation. Si vous avez une stratégie de rétention différente de zéro, nous vous recommandons de supprimer régulièrement les blobs de stockage qui ont dépassé leur période de rétention afin d’éviter des frais supplémentaires. Pour plus d’informations sur la suppression des objets blob de stockage du journal de flux NSG, consultez [Supprimer les objets blob de stockage du journal de flux NSG](network-watcher-delete-nsg-flow-log-blobs.md).

Pour plus d’informations sur la structure du journal, consultez [Network Security Group Flow log Overview (Présentation de la journalisation des flux de groupe de sécurité réseau)](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [visualiser vos journaux d’activité des flux de groupe de sécurité réseau avec Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Découvrez comment [visualiser vos journaux de flux de groupe de sécurité réseau avec des outils open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md).

---
title: Gérer les journaux des flux de groupe de sécurité réseau avec Azure Network Watcher - Azure CLI | Microsoft Docs
description: Cette page explique comment gérer les journaux des flux de groupe de sécurité réseau dans Azure Network Watcher avec l’interface de ligne de commande Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 61674cc5551fb0d1be184de0ff0ed659cc56bbbe
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340244"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Configuration des journaux des flux de groupe de sécurité réseau avec l’interface de ligne de commande Azure

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interface de ligne de commande Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Les journaux des flux de groupe de sécurité réseau désignent une fonctionnalité de Network Watcher qui vous permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Ces flux de journaux sont écrits au format json et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), ainsi que l’autorisation ou le refus du trafic.

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Inscription du fournisseur Insights

Pour que les journaux de flux puissent correctement fonctionner, le fournisseur **Microsoft.Insights** doit être inscrit. Si vous ne savez pas si le fournisseur **Microsoft.Insights** est inscrit, exécutez le script suivant.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Activer les journaux des flux de groupe de sécurité réseau

La commande d’activation des journaux de flux est illustrée dans l’exemple suivant :

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Le compte de stockage que vous spécifiez ne peut pas avoir de règles réseau configurées qui limitent l’accès réseau pour les services Microsoft ou des réseaux virtuels spécifiques uniquement. Le compte de stockage peut être dans le même abonnement Azure que le groupe de sécurité réseau pour lequel vous activez le journal de flux, ou dans un autre abonnement. Si vous utilisez des abonnements différents, ils doivent tous deux être associés au même locataire Azure Active Directory. Le compte que vous utilisez pour chaque abonnement doit avoir les [autorisations nécessaires](required-rbac-permissions.md). 

Si le compte de stockage se trouve dans un autre groupe de ressources ou un autre abonnement que le groupe de sécurité réseau, spécifiez l’ID complet du compte de stockage plutôt que son nom. Par exemple, si le compte de stockage figure dans un groupe de ressources nommé *RG-Storage*, au lieu de spécifier *storageAccountName* dans la commande précédente, vous indiqueriez */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Désactiver les journaux des flux de groupe de sécurité réseau

Utilisez l’exemple suivant pour désactiver les journaux de flux :

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Télécharger un journal de flux

L’emplacement de stockage d’un journal de flux est défini au moment de la création. L’Explorateur Stockage Microsoft Azure est un outil très pratique pour accéder à ces journaux de flux enregistrés dans un compte de stockage. Vous pouvez le télécharger ici : http://storageexplorer.com/

Si un compte de stockage est spécifié, les fichiers journaux de flux sont enregistrés dans un compte de stockage à l’emplacement suivant :

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Pour plus d’informations sur la structure du journal, consultez [Network Security Group Flow log Overview (Présentation de la journalisation des flux de groupe de sécurité réseau)](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [visualiser vos journaux de flux de groupe de sécurité réseau avec Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

Découvrez comment [visualiser vos journaux de flux de groupe de sécurité réseau avec des outils open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md).

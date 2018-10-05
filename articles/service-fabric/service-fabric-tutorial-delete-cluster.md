---
title: Supprimer un cluster Service Fabric dans Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment supprimer un cluster Service Fabric hébergé par Azure et toutes ses ressources. Vous pouvez supprimer le groupe de ressources contenant le cluster ou supprimer des ressources de manière sélective.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 70c5fa5de627b69623b1cce6929615f4e99e2a05
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410806"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Didacticiel : Supprimer un cluster Service Fabric dans Azure

Quatrième d’une série, ce didacticiel vous montre comment supprimer un cluster Service Fabric fonctionnant dans Azure. Pour supprimer complètement un cluster Service Fabric, vous devez également supprimer les ressources utilisées par le cluster. Vous pouvez procéder de deux façons : supprimer le groupe de ressources dans lequel est situé le cluster (ce qui supprime la ressource de cluster et toutes les autres ressources du groupe de ressources) ou supprimer spécifiquement la ressource de cluster et les ressources associées (mais pas d’autres ressources du groupe de ressources).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Supprimer un groupe de ressources et toutes ses ressources
> * Supprimer des ressources de manière sélective à partir d’un groupe de ressources

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure à l’aide d’un modèle
> * [Mettre à l’échelle un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * Suppression d'un cluster

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installez le [module Azure PowerShell 4.1 ou version ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI](/cli/azure/install-azure-cli).
* Créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Supprimer le groupe de ressources contenant le cluster Service Fabric
Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources.

Connectez-vous à Azure et sélectionnez l’ID d’abonnement pour lequel vous souhaitez supprimer le cluster.  Vous pouvez trouver votre ID d’abonnement en vous connectant au [portail Azure](http://portal.azure.com). Supprimez le groupe de ressources et toutes les ressources de cluster à l’aide de la cmdlet [Remove-AzureRMResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) ou de la commande [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Supprimer de manière sélective la ressource de cluster et les ressources associées
Si votre groupe de ressources comporte uniquement des ressources qui sont liées au cluster Service Fabric à supprimer, il est plus facile de supprimer l’ensemble du groupe de ressources. Si vous souhaitez supprimer de manière sélective les ressources dans votre groupe de ressources et conserver des ressources qui ne sont pas associées au cluster, procédez comme suit.

Répertoriez les ressources dans le groupe de ressources :

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzureRmResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Pour chacune des ressources à supprimer, exécutez le script suivant :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Pour supprimer la ressource de cluster, exécutez le script suivant :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Supprimer un groupe de ressources et toutes ses ressources
> * Supprimer des ressources de manière sélective à partir d’un groupe de ressources

Maintenant que vous avez terminé ce didacticiel, essayez ce qui suit :
* Découvrez comment inspecter et gérer un cluster Service Fabric à l’aide de [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Découvrez comment [appliquer des correctifs au système d’exploitation Windows](service-fabric-patch-orchestration-application.md) ou [appliquer des correctifs au système d’exploitation Linux](service-fabric-patch-orchestration-application-linux.md) des nœuds du cluster.
* Découvrez comment agréger et collecter des événements pour les [clusters Windows](service-fabric-diagnostics-event-aggregation-wad.md) ou les [clusters Linux](service-fabric-diagnostics-event-aggregation-lad.md) et [configurer Log Analytics](service-fabric-diagnostics-oms-setup.md) pour surveiller les événements de cluster.
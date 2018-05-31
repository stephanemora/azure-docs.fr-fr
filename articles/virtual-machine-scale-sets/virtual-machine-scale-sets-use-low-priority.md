---
title: Créer un groupe identique Azure utilisant des machines virtuelles basse priorité (préversion) | Microsoft Docs
description: Découvrez comment créer des groupes identiques Azure qui utilisent des machines virtuelles basse priorité pour réduire vos coûts
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 5c0726ea0da288d5306e28b101e4d3b59605b443
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894896"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Machines virtuelles basse priorité dans des groupes identiques (préversion)

L’utilisation de machines virtuelles basse priorité dans des groupes identiques vous permet de disposer de notre capacité inutilisée et ainsi de réduire nettement vos coûts. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure évince les machines virtuelles basse priorité. Les machines virtuelles basse priorité sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les grosses charges de calcul, entre autres.

La capacité inutilisée disponible dépend de divers facteurs, tels que la taille, la région, l’heure, etc. Quand des machines virtuelles basse priorité sont déployées dans des groupes identiques, Azure alloue la capacité disponible aux machines virtuelles, le cas échéant. Sachez toutefois qu’il n’y a pas de contrat SLA pour ces machines virtuelles. Un groupe identique basse priorité est déployé dans un domaine d’erreur unique. Il n’offre pas de garantie de haute disponibilité.

## <a name="eviction-policy"></a>Stratégie d’éviction

Quand vous créez des groupes identiques basse priorité, vous pouvez *Libérer* (par défaut) ou *Supprimer* la stratégie d’éviction. 

La stratégie *Libérer* affecte à vos machines virtuelles écartées l’état « arrêté-libéré », ce qui vous permet de redéployer les instances écartées. Toutefois, la réussite de l’allocation n’est pas garantie. Les machines virtuelles libérées sont comptabilisées dans votre quota d’instances de groupe identique, et vos disques sous-jacents vous sont facturés. 

Si vous souhaitez que les machines virtuelles dans votre groupe identique basse priorité soient supprimées après avoir été écartées, définissez la stratégie d’éviction avec la valeur *Supprimer*. Avec cette configuration, vous pouvez créer d’autres machines virtuelles en définissant la propriété du nombre d’instances du groupe identique à une valeur plus grande. Les machines virtuelles évincées sont supprimées en même temps que leurs disques sous-jacents. Vous n’êtes donc pas facturé pour le stockage. Vous pouvez également utiliser la fonctionnalité de mise à l’échelle automatique des groupes identiques pour essayer et compenser automatiquement les machines virtuelles évincées, mais sans garantie de réussite de l’allocation. Nous vous recommandons d’utiliser uniquement la mise à l’échelle automatique des groupes identiques basse priorité quand vous définissez la stratégie d’éviction avec suppression pour éviter la facturation de vos disques et le dépassement de vos limites de quota. 

> [!NOTE]
> Dans la préversion, vous pouvez définir la stratégie d’éviction à l’aide du [portail Azure](#use-the-azure-portal) et des [modèles Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Déployer des machines virtuelles basse priorité dans des groupes identiques

Pour déployer des machines virtuelles basse priorité dans des groupes identiques, définissez le nouvel indicateur *Priority* sur *Low*. Toutes les machines virtuelles dans votre groupe identique sont alors configurées en basse priorité. Pour créer un groupe identique avec des machines virtuelles basse priorité, utilisez l’une des méthodes suivantes :
- [Portail Azure](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modèles Microsoft Azure Resource Manager](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Le processus de création d’un groupe identique utilisant des machines virtuelles basse priorité est identique à celui décrit dans [l’article de démarrage rapide](quick-create-portal.md). Quand vous déployez un groupe identique, vous pouvez choisir de définir l’indicateur de basse priorité et la stratégie d’éviction : ![Créer un groupe identique avec des machines virtuelles basse priorité](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans [l’article de démarrage rapide](quick-create-cli.md). Ajoutez simplement le paramètre '--Priority' à l’appel CLI et définissez-le sur *Low*, comme indiqué dans l’exemple ci-dessous :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans [l’article de démarrage rapide](quick-create-powershell.md).
Ajoutez simplement le paramètre '-Priority' à [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) et définissez-le sur *Low*, comme indiqué dans l’exemple ci-dessous :

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Utiliser les modèles Azure Resource Manager

Le processus de création d’un groupe identique avec des machines virtuelles basse priorité est identique à celui décrit dans l’article de démarrage rapide pour [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Ajoutez la propriété 'priority' au type de ressource *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* dans votre modèle et définissez la propriété sur *Low*. Veillez à utiliser l’API version *2018-03-01* ou ultérieure. 

Pour configurer la stratégie d’éviction avec suppression, ajoutez le paramètre 'evictionPolicy' défini sur *delete*.

L’exemple suivant crée un groupe identique basse priorité pour Linux, nommé *myScaleSet*, dans la région *West Central US*, avec *suppression* des machines virtuelles du groupe identique qui sont évincées :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>Forum Aux Questions

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Puis-je convertir des groupes identiques existants en groupes identiques basse priorité ?
Non. La définition de l’indicateur de basse priorité n’est prise en charge qu’au moment de la création.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Puis-je créer un groupe identique avec des machines virtuelles normales et basse priorité ?
Non. Un groupe identique ne prend en charge qu’un seul type de priorité.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Comment les quotas sont-ils gérés pour les machines virtuelles basse priorité ?
Les machines virtuelles normales et basse priorité partagent le même pool de quotas. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Puis-je utiliser la mise à l’échelle automatique avec des groupes identiques basse priorité ?
Oui. Vous pouvez définir des règles de mise à l’échelle automatique sur votre groupe identique basse priorité. Si vos machines virtuelles sont écartées, la mise à l’échelle automatique peut essayer de créer des machines virtuelles basse priorité. N’oubliez pas que cette fonctionnalité n’est pas garantie. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>La mise à l’échelle automatique fonctionne-t-elle avec les deux stratégies d’éviction (Libérer et Supprimer) ?
Il est recommandé de choisir la stratégie d’éviction Supprimer avec la mise à l’échelle automatique. En effet, le nombre d’instances libérées est soustrait de la capacité sur le groupe identique. Quand vous utilisez la mise à l’échelle automatique, le nombre d’instances cibles est souvent rapidement atteint en raison des instances libérées et écartées. 

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé un groupe identique avec des machines virtuelles basse priorité. Essayez maintenant de déployer notre [modèle de mise à l’échelle automatique des machines virtuelles basse priorité](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Consultez la page [Tarification des groupes identiques de machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) pour connaître les tarifs appliqués.
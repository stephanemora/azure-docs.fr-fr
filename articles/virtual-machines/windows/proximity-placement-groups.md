---
title: Utiliser des groupes de placements de proximité pour les machines virtuelles Windows | Microsoft Docs
description: En savoir plus sur la création et l’utilisation de groupes de placements de proximité pour les machines virtuelles Windows dans Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: af75b3f98232d6507fc8b0fda179bebc75828086
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088837"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Aperçu : Déployer des machines virtuelles dans des groupes de placements avec PowerShell


Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un [groupe de placements de proximité](co-location.md#preview-proximity-placement-groups).

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.

> [!IMPORTANT]
> Les groupes de placements de proximité sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Les groupes de placements de proximité ne sont pas disponibles dans ces régions en préversion : **Japon Est**, **Australie Est** et **Inde Centre**.


## <a name="create-a-proximity-placement-group"></a>Créer un groupe de placements de proximité
Créez un groupe de placements de proximité à l’aide de la cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup). 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Liste de groupes de placements de proximité

Vous pouvez créer une liste de tous les groupes de placements de proximité à l’aide de la cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle dans le groupe de placements de proximité avec `-ProximityPlacementGroup $ppg.Id` pour faire référence à l’ID de groupe de placements de proximité quand vous utilisez [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) pour créer la machine virtuelle.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

Vous pouvez voir la machine virtuelle dans le groupe de placements avec [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>Groupes à haute disponibilité
Vous pouvez également créer un groupe à haute disponibilité dans votre groupe de placements de proximité. Utilisez le même paramètre `-ProximityPlacementGroup`avec la cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) pour créer un groupe à haute disponibilité et toutes les machines virtuelles créées dans le groupe à haute disponibilité seront également créées dans le même groupe de placements de proximité.

## <a name="scale-sets"></a>Groupes identiques

Vous pouvez également créer un groupe identique dans votre groupe de placements de proximité. Utilisez le même paramètre `-ProximityPlacementGroup` avec [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) pour créer un jeu de mise à l’échelle et toutes les instances seront créées dans le même groupe de placements de proximité.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également utiliser [Azure CLI](../linux/proximity-placement-groups.md) pour créer des groupes de placements de proximité.

---
title: Utiliser des groupes de placements de proximité pour des machines virtuelles Windows
description: En savoir plus sur la création et l’utilisation de groupes de placements de proximité pour les machines virtuelles Windows dans Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171207"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Déployer des machines virtuelles dans des groupes de placements avec PowerShell


Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un [groupe de placements de proximité](co-location.md#proximity-placement-groups).

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.


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

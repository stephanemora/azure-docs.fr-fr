---
title: Supprimer une association de groupe de machines virtuelles identiques d’un groupe de réservations de capacité (version préliminaire)
description: Découvrez comment supprimer un groupe de machines virtuelles identiques d’un groupe de réservations de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 2d8f9c7c73b4cb5d0f617893a7d981b94d30b344
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553094"
---
# <a name="remove-a-virtual-machine-scale-set-association-from-a-capacity-reservation-group"></a>Supprimer une association de groupe de machines virtuelles identiques d’un groupe de réservations de capacité 

Cet article vous guide tout au long des étapes de suppression d’une association de groupe de machines virtuelles identiques d’un groupe de réservations de capacité. Pour en savoir plus sur les réservations de capacité, consultez l'[article vue d’ensemble](capacity-reservation-overview.md). 

Étant donné que la machine virtuelle et la réservation de capacité sous-jacente occupent la capacité logiquement, Azure impose certaines contraintes sur ce processus afin d’éviter des états d’allocation ambigus et des erreurs inattendues.  

Vous pouvez modifier l’association de deux façons : 
- Option 1 : libérer le groupe de machines virtuelles identiques, changer la propriété de groupe de réservations de capacité au niveau du groupe identique, puis mettre à jour les machines virtuelles sous-jacentes
- Option 2 : mettre à jour la quantité réservée à zéro, puis modifier la propriété du groupe de réservations de capacité

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>S’inscrire à la réservation de capacité 

Avant de pouvoir utiliser la fonctionnalité Réservation de capacité, vous devez [inscrire votre abonnement pour la préversion](capacity-reservation-overview.md#register-for-capacity-reservation). L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser Azure CLI ou PowerShell pour finaliser l’inscription de la fonctionnalité.

> [!NOTE]
> La réservation de capacité à la demande est disponible pour les groupes de machines virtuelles identiques en mode d’orchestration uniforme uniquement dans les régions sélectionnées. Pour vérifier si votre région est prise en charge, accédez au [Traqueur de déploiement de groupe de machines virtuelles identiques uniformes](https://aka.ms/vmssuniformdeploymenttracker).


## <a name="deallocate-the-virtual-machine-scale-set"></a>Libérer un groupe de machines virtuelles identiques

La première option est de libérer le groupe de machines virtuelles identiques, changer la propriété de groupe de réservations de capacité au niveau du groupe identique, puis mettre à jour les machines virtuelles sous-jacentes. 

Accédez à [stratégies de mise à niveau](#upgrade-policies) pour plus d’informations sur les mises à niveau automatiques, propagées et manuelles. 

### <a name="api"></a>[API](#tab/api1)

1. Libérer un groupe de machines virtuelles identiques

    ```rest
    POST  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Mettre à jour le groupe de machines virtuelles identiques pour supprimer l’association avec le groupe de réservations de capacité
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```
    Dans le corps de la demande, affectez à la propriété `capacityReservationGroup` la valeur null pour supprimer l’association de groupe de machines virtuelles identiques au groupe :

    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null    
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Libérer le groupe de machines virtuelles identiques. La commande suivante permet de libérer toutes les machines virtuelles du groupe identique : 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    ```

1. Mettre à jour le groupe identique pour supprimer l’association avec le groupe de réservations de capacité. Affecter à la propriété `CapacityReservationGroupId` la valeur null supprime l’association du groupe identique au groupe de réservations de capacité : 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss), et [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Mettez à jour la quantité réservée à zéro 

La deuxième option consiste à mettre à jour la quantité réservée à zéro, puis à modifier la propriété du groupe de réservations de capacité.

Cette option fonctionne bien lorsque le groupe de machines virtuelles identiques ne peut pas être libéré et lorsqu’une réservation n’est plus nécessaire. Par exemple, vous pouvez créer une réservation de capacité pour garantir temporairement la capacité pendant un déploiement à grande échelle. Une fois l’opération terminée, la réservation n’est plus nécessaire. 

Accédez à [stratégies de mise à niveau](#upgrade-policies) pour plus d’informations sur les mises à niveau automatiques, propagées et manuelles. 

### <a name="api"></a>[API](#tab/api2)

1. Mettez à jour la quantité réservée à zéro 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/CapacityReservations/{CapacityReservationName}?api-version=2021-04-01
    ```

    Dans le corps de la demande, ajoutez les éléments suivants :
    
    ```json
    {
    "sku": 
        {
        "capacity": 0
        }
    } 
    ```
    
    Notez que la propriété `capacity` est définie sur 0 et plus.

1. Mettez à jour le groupe de machines virtuelles identiques pour supprimer l’association avec le groupe de réservations de capacité

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/update?api-version=2021-04-01
    ```

    Dans le corps de la demande, affectez à la propriété `capacityReservationGroup` la valeur null pour supprimer l’association :
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
            "capacityReservation": {
                "capacityReservationGroup":{
                    "id":null
                }
            }
        }
    }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Mettez à jour la quantité réservée à zéro :

    ```powershell-interactive
    Update-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -CapacityToReserve 0
    ```

2. Mettez à jour le groupe identique pour supprimer l’association avec le groupe de réservations de capacité en affectant à la propriété `CapacityReservationGroupId` la valeur null : 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myvmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId $null
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVmss](/powershell/module/az.compute/get-azvmss), et [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="upgrade-policies"></a>Stratégies de mise à niveau

- **Mise à niveau automatique** : dans ce mode, les instances de machine virtuelle du groupe identique sont automatiquement dissociées au groupe de réservations de capacité sans aucune autre action de votre part.
- **Mise à niveau propagée** : dans ce mode, les instances de machine virtuelle du groupe identique sont dissociées au groupe de réservations de capacité sans aucune autre action de votre part. Toutefois, elles sont mises à jour par lots avec une durée de pause facultative entre elles.
- **Mise à niveau manuelle** : dans ce mode, rien n’arrive aux instances de machine virtuelle du groupe identique lorsque le groupe de machines virtuelles identiques est mis à niveau. Vous devez enlever individuellement chaque machine virtuelle du groupe identique en [la mettant à niveau avec le dernier modèle de groupe identique](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la surallocation d’une réservation de capacité](capacity-reservation-overallocate.md)

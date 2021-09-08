---
title: Supprimer une association de machines virtuelles d’un groupe de réservations de capacité (préversion)
description: Découvrez comment supprimer une machine virtuelle d’un groupe de réservations de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 2f5537ec3ad34e3f0ad7eff32d32762ed6fedef3
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273379"
---
# <a name="remove-a-vm-association-from-a-capacity-reservation-group-preview"></a>Supprimer une association de machines virtuelles d’un groupe de réservations de capacité (préversion)

Cet article vous guide tout au long des étapes de suppression d’une association de machines virtuelles à un groupe de réservations de capacité. Pour en savoir plus sur les réservations de capacité, consultez l'[article vue d’ensemble](capacity-reservation-overview.md). 

Étant donné que la machine virtuelle et la réservation de capacité sous-jacente occupent la capacité logiquement, Azure impose certaines contraintes sur ce processus afin d’éviter des états d’allocation ambigus et des erreurs inattendues.  

Vous pouvez modifier l’association de deux façons : 
- Option 1 : libérer la machine virtuelle, modifier la propriété de groupe de réservation de capacité et éventuellement redémarrer la machine virtuelle
- Option 2 : mettre à jour la quantité réservée à zéro, puis modifier la propriété du groupe de réservations de capacité

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-for-capacity-reservation"></a>S’inscrire à la réservation de capacité 

Avant de pouvoir utiliser la fonctionnalité Réservation de capacité, vous devez [inscrire votre abonnement à la préversion](capacity-reservation-overview.md#register-for-capacity-reservation). L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser Azure CLI ou PowerShell pour finaliser l’inscription de la fonctionnalité.


## <a name="deallocate-the-vm"></a>Libérer la machine virtuelle

La première option est de libérer la machine virtuelle, modifier la propriété de groupe de réservation de capacité et éventuellement redémarrer la machine virtuelle. 

### <a name="api"></a>[API](#tab/api1)

1. Libérer la machine virtuelle

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Mettre à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/update?api-version=2021-04-01
    ```
    Dans le corps de la demande, affectez à la propriété `capacityReservationGroup` la valeur vide pour supprimer l’association de machines virtuelles au groupe :

    ```json
     {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    }
    ```

### <a name="portal"></a>[Portail](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Allez vers votre machine virtuelle, puis choisissez **Vue d’ensemble**
1. Sélectionnez **Arrêter** 
    1. Vous savez que votre machine virtuelle est libérée lorsque l’état passe à *Arrêté (libéré)*
    1. À ce stade du processus, la machine virtuelle est toujours associée au groupe de réservations de capacité, qui est reflétée dans la propriété `virtualMachinesAssociated` de la réservation de capacité 
1. Sélectionnez **Configuration**.
1. Définissiez la valeur **Groupe de réservation de capacité** sur *Aucun*
    - La machine virtuelle n’est plus associée au groupe de réservations de capacité 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Libérez la machine virtuelle

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

    Vous savez que votre machine virtuelle est libérée lorsque l’état passe à **Arrêté (libéré)** .

1. Mettez à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité en affectant à la propriété `CapacityReservationGroupId` la valeur vide :

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm), et [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="update-the-reserved-quantity-to-zero"></a>Mettez à jour la quantité réservée à zéro 

La deuxième option consiste à mettre à jour la quantité réservée à zéro, puis à modifier la propriété du groupe de réservations de capacité.

Cette option fonctionne bien lorsque la machine virtuelle ne peut pas être libérée et lorsqu’une réservation n’est plus nécessaire. Par exemple, vous pouvez créer une réservation de capacité pour garantir temporairement la capacité pendant un déploiement à grande échelle. Une fois l’opération terminée, la réservation n’est plus nécessaire. 

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
    
    Notez également que la propriété `capacity` est définie sur 0 et plus.

1. Mettez à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/update?api-version=2021-04-01
    ```

    Dans le corps de la demande, affectez à la propriété `capacityReservationGroup` la valeur vide pour supprimer l’association :
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id":""
            }
        }
    }
    } 
    ```

### <a name="portal"></a>[Portail](#tab/portal2)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Allez sur votre groupe de réservation de capacité et choisissez **Vue d’ensemble**
1. Choisissez **Réservations** 
1. En haut de la page, sélectionnez **Gérer les réservations** 
1. Sur le panneau *Gérer les réservations* :
    1. Entrez `0` dans le champs **Instances**
    1. Sélectionnez **Enregistrer**. 
1. Allez vers votre machine virtuelle, puis choisissez **Configuration**
1. Définissiez la valeur **Groupe de réservation de capacité** sur *Aucun*
    - La machine virtuelle n’est plus associée au groupe de réservations de capacité

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

>[!NOTE]
> La commande `Update-AzCapacityReservation` n’est pas disponible pendant la version préliminaire. Utilisez `New-AzCapacityReservation` pour modifier une réservation de capacité existante.

1. Mettez à jour la quantité réservée à zéro

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 0
    ```

1. Mettez à jour la machine virtuelle pour supprimer l’association avec le groupe de réservations de capacité en affectant à la propriété `CapacityReservationGroupId` la valeur vide :

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId " "
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation), [Get-AzVM](/powershell/module/az.compute/get-azvm), et [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment associer un groupe identique à un groupe de réservations de capacité](capacity-reservation-associate-virtual-machine-scale-set.md)
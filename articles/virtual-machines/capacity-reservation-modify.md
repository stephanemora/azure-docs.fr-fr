---
title: Modifier une réservation de capacité dans Azure (version préliminaire)
description: Découvrez comment modifier une réservation de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 25b6eaea3c639d39721bd80aaad08cf2c6f80380
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066468"
---
# <a name="modify-a-capacity-reservation-preview"></a>Modifier une réservation de capacité (version préliminaire)

Après avoir créé un groupe de réservations de capacité et une réservation de capacité, vous souhaiterez peut-être modifier vos réservations. Cet article explique comment effectuer les opérations suivantes à l’aide de l’API, du portail Azure et de PowerShell. 

> [!div class="checklist"]
> * Mettez à jour le nombre d’instances réservées dans une réservation de capacité 
> * Redimensionnez les machines virtuelles associées à un groupe de réservations de capacité
> * Supprimez le groupe de réservations de capacité et la réservation de capacité

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="update-the-number-of-instances-reserved"></a>Mettez à jour le nombre d’instances réservées 

Mettez à jour le nombre d’instances de machine virtuelle réservées dans une réservation de capacité.  

> [!IMPORTANT]
> Dans de rares cas où Azure ne peut pas traiter la demande pour augmenter la quantité réservée pour les réservations de capacité existantes, il est possible qu’une réservation passe dans un état *échec* et devienne indisponible jusqu’à ce que la [quantité soit restaurée sur le montant d’origine](#restore-instance-quantity).

### <a name="api"></a>[API](#tab/api1)

```rest
    PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
``` 
    
Dans le corps de la demande, mettez à jour la propriété `capacity` avec le nouveau nombre que vous souhaitez réserver : 
    
```json
{
    "sku":
    {
        "capacity": 5
    }
} 
```

Notez que la propriété `capacity` a la valeur 5 maintenant dans cet exemple.


### <a name="portal"></a>[Portail](#tab/portal1) 

1. Ouvrez le [portail Azure](https://portal.azure.com)
1. Allez à votre groupe de réservations de capacité
1. Sélectionnez **Vue d’ensemble** 
1. Sélectionnez **Réservations** 
1. En haut de la page, sélectionnez **Gérer les réservations** 
1. Dans la page *Gérer les réservations*, entrez la nouvelle quantité à réserver dans le champ **Instances** 
1. Sélectionnez **Enregistrer**. 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli1)
Pour mettre à jour la quantité réservée, utilisez `az capacity reservation update` avec la propriété mise à jour `capacity `.

 ```azurecli-interactive
 az capacity reservation update 
 -c myCapacityReservationGroup 
 -n myCapacityReservation 
 -g myResourceGroup2 
 --capacity 5
 ```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Pour mettre à jour la quantité réservée, utilisez `New-AzCapacityReservation` avec la propriété mise à jour `capacityToReserve`.

```powershell-interactive
Update-AzCapacityReservation
-ResourceGroupName "myResourceGroup"
-ReservationGroupName "myCapacityReservationGroup"
-Name "myCapacityReservation"
-CapacityToReserve 5
```

Pour plus d’informations, accédez à la commande Azure PowerShell [Update-AzCapacityReservation](/powershell/module/az.compute/update-azcapacityreservation).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="resize-vms-associated-with-a-capacity-reservation-group"></a>Redimensionnez les machines virtuelles associées à un groupe de réservations de capacité 

Si la machine virtuelle redimensionnée est actuellement attachée à un groupe de réservations de capacité et que ce groupe n’a pas de réservation pour la taille cible, créez une nouvelle réservation pour cette taille ou supprimez la machine virtuelle du groupe de réservations avant le redimensionnement. 

Vérifiez si la taille cible fait partie du groupe de réservations : 

### <a name="api"></a>[API](#tab/api2)

1. Obtenez le nom de toutes les réservations de capacité au sein du groupe.
 
    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "<CapacityReservationGroupName>", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
        "type": "Microsoft.Compute/capacityReservationGroups", 
        "location": "eastUS", 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "capacityReservations": [ 
                { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}" 
                }, 
    { 
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName2}" 
                } 
            ] 
        } 
    } 
    ```

1. Déterminez la taille de machine virtuelle réservée pour chaque réservation. L’exemple suivant est destiné à `capacityReservationName1`, mais vous pouvez répéter cette étape pour d’autres réservations.

    ```rest
        GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}?api-version=2021-04-01
    ``` 
    
    ```json
    { 
        "name": "capacityReservationName1", 
        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName1}", 
        "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
        "location": "eastUS", 
        "sku": { 
            "name": "Standard_D2s_v3", 
            "capacity": 3 
        }, 
        "zones": [ 
            "1" 
        ], 
        "properties": { 
            "reservationId": "<reservationId>", 
            "provisioningTime": "<provisioningTime>", 
            "provisioningState": "Succeeded" 
        } 
    }  
    ```

1. Tenez compte des éléments suivants : 
    1. Si la taille de machine virtuelle cible ne fait pas partie du groupe, [créez une nouvelle réservation de capacité](capacity-reservation-create.md) pour la machine virtuelle cible 
    1. Si la taille de machine virtuelle cible existe déjà dans le groupe, [redimensionnez la machine virtuelle](resize-vm.md) 

### <a name="portal"></a>[Portail](#tab/portal2)

1. Ouvrez le [portail Azure](https://portal.azure.com)
1. Allez à votre groupe de réservations de capacité
1. Sélectionnez **Vue d’ensemble** 
1. Sélectionnez **Réservations** 
1. Déterminez la *taille de machine virtuelle* réservée pour chaque réservation 
    1. Si la taille de machine virtuelle cible ne fait pas partie du groupe, [créez une nouvelle réservation de capacité](capacity-reservation-create.md) pour la machine virtuelle cible 
    1. Si la taille de machine virtuelle cible existe déjà dans le groupe, [redimensionnez la machine virtuelle](resize-vm.md) 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli2)

1. Obtenez le nom de toutes les réservations de capacité au sein du groupe de réservation de capacité avec `az capacity reservation group show`

    ```azurecli-interactive
    az capacity reservation group show 
    -g myResourceGroup
    -n myCapacityReservationGroup 
    ```

1. À partir de la réponse, recherchez le nom de toutes les réservations de capacité

1. Exécutez les commandes suivantes pour déterminer la ou les tailles de machine virtuelle réservées pour chaque réservation

    ```azurecli-interactive
    az capacity reservation show
    -g myResourceGroup
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. Tenez compte des éléments suivants : 
    1. Si la taille de machine virtuelle cible ne fait pas partie du groupe, [créez une nouvelle réservation de capacité](capacity-reservation-create.md) pour la machine virtuelle cible 
    1. Si la taille de machine virtuelle cible existe déjà dans le groupe, [redimensionnez la machine virtuelle](resize-vm.md) 


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Obtenez le nom de toutes les réservations de capacité au sein du groupe avec `Get-AzCapacityReservationGroup`

    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. À partir de la réponse, recherchez le nom de toutes les réservations de capacité

1. Exécutez les commandes suivantes pour déterminer la ou les tailles de machine virtuelle réservées pour chaque réservation

    ```powershell-interactive
    $CapRes =
    Get-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "mycapacityReservation"
    
    $CapRes.Sku
    ```

1. Tenez compte des éléments suivants : 
    1. Si la taille de machine virtuelle cible ne fait pas partie du groupe, [créez une nouvelle réservation de capacité](capacity-reservation-create.md) pour la machine virtuelle cible 
    1. Si la taille de machine virtuelle cible existe déjà dans le groupe, [redimensionnez la machine virtuelle](resize-vm.md) 


Pour plus d’informations, accédez aux commandes Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup) et [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="delete-a-capacity-reservation-group-and-capacity-reservation"></a>Supprimez le groupe de réservations de capacité et la réservation de capacité 

Azure permet de supprimer un groupe de réservations de capacité lorsque toutes les réservations de capacité des membres ont été supprimées et qu’aucune machine virtuelle n’est associée au groupe.  

Pour supprimer une réservation de capacité, commencez par rechercher toutes les machines virtuelles qui y sont associées. La liste des machines virtuelles est disponible sous la propriété `virtualMachinesAssociated`. 

### <a name="api"></a>[API](#tab/api3)

Tout d’abord, recherchez toutes les machines virtuelles associées au groupe de réservations de capacité et dissociez-les.
 
```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01
``` 

```json
{ 
    "name": "<capacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}/capacityReservations/{capacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
            }, 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName2}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "{capacityReservationName}", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName1}" 
                            } 
                        ] 
                    }, 
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "<time>" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
}  
```
À partir de la réponse ci-dessus, recherchez les noms de toutes les machines virtuels sous la propriété `virtualMachinesAssociated` et supprimez-les du groupe de réservations de capacité en suivant les étapes de la section [supprimer une association de machines virtuelles à une réservation de capacité](capacity-reservation-remove-vm.md). 

Une fois que toutes les machines virtuelles sont supprimées du groupe de réservations de capacité, supprimez la ou les réservations de capacité du membre :

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```

Enfin, supprimez le groupe de réservations de capacité parent.

```rest
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?api-version=2021-04-01
```


### <a name="portal"></a>[Portail](#tab/portal3)

1. Ouvrez le [portail Azure](https://portal.azure.com)
1. Allez à votre groupe de réservations de capacité
1. Sélectionnez **Ressources** 
1. Recherchez toutes les machines virtuelles associées au groupe
1. [Dissociez chaque machine virtuelle](capacity-reservation-remove-vm.md)
1. Supprimez chaque réservation de capacité dans le groupe
    1. Accédez à **Réservations**
    1. Sélectionnez chaque réservation 
    1. Sélectionnez **Supprimer**.
1. Supprimez le groupe de réservations de capacité
    1. Allez au groupe de réservations de capacité
    1. En haut de la page, sélectionnez **Supprimer**

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli3)

Recherchez toutes les machines virtuelles associées au groupe de réservations de capacité et dissociez-les.

1. Exécutez la commande suivante : 
    
    ```azurecli-interactive
    az capacity reservation group show
    -g myResourceGroup
    -n myCapacityReservationGroup
    ```

1. À partir de la réponse ci-dessus, recherchez les noms de toutes les machines virtuels sous la propriété `VirtualMachinesAssociated` et supprimez-les du groupe de réservations de capacité en suivant les étapes détaillées dans [Supprimer une association de machines virtuelles à partir d’un groupe de réservation de capacité](capacity-reservation-remove-vm.md).

1. Une fois que toutes les machines virtuelles sont supprimées du groupe, passez aux étapes suivantes. 

1. Supprimez la réservation de capacité :

    ```azurecli-interactive
    az capacity reservation delete 
    -g myResourceGroup 
    -c myCapacityReservationGroup 
    -n myCapacityReservation 
    ```

1. Supprimez le groupe de réservations de capacité :

    ```azurecli-interactive
    az capacity reservation group delete 
    -g myResourceGroup 
    -n myCapacityReservationGroup
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell3)

Recherchez toutes les machines virtuelles associées au groupe de réservations de capacité et dissociez-les.

1. Exécutez la commande suivante : 
    
    ```powershell-interactive
    Get-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

1. À partir de la réponse ci-dessus, recherchez les noms de toutes les machines virtuels sous la propriété `VirtualMachinesAssociated` et supprimez-les du groupe de réservations de capacité en suivant les étapes détaillées dans [Supprimer une association de machines virtuelles à partir d’un groupe de réservation de capacité](capacity-reservation-remove-vm.md).

1. Une fois que toutes les machines virtuelles sont supprimées du groupe, passez aux étapes suivantes. 

1. Supprimez la réservation de capacité :

    ```powershell-interactive
    Remove-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    ```

1. Supprimez le groupe de réservations de capacité :

    ```powershell-interactive
    Remove-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Name "myCapacityReservationGroup"
    ```

Pour plus d’informations, accédez aux commandes Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationgroup), [Remove-AzCapacityReservation](/powershell/module/az.compute/remove-azcapacityreservation), et [Remove-AzCapacityReservationGroup](/powershell/module/az.compute/remove-azcapacityreservationgroup).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="restore-instance-quantity"></a>Restaurer la quantité d’instance 

Une demande bien formée pour réduire la quantité réservée doit toujours réussir, quel que soit le nombre de machines virtuelles associées à la réservation. Toutefois, l’amélioration de la quantité réservée peut nécessiter plus de quota et pour qu’Azure puisse traiter la demande de capacité supplémentaire. Dans de rares cas où Azure ne peut pas traiter la requête pour augmenter la quantité réservée pour les réservations existantes, il est possible qu’une réservation passe dans un état *échec* et devienne indisponible jusqu’à ce que la quantité soit restaurée sur le montant d’origine.  

> [!NOTE]
> Si une réservation est dans un état *échec*, toutes les machines virtuelles associées à la réservation continuent de fonctionner normalement.  

Par exemple, `myCapacityReservation` a une quantité réservée de 5. Vous demandez 5 instances supplémentaires, ce qui rend la quantité totale réservée égale à 10. Toutefois, en raison d’une situation de capacité restreinte dans la région, Azure ne peut pas traiter les 5 quantités supplémentaires demandées. Dans ce cas, `myCapacityReservation` ne répondra pas à son état prévu de 10 quantités réservées et passera en état d'*échec*. 

Pour résoudre ce problème, effectuez les étapes suivantes pour localiser l’ancienne valeur réservée quantité :  

1. Accédez à [Analyse des modifications d’application](https://ms.portal.azure.com/#blade/Microsoft_Azure_ChangeAnalysis/ChangeAnalysisBaseBlade) dans le portail Azure 
1. Sélectionnez l'**Abonnement**, le **Groupe de ressources** et l'**Intervalle de temps** appropriés dans les filtres
    - Vous ne pouvez revenir à plus de 14 jours dans le passé du filtre **Intervalle de temps** 
1. Recherchez le nom de la réservation de capacité
1. Recherchez la modification de la propriété `sku.capacity` pour cette réservation 
    - L’ancienne quantité réservée sera la valeur sous la colonne **Ancienne valeur** 

Mettez à jour `myCapacityReservation` à l’ancienne quantité réservée. Une fois mis à jour, la réservation est immédiatement disponible pour une utilisation avec vos machines virtuelles. 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment supprimer des machines virtuelles d’une réservation de capacité](capacity-reservation-remove-vm.md)

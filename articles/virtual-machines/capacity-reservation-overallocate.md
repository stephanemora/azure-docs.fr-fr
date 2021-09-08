---
title: Surallocation d’une réservation de capacité dans Azure (préversion)
description: Découvrez le fonctionnement de la surallocation lorsqu’il s’agit de la réservation de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 0486263551246a794f90867621be0e87d42747c6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273376"
---
# <a name="overallocating-capacity-reservation-preview"></a>Surallocation d’une réservation de capacité (préversion)

Azure autorise l’association de machines virtuelles supplémentaires au-delà du nombre réservé d’une réservation de capacité pour faciliter les scénarios de burst et de scale-out, sans la surcharge liée à la gestion des limites de capacité réservée. La seule différence est que le nombre de machines virtuelles au-delà de la quantité réservée ne reçoit pas l’avantage du contrat SLA de disponibilité de capacité. Tant qu’Azure dispose d’une capacité disponible qui répond aux exigences des machines virtuelles, les allocations supplémentaires seront correctement réalisées. 

La vue d’instance d’un groupe de réservations de capacité fournit un instantané de l’utilisation pour chaque réservation de capacité membre. Vous pouvez utiliser la vue d’instance pour voir le fonctionnement de la surallocation. 

Cet article suppose que vous avez créé un groupe de réservations de capacité (`myCapacityReservationGroup` ), une réservation de capacité membre (`myCapacityReservation`) et une machine virtuelle (*myVM1*) associée au groupe. Accédez à [Créer une réservation de capacité](capacity-reservation-create.md) et [Associer une machine virtuelle à une réservation de capacité](capacity-reservation-associate-vm.md) pour plus de détails.

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="register-for-capacity-reservation"></a>S’inscrire à la réservation de capacité 

Avant de pouvoir utiliser la fonctionnalité Réservation de capacité, vous devez [inscrire votre abonnement pour la préversion](capacity-reservation-overview.md#register-for-capacity-reservation). L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser Azure CLI ou PowerShell pour finaliser l’inscription de la fonctionnalité.

## <a name="instance-view-for-capacity-reservation-group"></a>Vue d’instance pour le groupe de réservations de capacité 

La vue d’instance d’un groupe de réservations de capacité se présente comme suit : 

```rest
GET 
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/myCapacityReservationGroup?$expand=instanceview&api-version=2021-04-01
```

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
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

Supposons que nous créons une autre machine virtuelle nommée *myVM2* et que vous l’associiez au groupe de réservations de capacité ci-dessus. 

La vue d’instance pour le groupe de réservations de capacité se présente comme suit : 

```json
{ 
    "name": "myCapacityReservationGroup", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/myCapacityReservationGroup", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus", 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/MYCAPACITYRESERVATIONGROUP/capacityReservations/MYCAPACITYRESERVATION" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
            }, 
 { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "myCapacityReservation", 
"utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM1" 
                            }, 
{ 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/myVM2" 
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

Notez que la longueur de `virtualMachinesAllocated` (2) est supérieure à `capacity` (1). Cet état valide est appelé *suralloué*. 

> [!IMPORTANT]
> Azure n’arrête pas les allocations juste parce qu’une réservation de capacité est entièrement consommée. Les règles de mise à l’échelle automatique, le scale-out temporaire et les spécifications associées fonctionnent au-delà de la quantité de capacité réservée, à condition qu’Azure dispose de la capacité disponible.  


## <a name="states-and-considerations"></a>États et considérations  

Il existe trois états valides pour une réservation de capacité donnée : 

| État  | État  | Considérations  |
|---|---|---|
| Capacité de réserve disponible  | Longueur de `virtualMachinesAllocated` < `capacity`  | La capacité de réserve est-elle nécessaire ? Réduisez éventuellement la capacité pour réduire les coûts.  |
| Réservation consommée  | Longueur de `virtualMachinesAllocated` == `capacity`  | Les machines virtuelles supplémentaires ne recevront pas le contrat SLA de capacité, sauf si certaines machines virtuelles existantes sont désallouées. Essayez éventuellement d’augmenter la capacité pour que les machines virtuelles supplémentaires planifiées reçoivent un contrat SLA.  |
| Réservation surutilisée  | Longueur de `virtualMachinesAllocated` > `capacity`  | Les machines virtuelles supplémentaires ne recevront pas le contrat SLA de capacité. En outre, la quantité de machines virtuelles (longueur de `virtualMachinesAllocated` – `capacity`) ne recevra pas de contrat SLA de capacité si elle est désallouée. Augmentez éventuellement la capacité pour ajouter le contrat SLA de capacité à davantage de machines virtuelles existantes.  |


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment supprimer des machines virtuelles d’une réservation de capacité](capacity-reservation-remove-vm.md)
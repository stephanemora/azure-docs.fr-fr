---
title: Associer un groupe de machines virtuelles identiques à un groupe de réservations de capacité (version préliminaire)
description: Découvrez comment associer un groupe de machines virtuelles identiques nouveau ou existant à un groupe de réservations de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: e2563f7addb773b256a56dc67b2ec892b7231372
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532657"
---
# <a name="associate-a-virtual-machine-scale-set-to-a-capacity-reservation-group-preview"></a>Associer un groupe de machines virtuelles identiques à un groupe de réservations de capacité (version préliminaire)

Les groupes de machines virtuelles identiques ont deux modes : 

- **Mode d’orchestration uniforme :** dans ce mode, les groupes de machines virtuelles identiques utilisent un profil de machine virtuelle ou un modèle pour monter en puissance jusqu’à la capacité souhaitée. Même s’il est possible de gérer ou personnaliser des instances de machine virtuelle individuelles, le mode Uniform utilise des instances de machine virtuelle identiques. Ces instances sont exposées par le biais des API de machine virtuelle des groupes de machines virtuelles identiques et ne sont pas compatibles avec les commandes standard de l’API de machine virtuelle Azure IaaS. Étant donné que le groupe identique effectue toutes les opérations de machine virtuelle, les réservations sont associées directement au groupe de machines virtuelles identiques. Une fois le groupe identique associé à la réservation, toutes les allocations de machines virtuelles suivantes sont effectuées sur la réservation. 
- **Mode d’orchestration flexible :** dans ce mode, vous bénéficiez d’une plus grande flexibilité pour gérer les instances de machine virtuelle du groupe de machines virtuelles identiques, car elles peuvent utiliser les API de machine virtuelle Azure IaaS standard au lieu d’utiliser l’interface de groupe identique. Ce mode ne fonctionne pas avec la réservation de capacité pendant la version préliminaire publique.

Pour en savoir plus sur ces modes, consultez [Modes d’orchestration des groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md). Le reste de cet article explique comment associer un groupe de machines virtuelles identiques uniforme à un groupe de réservations de capacité. 

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations-of-scale-sets-in-uniform-orchestration"></a>Limitations des groupes identiques dans l’orchestration uniforme 

- Pour que les groupee de machines virtuelles identiques dans l’orchestration uniforme soient compatibles avec la réservation de capacité, la propriété `singlePlacementGroup` doit avoir la valeur *false*. 
- L’option de disponibilité **Diffusion statique fixe** pour les groupes identiques uniformes à plusieurs zones n’est pas prise en charge avec la réservation de capacité. Cette option requiert l’utilisation de 5 domaines d’erreur, tandis que les réservations prennent uniquement en charge jusqu’à 3 domaines d’erreur pour des tailles d’usage général. L’approche recommandée consiste à utiliser l’option de **diffusion maximale** qui répartit les machines virtuelles sur autant de domaines d’erreur que possible dans chaque zone. Si nécessaire, configurez une configuration de domaine d’erreur personnalisée inférieure ou égale à 3. 

Il existe d’autres restrictions lors de l’utilisation de la réservation de capacité. Pour obtenir la liste complète, reportez-vous à la [vue d’ensemble des réservations de capacité](capacity-reservation-overview.md).  

## <a name="associate-a-new-virtual-machine-scale-set-to-a-capacity-reservation-group"></a>Associer un nouveau groupe de machines virtuelles identiques à un groupe de réservations de capacité


### <a name="api"></a>[API](#tab/api1)  

Pour associer un nouveau groupe de machines virtuelles identiques uniforme à un groupe de réservations de capacité, construisez la requête PUT suivante auprès du fournisseur *Microsoft. Compute* :

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
```

Ajoutez la propriété `capacityReservationGroup` dans le `virtualMachineProfile` comme montré ci-dessous : 

```json
{ 
    "name": "<VMScaleSetName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}", 
    "type": "Microsoft.Compute/virtualMachineScaleSets", 
    "location": "eastus", 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "tier": "Standard", 
        "capacity": 3 
}, 
"properties": { 
    "virtualMachineProfile": { 
        "capacityReservation": { 
            "capacityReservationGroup":{ 
                "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroup/{CapacityReservationGroupName}" 
            } 
         }, 
        "osProfile": { 
            … 
        }, 
        "storageProfile": { 
            … 
        }, 
        "networkProfile": { 
            …,
            "extensionProfile": { 
                … 
            } 
        } 
    } 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1) 

Utilisez `New-AzVmss` pour créer un groupe de machines virtuelles identiques et ajouter la propriété `CapacityReservationGroupId` pour associer le groupe identique à un groupe de réservations de capacité existant. L’exemple ci-dessous crée un groupe identique uniforme pour une machine virtuelle Standard_Ds1_v2 à l’emplacement États-Unis de l’est et associe le groupe identique à un groupe de réservations de capacité.

```powershell-interactive
$vmssName = <"VMSSNAME">
$vmPassword = ConvertTo-SecureString <"PASSWORD"> -AsPlainText -Force
$vmCred = New-Object System.Management.Automation.PSCredential(<"USERNAME">, $vmPassword)
New-AzVmss
–Credential $vmCred
-VMScaleSetName $vmssName
-ResourceGroupName "myResourceGroup"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
-PlatformFaultDomainCount 2
```

Pour en savoir plus, allez voir la commande Azure PowerShell [New-AzVmss](/powershell/module/az.compute/new-azvmss).

### <a name="arm-template"></a>[Modèle ARM](#tab/arm1)

Un  [modèle ARM](../azure-resource-manager/templates/overview.md)  est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative. Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement. 

Les modèles ARM vous permettent de déployer des groupes de ressources associées. Dans un modèle unique, vous pouvez créer un groupe de réservations de capacité et des réservations de capacité. Vous pouvez déployer des modèles via le Portail Azure, Azure CLI ou Azure PowerShell, ou à partir de pipelines d’intégration continue/de livraison continue. 

Si votre environnement est conforme aux conditions préalables et que vous êtes familiarisé avec l’utilisation des modèles ARM, utilisez ce modèle [Créer des groupes de machines virtuelles identiques avec la réservation de capacité](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json). 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-virtual-machine-scale-set-to-capacity-reservation-group"></a>Associer un groupe de machines virtuelles identiques existant à un groupe de réservations de capacité 

Pour la version préliminaire publique, afin d’associer un groupe de machines virtuelles identiques uniforme existant au groupe de réservations de capacité, il est nécessaire de libérer d’abord le groupe identique, puis d’effectuer l’Association au moment de la réallocation. Cela garantit que toutes les machines virtuelles du groupe identique consomment la réservation de capacité au moment de la réallocation.

### <a name="important-notes-on-upgrade-policies"></a>Remarques importantes sur les stratégies de mise à niveau 

- **Mise à niveau automatique** : dans ce mode, les instances de machine virtuelle du groupe identique sont automatiquement associées au groupe de réservations de capacité sans aucune autre action de votre part. Lorsque les machines virtuelles du groupe identique sont réallouées, elles commencent à consommer la capacité réservée.
- **Mise à niveau propagée** : dans ce mode, les instances de machine virtuelle du groupe identique sont associées au groupe de réservations de capacité sans aucune autre action de votre part. Toutefois, elles sont mises à jour par lots avec une durée de pause facultative entre elles. Lorsque les machines virtuelles du groupe identique sont réallouées, elles commencent à consommer la capacité réservée.
- **Mise à niveau manuelle** : dans ce mode, rien ne se passe aux instances de machine virtuelle du groupe identique lorsque le groupe de machines virtuelles identiques est attaché à un groupe de réservations de capacité. Vous devez effectuer des mises à jour individuelles sur chaque machine virtuelle du groupe identique en [la mettant à niveau avec le dernier modèle de groupe identique](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="api"></a>[API](#tab/api2)

1. Libérez un groupe de machines virtuelles identiques. 

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/deallocate?api-version=2021-04-01
    ```

1. Ajoutez la propriété `capacityReservationGroup` au modèle de groupe identique. Construisez la requête PUT suivante pour le fournisseur *Microsoft.Compute* :

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}?api-version=2021-04-01
    ```

    Dans le corps de la requête, ajoutez la propriété `capacityReservationGroup` :

    ```json
    "location": "eastus",
    "properties": {
        "virtualMachineProfile": {
             "capacityReservation": {
                      "capacityReservationGroup": {
                            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
                      }
                }
        }
    }
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2) 

1. Libérez un groupe de machines virtuelles identiques. 

    ```powershell-interactive
    Stop-AzVmss
    -ResourceGroupName "myResourceGroup”
    -VMScaleSetName "myVmss”
    ```

1. Associez le groupe identique au groupe de réservations de capacité. 

    ```powershell-interactive
    $vmss =
    Get-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    
    Update-AzVmss
    -ResourceGroupName "myResourceGroup"
    -VMScaleSetName "myVmss"
    -VirtualMachineScaleSet $vmss
    -CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), [Get-AzVmss](/powershell/module/az.compute/get-azvmss), et [Update-AzVmss](/powershell/module/az.compute/update-azvmss).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="view-virtual-machine-scale-set-association-with-instance-view"></a>Affichez l’Association de groupe de machines virtuelles identiques avec la vue d’instance 

Une fois que le groupe de machines virtuelles identiques uniforme est associé au groupe de réservations de capacité, toutes les allocations de machines virtuelles suivantes sont effectuées sur la réservation de capacité. Azure recherche automatiquement la réservation de capacité correspondante dans le groupe et consomme un emplacement réservé. 

### <a name="api"></a>[API](#tab/api3) 

Le groupe de réservation de capacité *Vue d’instance* reflète les nouvelles machines virtuelles du groupe identique selon les propriétés `virtualMachinesAssociated` & `virtualMachinesAllocated` comme montré ci-dessous : 

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}?$expand=instanceview&api-version=2021-04-01 
```

```json
{ 
    "name": "<CapacityReservationGroupName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}", 
    "type": "Microsoft.Compute/capacityReservationGroups", 
    "location": "eastus" 
}, 
    "properties": { 
        "capacityReservations": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}" 
            } 
        ], 
        "virtualMachinesAssociated": [ 
            { 
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
            } 
        ], 
        "instanceView": { 
            "capacityReservations": [ 
                { 
                    "name": "<CapacityReservationName>", 
                    "utilizationInfo": { 
                        "virtualMachinesAllocated": [ 
                            { 
                                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{VMScaleSetName}/virtualMachines/{VirtualMachineId}" 
                            } 
                        ] 
                    },
                    "statuses": [ 
                        { 
                            "code": "ProvisioningState/succeeded", 
                            "level": "Info", 
                            "displayStatus": "Provisioning succeeded", 
                            "time": "2021-05-25T15:12:10.4165243+00:00" 
                        } 
                    ] 
                } 
            ] 
        } 
    } 
} 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell3) 

Affichez votre groupe de machines virtuelles identiques et votre association de groupe de réservation de capacité avec la vue d’instance à l’aide de PowerShell. 

```powershell-interactive
$CapRes=
Get-AzCapacityReservationGroup
-ResourceGroupName <"ResourceGroupName">
-Name <"CapacityReservationGroupName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
``` 

Pour plus d’informations, accédez à la commande Azure PowerShell [Get-AzCapacityReservationGroup](/powershell/module/az.compute/get-azcapacityreservationGroup).

### <a name="portal"></a>[Portail](#tab/portal3)

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Allez à votre groupe de réservation de capacité
1. Sélectionnez **Ressources** dans **Paramètres** sur la gauche
1. Dans le tableau, vous pourrez voir toutes les machines virtuelles de groupe identique associées au groupe de réservations de capacité
--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="region-and-availability-zones-considerations"></a>Considérations relatives aux régions et aux Zones de disponibilité 

Les groupes de machines virtuelles identiques peuvent être créés au niveau régional ou dans une ou plusieurs zones de disponibilité pour les protéger contre les défaillances au niveau du centre de données. En savoir plus sur les groupes de machines virtuelles identiques avec plusieurs zones, reportez-vous à [Groupes de machines virtuelles identiques qui utilisent des zones de disponibilité](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md).  

 
>[!IMPORTANT]
> L’emplacement (région et zones de disponibilité) du groupe de machines virtuelles identiques et du groupe de réservations de capacité doit correspondre pour que l’association aboutisse. Pour un groupe identique régional, la région doit correspondre entre le groupe identique et le groupe de réservations de capacité. Pour un groupe identique de zone, les régions et les zones doivent correspondre entre le groupe identique et le groupe de réservations de capacité. 


Lorsqu’un groupe identique est réparti sur plusieurs zones, il tente toujours de le déployer uniformément entre les zones de disponibilité incluses. En raison de ce même déploiement, un groupe de réservations de capacité doit toujours avoir la même quantité de machines virtuelles réservées dans chaque zone. En guise d’illustration de ce qui est important, prenons l’exemple ci-dessous.   

Dans cet exemple, une quantité différente est réservée à chaque zone. Supposons que le groupe de machines virtuelles identiques évolue jusqu’à 75 instances. Étant donné que le groupe identique tente toujours de déployer uniformément entre les zones, la distribution de la machine virtuelle doit se présenter comme suit : 

| Zone  | Quantité réservée  | Non. de machines virtuelles de groupe identique dans chaque zone  | Quantité non utilisée réservée  | Surutilisés   |
|---|---|---|---|---|
| 1  | 40  | 25  | 15  | 0  |
| 2  | 20  | 25  | 0  | 5  |
| 3  | 15  | 25  | 0  | 10  |

Dans ce cas, le groupe identique est soumis à des frais supplémentaires pour 15 instances inutilisées dans la Zone 1. La scale-out s’appuie également sur 5 machines virtuelles dans la Zone 2 et 10 machines virtuelles dans la Zone 3 qui ne sont pas protégées par la réservation de capacité. Si 25 instances de capacité étaient réservées à chaque zone, toutes les 75 machines virtuelles seraient protégées par la réservation de capacité et le déploiement n’entraînerait aucun coût supplémentaire pour les instances inutilisées.  

Étant donné que les réservations peuvent être surutilisées, le groupe identique peut continuer à être mis à l’échelle normalement au-delà des limites de la réservation. La seule différence est que les machines virtuelles allouées au-dessus de la quantité réservée ne sont pas couvertes par le contrat SLA de réservation de capacité. Pour en savoir plus, consultez [Surallocation d’une réservation de capacité](capacity-reservation-overallocate.md).


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment supprimer une association de groupe identique d’une réservation de capacité](capacity-reservation-remove-virtual-machine-scale-set.md)

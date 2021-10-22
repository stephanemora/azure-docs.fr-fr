---
title: Associer une machine virtuelle à un groupe de réservations de capacité (préversion)
description: Découvrez comment associer une machine virtuelle nouvelle ou existante à un groupe de réservations de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 8bfb5811cd8c4ffe2efa10a0bb8fcac8b449092e
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065037"
---
# <a name="associate-a-vm-to-a-capacity-reservation-group-preview"></a>Associer une machine virtuelle à un groupe de réservations de capacité (préversion) 

Cet article vous guide tout au long des étapes d’association d’une machine virtuelle nouvelle ou existante à un groupe de réservations de capacité. Pour en savoir plus sur les réservations de capacité, consultez l’[article de présentation](capacity-reservation-overview.md). 

> [!IMPORTANT]
> La réservation de capacité est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="associate-a-new-vm"></a>Associer une nouvelle machine virtuelle

Pour associer une nouvelle machine virtuelle au groupe de réservations de capacité, le groupe doit être explicitement référencé en tant que propriété de la machine virtuelle. Cette référence empêche la réservation correspondante dans le groupe d’être consommée accidentellement par des applications et charges de travail moins critiques qui ne sont pas destinées à l’utiliser.  

### <a name="api"></a>[API](#tab/api1)

Pour ajouter la propriété `capacityReservationGroup` à une machine virtuelle, envoyez la demande PUT suivante au fournisseur *Microsoft.Compute* :

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
```

Dans le corps de la demande, ajoutez la propriété `capacityReservationGroup` comme indiqué ci-dessous :

```json
{ 
  "location": "eastus", 
  "properties": { 
    "hardwareProfile": { 
      "vmSize": "Standard_D2s_v3" 
    }, 
    … 
   "CapacityReservation":{ 
    "capacityReservationGroup":{ 
        "id":"subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}" 
    } 
    "storageProfile": { 
    … 
    }, 
    "osProfile": { 
    … 
    }, 
    "networkProfile": { 
     …     
    } 
  } 
} 
```

### <a name="portal"></a>[Portail](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Dans la barre de recherche, tapez **machine virtuelle**.
1. Sous *Services*, sélectionnez **Machines virtuelles**.
1. Dans la page *Machines virtuelles*, sélectionnez **Créer**, puis **Machine virtuelle**.
1. Sous l’onglet *De base*, sous *Détails du projet*, sélectionnez l’**abonnement** approprié, puis choisissez de créer un **groupe de ressources** ou d’en utiliser un déjà disponible.
1. Sous *Détails de l’instance*, entrez le **nom** de la machine virtuelle et choisissez votre **région**.
1. Choisissez une **image** et la **taille de machine virtuelle**
1. Sous *Compte Administrateur*, spécifiez un **nom d’utilisateur** et un **mot de passe**.
    1. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux exigences de complexité définies.
1. Sous *Règles des ports d’entrée*, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** et **HTTP (80)** dans la liste déroulante.
1. Accédez à la *section Avancé*.
1. Dans la liste déroulante **Réservations de capacité**, sélectionnez le groupe de réservations de capacité auquel vous souhaitez associer la machine virtuelle.
1. Sélectionnez le bouton **Vérifier + Créer**. 
1. Une fois la validation exécutée, sélectionnez le bouton **Créer**. 
1. Une fois le déploiement effectué, sélectionnez **Accéder à la ressource**.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli1)

Utilisez `az vm create` pour créer une machine virtuelle et ajouter la propriété `capacity-reservation-group` pour associer la machine virtuelle à un groupe de réservations de capacité existant. L’exemple ci-dessous crée une machine virtuelle Standard_D2s_v3 dans l’emplacement USA Est et associe la machine virtuelle à un groupe de réservations de capacité.

```azurecli-interactive
az vm create 
--resource-group myResourceGroup 
--name myVM 
--location eastus 
--size Standard_D2s_v3 
--image UbuntuLTS 
--capacity-reservation-group /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}
```

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

Utilisez `New-AzVM` pour créer une machine virtuelle et ajouter la propriété `CapacityReservationGroupId` pour associer la machine virtuelle à un groupe de réservations de capacité existant. L’exemple ci-dessous crée une machine virtuelle Standard_D2s_v3 dans l’emplacement USA Est et associe la machine virtuelle à un groupe de réservations de capacité.

```powershell-interactive
New-AzVm
-ResourceGroupName "myResourceGroup"
-Name "myVM"
-Location "eastus"
-VirtualNetworkName "myVnet"
-SubnetName "mySubnet"
-SecurityGroupName "myNetworkSecurityGroup"
-PublicIpAddressName "myPublicIpAddress"
-OpenPorts 80,3389
-Size "Standard_D2s_v3"
-CapacityReservationGroupId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
```

Pour en savoir plus, consultez la commande Azure PowerShell [New-AzVM](/powershell/module/az.compute/new-azvm).

### <a name="arm-template"></a>[Modèle ARM](#tab/arm1)

Un  [modèle ARM](../azure-resource-manager/templates/overview.md)  est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative. Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement. 

Les modèles ARM vous permettent de déployer des groupes de ressources associées. Dans un modèle unique, vous pouvez créer un groupe de réservations de capacité et des réservations de capacité. Vous pouvez déployer des modèles via le Portail Azure, Azure CLI ou Azure PowerShell, ou à partir de pipelines d’intégration continue/de livraison continue. 

Si votre environnement est conforme aux conditions préalables et que vous êtes familiarisé avec l’utilisation des modèles ARM, utilisez ce modèle [Créer une machine virtuelle avec la réservation de capacité](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json). 


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="associate-an-existing-vm"></a>Associer une machine virtuelle existante 

Alors que la réservation de capacité est en préversion, pour associer une machine virtuelle existante à un groupe de réservations de capacité, il est nécessaire de libérer d’abord la machine virtuelle, puis d’effectuer l’association au moment de la réallocation. Ce processus garantit que la machine virtuelle consomme l’un des emplacements vides dans la réservation. 

### <a name="api"></a>[API](#tab/api2)

1. Libérez la machine virtuelle. 

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourcegroupname}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}/deallocate?api-version=2021-04-01
    ```

1. Ajoutez la propriété `capacityReservationGroup` à la machine virtuelle. Envoyez la demande PUT suivante au fournisseur *Microsoft.Compute* :

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{VirtualMachineName}?api-version=2021-04-01
    ```

    Dans le corps de la demande, ajoutez la propriété `capacityReservationGroup` : 
    
    ```json
    {
    "location": "eastus",
    "properties": {
        "capacityReservation": {
            "capacityReservationGroup": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{capacityReservationGroupName}"
            }
        }
    }
    }
    ```


### <a name="portal"></a>[Portail](#tab/portal2)

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accéder à votre machine virtuelle.
1. Sélectionnez **Vue d’ensemble** à gauche.
1. Sélectionnez **Arrêter** en haut de la page pour libérer la machine virtuelle. 
1. Accédez à **Configurations** à gauche.
1. Dans la liste déroulante **Groupe de réservations de capacité**, sélectionnez le groupe auquel vous souhaitez associer la machine virtuelle. 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli2)

1. Libérer la machine virtuelle

    ```azurecli-interactive
    az vm deallocate 
    -g myResourceGroup 
    -n myVM
    ```

1. Associer la machine virtuelle à un groupe de réservations de capacité

    ```azurecli-interactive
    az vm update 
    -g myresourcegroup 
    -n myVM 
    --capacity-reservation-group subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}
    ```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

1. Libérer la machine virtuelle

    ```powershell-interactive
    Stop-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    ```

1. Associer la machine virtuelle à un groupe de réservations de capacité

    ```powershell-interactive
    $VirtualMachine =
    Get-AzVM
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    
    Update-AzVM
    -ResourceGroupName "myResourceGroup"
    -VM $VirtualMachine
    -CapacityReservationGroupId "subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}"
    ```

Pour en savoir plus, allez voir les commandes Azure PowerShell [Stop-AzVM](/powershell/module/az.compute/stop-azvm), [Get-AzVM](/powershell/module/az.compute/get-azvm) et [Update-AzVM](/powershell/module/az.compute/update-azvm).

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="view-vm-association-with-instance-view"></a>Afficher l’association de machines virtuelles avec la vue d’instance 

Une fois que la propriété `capacityReservationGroup` est définie, une association existe entre la machine virtuelle et le groupe. Azure recherche automatiquement la réservation de capacité correspondante dans le groupe et consomme un emplacement réservé. La *Vue d’instance* du groupe de réservations de capacité reflète la nouvelle machine virtuelle dans la propriété `virtualMachinesAllocated`, comme montré ci-dessous : 

### <a name="api"></a>[API](#tab/api3)

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{capacityReservationGroupName}?$expand=instanceView&api-version=2021-04-01 
```

```json
{
   "name":"{CapacityReservationGroupName}",
   "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}",
   "type":"Microsoft.Compute/capacityReservationGroups",
   "location":"eastus",
   "properties":{
      "capacityReservations":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/ {CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}"
         }
      ],
      "virtualMachinesAssociated":[
         {
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
         }
      ],
      "instanceView":{
         "capacityReservations":[
            {
               "name":"{CapacityReservationName}",
               "utilizationInfo":{
                  "virtualMachinesAllocated":[
                     {
                        "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{myVM}"
                     }
                  ]
               },
               "statuses":[
                  {
                     "code":"ProvisioningState/succeeded",
                     "level":"Info",
                     "displayStatus":"Provisioning succeeded",
                     "time":"2021-05-25T15:12:10.4165243+00:00"
                  }
               ]
            }
         ]
      }
   }
} 
``` 

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli3)

```azurecli-interactive
az capacity reservation show 
-g myResourceGroup
-c myCapacityReservationGroup 
-n myCapacityReservation 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell3)

```powershell-interactive
$CapRes=
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName] <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
-InstanceView

$CapRes.InstanceView.Utilizationinfo.VirtualMachinesAllocated
```

Pour plus d’informations, accédez à la commande Azure PowerShell [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).


### <a name="portal"></a>[Portail](#tab/portal3)

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Accédez à votre groupe de réservations de capacité.
1. Sélectionnez **Ressources** dans **Paramètres** à gauche.
1. Examinez le tableau pour voir toutes les machines virtuelles associées au groupe de réservations de capacité.  

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Supprimer une association de machines virtuelles à un groupe de réservations de capacité](capacity-reservation-remove-vm.md)

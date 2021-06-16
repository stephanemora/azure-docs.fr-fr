---
title: Supprimer des ressources d’une collection de déplacements dans Azure Resource Mover
description: Découvrez comment supprimer des ressources d’une collection de déplacements dans Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71397e4635e447701863122e58576dfb6507bcaf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698380"
---
# <a name="manage-move-collections-and-resource-groups"></a>Gérer les collections de déplacement et les groupes de ressources

Cet article explique comment supprimer des ressources d’une collection de déplacement ou supprimer une collection de déplacement/un groupe de ressources dans [Azure Resource Mover](overview.md). Les collections de déplacement sont utilisées lors du déplacement de ressources Azure entre régions Azure.

## <a name="remove-a-resource-portal"></a>Suppression d’une ressource (portail)

Vous pouvez supprimer des ressources dans une collection de déplacement, dans le portail du déplaceur de ressources, comme suit :

1. Dans **Entre régions**, sélectionnez toutes les ressources que vous souhaitez supprimer de la collection, puis sélectionnez **Supprimer**. 

    ![Bouton à sélectionner pour supprimer](./media/remove-move-resources/portal-select-resources.png)

2. Dans **Supprimer des ressources**, cliquez sur **Supprimer**.

    ![Bouton permettant de supprimer des ressources d’une collection de déplacements](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Supprimer une collection de déplacement/un groupe de ressources (portail)

Vous pouvez supprimer une collection de déplacement/un groupe de ressources dans le portail.

1. Suivez les instructions de la procédure ci-dessus pour supprimer des ressources de la collection. Si vous supprimez un groupe de ressources, assurez-vous qu’il ne contient aucune ressource.
2. Supprimez la collection de déplacement ou le groupe de ressources.  

## <a name="remove-a-resource-powershell"></a>Supprimer une ressource (PowerShell)

À l’aide des applets de commande PowerShell, vous pouvez supprimer une ressource unique d’un MoveCollection, ou supprimer plusieurs ressources.

### <a name="remove-a-single-resource"></a>Sélectionner une ressource unique

Supprimez une ressource (dans notre exemple le réseau virtuel *psdemorm-vnet*) comme suit :

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**Sortie après l’exécution de l’applet de commande**

![Texte de sortie après la suppression d’une ressource d’une collection de déplacement](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>Supprimer plusieurs ressources

Supprimez plusieurs ressources comme suit :

1. Validez les dépendances :

    ````azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. Retrieve the dependent resources that need to be removed (along with our example virtual network psdemorm-vnet):

    ````azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. Remove all resources, along with the virtual network:

    
    ````azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **Output after running cmdlet**

    ![Output text after removing all resources from a move collection](./media/remove-move-resources/remove-multiple-all.png)


## Remove a collection (PowerShell)

Remove an entire move collection from the subscription, as follows:

1. Follow the instructions above to remove resources in the collection using PowerShell.
2. Run:

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **Output after running cmdlet**
    
    ![Output text after removing a move collection](./media/remove-move-resources/remove-collection.png)

## VM resource state after removing

What happens when you remove a VM resource from a move collection depends on the resource state, as summarized in the table.

###  Remove VM state
**Resource state** | **VM** | **Networking**
--- | --- | --- 
**Added to move collection** | Delete from move collection. | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection  | Delete from move collection. 
**Prepare in progress**<br/> (or any other state in progress) | Delete operation fails with error.  | Delete operation fails with error.
**Prepare failed** | Delete from the move collection.<br/>Delete anything created in the target region, including replica disks. <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from the move collection.  
**Initiate move pending** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Initiate move failed** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Commit failed** | We recommend that you discard the  so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Discard completed** | The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target - VM, replica disks, vault etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. <br/><br/> Infrastructure resources created during the move need to be deleted manually. |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection.
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there.
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.  | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region. |  Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## SQL resource state after removing

What happens when you remove an Azure SQL resource from a move collection depends on the resource state, as summarized in the table.

**Resource state** | **SQL** 
--- | --- 
**Added to move collection** | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection 
**Prepare in progress**<br/> (or any other state in progress)  | Delete operation fails with error. 
**Prepare failed** | Delete from move collection<br/><br/>Delete anything created in the target region. 
**Initiate move pending** |  Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and will be deleted. 
**Initiate move failed** | Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and must be deleted. 
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Commit failed** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Discard completed** |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target, including SQL databases. 
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## Next steps

Try [moving a VM](tutorial-move-region-virtual-machines.md) to another region with Resource Mover.

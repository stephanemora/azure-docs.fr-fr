---
title: Supprimer des ressources d’une collection de déplacements dans Azure Resource Mover
description: Découvrez comment supprimer des ressources d’une collection de déplacements dans Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652612"
---
# <a name="remove-resources-from-a-move-collection"></a>Supprimer des ressources d’une collection de déplacements

Cet article explique comment supprimer des ressources d’une collection de déplacements dans [Azure Resource Mover](overview.md). Les collections de déplacement sont utilisées lors du déplacement de ressources Azure entre régions Azure.

## <a name="remove-a-resource-portal"></a>Suppression d’une ressource (portail)

Supprimez dans le portail Resource Move en procédant comme suit :

1. Dans **Entre régions**, sélectionnez les ressources que vous souhaitez supprimer de la collection > **Supprimer**.

    ![Bouton à sélectionner pour supprimer](./media/remove-move-resources/portal-select-resources.png)

1. Dans **Supprimer des ressources**, cliquez sur **Supprimer**.

    ![Bouton permettant de supprimer des ressources d’une collection de déplacements](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>Supprimer une ressource (PowerShell)

Pour supprimer une ressource (dans notre exemple, les machines PSDemoVM) d’une collection à l’aide de PowerShell, procédez comme suit :

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Sortie attendue**
![Texte de sortie après la suppression d’une ressource d’une collection de déplacement](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Supprimer une collection (PowerShell)

Supprimez une collection de déplacements entière à l’aide de PowerShell, comme suit :

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**Sortie attendue**
![Texte de sortie après la suppression d’une collection de déplacement](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>État des ressources de la machine virtuelle après suppression

Ce qui se passe lorsque vous supprimez une ressource de machine virtuelle d’une collection de déplacements dépend de l’état de la ressource, comme le tableau le résume.

###  <a name="remove-vm-state"></a>Supprimer l’état de la machine virtuelle
**État de la ressource** | **Machine virtuelle** | **Mise en réseau**
--- | --- | --- 
**Ajouté à la collection de déplacement** | Supprimer de la collection de déplacement. | Supprimer de la collection de déplacement. 
**Dépendances résolues/préparation en attente** | Supprimer de la collection de déplacement  | Supprimer de la collection de déplacement. 
**Préparation en cours**<br/> (ou tout autre état en cours) | L’opération de suppression échoue avec une erreur.  | L’opération de suppression échoue avec une erreur.
**Échec de la préparation** | Supprimez de la collection de déplacement.<br/>Supprimez tout ce qui a été créé dans la région cible, y compris les disques de réplication. <br/><br/> Les ressources d’infrastructure créées pendant le déplacement doivent être supprimées manuellement. | Supprimez de la collection de déplacement.  
**Lancement du déplacement en attente** | Supprimer de la collection de déplacement.<br/><br/> Supprimez tout ce qui a été créé dans la région cible, y compris les machines virtuelles, les disques de réplication, etc.  <br/><br/> Les ressources d’infrastructure créées pendant le déplacement doivent être supprimées manuellement. | Supprimer de la collection de déplacement.
**Impossible de lancer le déplacement** | Supprimer de la collection de déplacement.<br/><br/> Supprimez tout ce qui a été créé dans la région cible, y compris les machines virtuelles, les disques de réplication, etc.  <br/><br/> Les ressources d’infrastructure créées pendant le déplacement doivent être supprimées manuellement. | Supprimer de la collection de déplacement.
**Validation en attente** | Nous vous recommandons d’ignorer le déplacement pour que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. | Nous vous recommandons d’ignorer le déplacement pour que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. 
**Échec de la validation** | Nous vous recommandons d’ignorer le déplacement afin que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. | Nous vous recommandons d’ignorer le déplacement pour que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là.
**Abandon terminé** | La ressource revient à l’état **Lancement du déplacement en attente**.<br/><br/> Elle est supprimée de la collection de déplacement, ainsi que tout ce qui a été créé sur la cible : les disques de réplication, le coffre, etc.  <br/><br/> Les ressources d’infrastructure créées pendant le déplacement doivent être supprimées manuellement. <br/><br/> Les ressources d’infrastructure créées pendant le déplacement doivent être supprimées manuellement. |  La ressource revient à l’état **Lancement du déplacement en attente**.<br/><br/> Elle est supprimée de la collection de déplacements.
**Échec de l’abandon** | Nous vous recommandons d’ignorer les déplacements afin que les ressources cibles soient supprimées en premier.<br/><br/> Après cela, la ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. | Nous vous recommandons d’ignorer les déplacements afin que les ressources cibles soient supprimées en premier.<br/><br/> Après cela, la ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là.
**Suppression de la source en attente** | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible.  | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible.
**Échec de la suppression de la source** | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible. | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible.

## <a name="sql-resource-state-after-removing"></a>État de la ressource SQL après suppression

Ce qui se passe lorsque vous supprimez une ressource Azure SQL d’une collection de déplacements dépend de l’état de la ressource, comme le tableau le résume.

**État de la ressource** | **SQL** 
--- | --- 
**Ajouté à la collection de déplacement** | Supprimer de la collection de déplacement. 
**Dépendances résolues/préparation en attente** | Supprimer de la collection de déplacement 
**Préparation en cours**<br/> (ou tout autre état en cours)  | L’opération de suppression échoue avec une erreur. 
**Échec de la préparation** | Supprimer de la collection de déplacement<br/><br/>Ne supprimez rien de ce qui a été créé dans la région cible. 
**Lancement du déplacement en attente** |  Supprimer de la collection de déplacement<br/><br/>Ne supprimez rien de ce qui a été créé dans la région cible. La base de données SQL existe à ce stade et sera supprimée. 
**Impossible de lancer le déplacement** | Supprimer de la collection de déplacement<br/><br/>Ne supprimez rien de ce qui a été créé dans la région cible. La base de données SQL existe à ce stade et doit être supprimée. 
**Validation en attente** | Nous vous recommandons d’ignorer le déplacement pour que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là.
**Échec de la validation** | Nous vous recommandons d’ignorer le déplacement pour que les ressources cibles soient supprimées en premier.<br/><br/> La ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. 
**Abandon terminé** |  La ressource revient à l’état **Lancement du déplacement en attente**.<br/><br/> Elle est supprimée de la collection de déplacement, ainsi que tout ce qui a été créé sur la cible, y compris les bases de données SQL. 
**Échec de l’abandon** | Nous vous recommandons d’ignorer les déplacements afin que les ressources cibles soient supprimées en premier.<br/><br/> Après cela, la ressource revient à l’état **Lancement du déplacement en attente** et vous pouvez continuer de là. 
**Suppression de la source en attente** | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible. 
**Échec de la suppression de la source** | Supprimé de la collection de déplacements.<br/><br/> Ne supprime rien de ce qui a été créé dans la région cible. 

## <a name="next-steps"></a>Étapes suivantes

Essayez de [déplacer une machine virtuelle](tutorial-move-region-virtual-machines.md) vers une autre région avec Resource Mover.
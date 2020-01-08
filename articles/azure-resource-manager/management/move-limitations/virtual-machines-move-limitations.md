---
title: Déplacer des machines virtuelles Azure vers un nouveau groupe d’abonnements ou de ressources
description: Utilisez Azure Resource Manager pour déplacer des machines virtuelles vers un nouveau groupe de ressources ou abonnement.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474915"
---
# <a name="move-guidance-for-virtual-machines"></a>Conseils pour le déplacement de machines virtuelles

Cet article décrit les scénarios actuellement non pris en charge et les étapes pour déplacer des machines virtuelles avec sauvegarde.

## <a name="scenarios-not-supported"></a>Scénarios non pris en charge

Les scénarios suivants ne sont pas encore pris en charge :

* Les disques managés dans les zones de disponibilité ne peuvent pas être déplacés vers un autre abonnement.
* Les groupes de machines virtuelles identiques avec un équilibreur de charge de référence SKU Standard ou avec une adresse IP publique de référence SKU Standard ne peuvent pas être déplacés.
* Les machines virtuelles auxquelles des plans sont associés créées à partir de ressources de la Place de marché ne peuvent pas être déplacées entre des groupes de ressources ou des abonnements. Annulez l'approvisionnement de la machine virtuelle dans l’abonnement actuel, puis redéployez-la dans le nouvel abonnement.
* Les machines virtuelles d'un réseau virtuel existant ne peuvent pas être déplacées vers un nouvel abonnement si vous ne déplacez pas toutes les ressources dans le réseau virtuel.
* Les machines virtuelles et les groupes de machines virtuelles identiques basse priorité ne peuvent pas être déplacés entre des groupes de ressources ou abonnements.
* Les machines virtuelles d’un groupe à haute disponibilité ne peuvent pas être déplacées individuellement.

## <a name="virtual-machines-with-azure-backup"></a>Machines virtuelles avec Sauvegarde Azure

Pour déplacer des machines virtuelles configurées avec Sauvegarde Azure, utilisez la solution de contournement suivante :

* Recherchez l’emplacement de votre machine virtuelle.
* Recherchez un groupe de ressources dont le modèle de nommage est le suivant : `AzureBackupRG_<location of your VM>_1`, par exemple, AzureBackupRG_westus2_1
* Si vous utilisez le portail Azure, cochez « Afficher les types masqués »
* Si vous utilisez PowerShell, utilisez l’applet de commande `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Si vous utilisez l’interface CLI, utilisez `az resource list -g AzureBackupRG_<location of your VM>_1`
* Recherchez la ressource du type `Microsoft.Compute/restorePointCollections` avec le modèle de nommage `AzureBackup_<name of your VM that you're trying to move>_###########`
* Supprimez cette ressource. Cette opération supprime uniquement les points de récupération instantanée, et non les données sauvegardées dans le coffre.
* Au terme de cette suppression, vous pouvez déplacer la machine virtuelle et le coffre vers l’abonnement cible. Après le déplacement, vous pouvez poursuivre les sauvegardes sans perte de données.
* Pour plus d’informations sur le déplacement des coffres Recovery Services pour la sauvegarde, consultez [Limitations de Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les commandes permettant de déplacer des ressources, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../move-resource-group-and-subscription.md).

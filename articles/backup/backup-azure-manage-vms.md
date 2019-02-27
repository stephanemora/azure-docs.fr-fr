---
title: Gérer et surveiller les sauvegardes de machine virtuelle Azure avec le service Sauvegarde Azure
description: Découvrez comment gérer et surveiller les sauvegardes de machine virtuelle Azure avec le service Sauvegarde Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430061"
---
# <a name="manage-azure-vm-backups"></a>Gérer les sauvegardes de machines virtuelles Azure

Cet article explique comment gérer des machines virtuelles Azure sauvegardées avec les sauvegardes du [service Sauvegarde Azure](backup-overview.md) et résume les informations d’alertes de sauvegarde disponibles dans le tableau de bord du portail.


Dans le portail Azure, le tableau de bord Coffre Recovery Services permet d’accéder à des informations concernant le coffre, notamment :

* La sauvegarde la plus récente, c’est-à-dire le dernier point de restauration.
* La stratégie de sauvegarde
* La taille totale de tous les instantanés de sauvegarde
* Le nombre de machines virtuelles activées pour la sauvegarde

Vous pouvez gérer les sauvegardes à l’aide du tableau de bord et en accédant aux machines virtuelles individuelles. L’ouverture du coffre dans le tableau de bord permet d’initier une sauvegarde de machine virtuelle. 

![Vue complète avec curseur](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Afficher les machines virtuelles dans le tableau de bord

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Coffre Recovery Services**. 
    ![Créer un coffre Recovery Services - Étape 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Pour la simplicité d’utilisation, cliquez avec le bouton droit sur le coffre dans la liste des coffres > **Épingler au tableau de bord**.
3. Ouvrez le tableau de bord du coffre. 
    ![Ouvrir le tableau de bord du coffre et le panneau Paramètres](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. Sur la vignette **Éléments de sauvegarde**, cliquez sur **Machines virtuelles Azure**.

    ![Ouvrir la mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. Dans le panneau **Éléments de sauvegarde**, vous pouvez voir la dernière tâche de sauvegarde pour chaque élément. Dans cet exemple, une machine virtuelle nommée demovm-markgal est protégée par ce coffre.  

    ![Mosaïque Éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. À partir du tableau de bord de l’élément de coffre, vous pouvez créer ou modifier des stratégies de sauvegarde, afficher les points de restauration, exécuter une sauvegarde à la demande, arrêter et redémarrer la protection des machines virtuelles, supprimer des points de récupération et exécuter une restauration.

    ![Tableau de bord Éléments de sauvegarde avec panneau Paramètres](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Gestion des stratégies de sauvegarde
1. À partir du [tableau de bord de l’élément de coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Tous les paramètres**.

    ![Panneau Stratégie de sauvegarde](./media/backup-azure-manage-vms/all-settings-button.png)
2. Dans **Paramètres**, cliquez sur **Stratégie de sauvegarde**.
3. Dans le menu **Choisir une stratégie de sauvegarde** :

   * Pour modifier les stratégies, sélectionnez une autre stratégie, puis cliquez sur **Enregistrer**. La nouvelle stratégie est appliquée immédiatement au coffre.
   * Pour créer une stratégie, sélectionnez **Créer**. [En savoir plus](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Sauvegarde de machine virtuelle](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande
Vous pouvez effectuer une sauvegarde à la demande d’une machine virtuelle une fois que celle-ci est configurée pour la protection.
- Si la sauvegarde initiale est en attente, la sauvegarde à la demande crée une copie complète de la machine virtuelle dans le coffre Recovery Services.
- Si la sauvegarde initiale est terminée, une sauvegarde à la demande enverra au coffre Recovery Services uniquement les modifications par rapport à l’instantané précédent. Autrement dit, les sauvegardes suivantes sont toujours incrémentielles.
- La plage de rétention pour une sauvegarde à la demande correspond à la valeur de rétention spécifiée pour le point de sauvegarde quotidien de la stratégie. Si aucun point de sauvegarde quotidien n’est sélectionné, le point de sauvegarde hebdomadaire est utilisé.


Pour déclencher une sauvegarde à la demande :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Sauvegarder maintenant**.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Cliquez sur **Oui** pour démarrer le travail de sauvegarde.

    ![Bouton Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-check.png)

 
 Le travail de sauvegarde crée un point de récupération. La plage de rétention du point de récupération est identique à celle spécifiée dans la stratégie associée à la machine virtuelle. Pour suivre la progression du travail, dans le tableau de bord du coffre, cliquez sur la mosaïque **Travaux de sauvegarde** .  

## <a name="stop-protecting-a-vm"></a>Arrêter la protection d’une machine virtuelle

Il existe deux façons d’arrêter la protection des machines virtuelles :

- Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération. Dans ce cas, vous ne pourrez pas restaurer la machine virtuelle.
- Arrêtez tous les travaux de sauvegarde à venir en conservant les points de récupération. La conservation des points de récupération dans le stockage présente un coût. Mais elle a l’avantage de vous permettre de restaurer ultérieurement la machine virtuelle, si vous le souhaitez. [En savoir plus](https://azure.microsoft.com/pricing/details/backup/) sur la tarification.

Pour arrêter la protection d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Arrêter la sauvegarde**.

    ![Bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/stop-backup-button.png
2. Indiquez si vous souhaitez conserver ou supprimer les données de sauvegarde et confirmez votre choix si nécessaire. Confirmez si nécessaire et entrez éventuellement un commentaire. Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour en afficher le nom.

    ![Arrêter la protection](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Un message de notification vous informe que les travaux de sauvegarde ont été interrompus.


## <a name="resume-protection-of-a-vm"></a>Reprendre la protection d’une machine virtuelle

Si vous avez conservé les données de sauvegarde lorsque la machine virtuelle a été arrêtée, vous pouvez réactiver la protection. Si vous avez supprimé des données de sauvegarde, vous ne pouvez pas réactiver la protection.

Te

1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Reprendre la sauvegarde**.

2. Suivez les étapes de la section [Modifier les stratégies de sauvegarde](backup-azure-manage-vms.md#manage-backup-policies) pour affecter la stratégie de la machine virtuelle. Vous pouvez choisir une autre stratégie que la stratégie avec laquelle la machine virtuelle a été initialement protégée.
3. Une fois la stratégie de sauvegarde appliquée à la machine virtuelle, le message suivant s’affiche.

    ![Machine virtuelle protégée](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Suppression des données de sauvegarde

Vous pouvez supprimer les données de sauvegarde associées à une machine virtuelle au cours du travail **Arrêter la sauvegarde** , ou à tout moment après la fin du travail de sauvegarde.

- Il peut même être préférable d’attendre plusieurs jours ou semaines avant de supprimer les points de récupération.
- Contrairement à la restauration des points de récupération, lors de la suppression des données de sauvegarde, vous ne pouvez pas choisir des points de récupération spécifiques à supprimer. Si vous choisissez de supprimer vos données de sauvegarde, vous supprimerez tous les points de récupération associés à l’élément.

Cette procédure suppose que le travail de sauvegarde de la machine virtuelle a été arrêté ou désactivé.


1. Sur le [tableau de bord de l’élément du coffre](backup-azure-manage-vms.md#open-a-vault-item-dashboard), cliquez sur **Supprimer la sauvegarde**.

    ![Type de machine virtuelle](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Saisissez le nom de l’élément pour confirmer la suppression des points de récupération.

    ![Arrêter la vérification](./media/backup-azure-manage-vms/item-verification-box.png)

4. Pour supprimer les données de sauvegarde de l’élément actif, cliquez sur le ![bouton Arrêter la sauvegarde](./media/backup-azure-manage-vms/delete-button.png).

    Un message de notification vous informe que les données de sauvegarde ont été supprimées.

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](backup-azure-vms-first-look-arm.md) sur la sauvegarde de machines virtuelles Azure à partir des paramètres de la machine virtuelle.
- [En savoir plus](backup-azure-arm-restore-vms.md) sur la restauration des machines virtuelles. 
- [En savoir plus](backup-azure-monitor-vms.md) sur la surveillance des sauvegardes de machines virtuelles Azure.
 

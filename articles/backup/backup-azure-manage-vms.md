---
title: Gérer et surveiller les sauvegardes de machines virtuelles Azure à l’aide du service sauvegarde Azure
description: Découvrez comment gérer et surveiller les sauvegardes de machines virtuelles Azure à l’aide du service Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295774"
---
# <a name="manage-azure-vm-backups"></a>Gérer les sauvegardes de machines virtuelles Azure

Cet article décrit comment gérer des machines virtuelles (VM) qui sont sauvegardés à l’aide de la [service Azure Backup](backup-overview.md). Cet article récapitule également les informations de sauvegarde que vous pouvez trouver sur le tableau de bord du coffre.


Dans le portail Azure, le tableau de bord du coffre Recovery Services fournit l’accès pour le coffre d’informations, y compris :

* La sauvegarde la plus récente, c’est-à-dire le dernier point de restauration.
* La stratégie de sauvegarde.
* La taille totale de tous les instantanés de sauvegarde.
* Le nombre de machines virtuelles qui sont activées pour les sauvegardes.

Vous pouvez gérer les sauvegardes à l’aide du tableau de bord et en accédant à des machines virtuelles individuelles. Pour commencer les sauvegardes de machines, ouvrez le coffre sur le tableau de bord.

![Affichage de tableau de bord complète avec curseur](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Afficher les machines virtuelles sur le tableau de bord

Pour afficher les machines virtuelles sur le tableau de bord du coffre :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, sélectionnez **Parcourir**. Dans la liste des ressources, tapez **Recovery Services**. Lorsque vous tapez, la liste est filtrée en fonction de votre entrée. Sélectionnez **Coffres Recovery Services**.

    ![Créer un coffre Recovery Services](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Simplicité d’utilisation, cliquez sur le coffre et sélectionnez **épingler au tableau de bord**.
4. Ouvrez le tableau de bord du coffre.

    ![Ouvrez le tableau de bord coffre et le panneau Paramètres](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Sur le **éléments de sauvegarde** vignette, sélectionnez **Machines virtuelles**.

    ![Ouvrez la vignette d’éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Sur le **éléments de sauvegarde** panneau, vous pouvez afficher la liste des machines virtuelles protégées. Dans cet exemple, le coffre protège une machine virtuelle : demobackup.  

    ![Afficher le panneau éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. À partir du tableau de bord de l’élément coffre, modifier les stratégies de sauvegarde, exécuter un arrêt de sauvegarde, à la demande ou reprendre la protection des machines virtuelles, supprimer les données de sauvegarde, afficher les points de restauration et exécuter une restauration.

    ![Le tableau de bord d’éléments de sauvegarde et le panneau Paramètres](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gérer la stratégie de sauvegarde pour une machine virtuelle

Pour gérer une stratégie de sauvegarde :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/). Ouvrez le tableau de bord du coffre.
2. Sur le **éléments de sauvegarde** vignette, sélectionnez **Machines virtuelles**.

    ![Ouvrez la vignette d’éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Sur le **éléments de sauvegarde** panneau, vous pouvez afficher la liste des machines virtuelles protégées et le dernier état de la sauvegarde avec la dernière heure de points de restauration.

    ![Afficher le panneau éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. À partir du tableau de bord de l’élément coffre, vous pouvez sélectionner une stratégie de sauvegarde.

   * Pour basculer des stratégies, sélectionnez une autre stratégie, puis sélectionnez **enregistrer**. La nouvelle stratégie est appliquée immédiatement au coffre.

     ![Choisir une stratégie de sauvegarde](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande
Vous pouvez exécuter une sauvegarde à la demande d’une machine virtuelle une fois que vous configurez sa protection. N’oubliez pas ces détails :

- Si la sauvegarde initiale est en attente, et à la demande crée une copie complète de la machine virtuelle dans le coffre Recovery Services.
- Si la sauvegarde initiale est terminée, une sauvegarde à la demande envoyer seulement les modifications à partir de l’instantané précédent dans le coffre Recovery Services. Autrement dit, les sauvegardes ultérieures sont toujours incrémentielles.
- La durée de rétention pour une sauvegarde à la demande est la valeur de rétention que vous spécifiez lorsque vous déclenchez la sauvegarde.

Pour déclencher une sauvegarde à la demande :

1. Sur le [tableau de bord](#view-vms-on-the-dashboard), sous **élément protégé**, sélectionnez **élément de sauvegarde**.

    ![L’option maintenant de sauvegarde](./media/backup-azure-manage-vms/backup-now-button.png)

2. À partir de **Type de gestion de sauvegarde**, sélectionnez **Machine virtuelle Azure**. Le **l’élément de sauvegarde (Machine virtuelle de Azure)** panneau s’affiche.
3. Sélectionnez une machine virtuelle et sélectionnez **sauvegarder maintenant** pour créer une sauvegarde à la demande. Le **sauvegarder maintenant** panneau s’affiche.
4. Dans le **conserver sauvegarde jusqu'à ce que** champ, spécifiez une date de la sauvegarde doivent être conservées.

    ![Le calendrier sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-check.png)

5. Sélectionnez **OK** pour exécuter le travail de sauvegarde.

Pour suivre la progression du travail, sur le tableau de bord du coffre, sélectionnez le **travaux de sauvegarde** vignette.

## <a name="stop-protecting-a-vm"></a>Arrêter la protection d’une machine virtuelle

Il existe deux façons d’arrêter la protection d’une machine virtuelle :

- Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération. Dans ce cas, vous ne pourrez pas restaurer la machine virtuelle.
- Arrêter toutes les futures tâches de sauvegarde et de conserver les points de récupération. Bien que vous devez payer conserver les points de récupération dans le coffre, vous serez en mesure de restaurer la machine virtuelle si nécessaire. Pour plus d’informations, consultez [tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Si vous supprimez une source de données sans avoir à arrêter les sauvegardes, les nouvelles sauvegardes échouera. Anciens points de récupération va expirer conformément à la stratégie, mais un dernier point de récupération est toujours conservé jusqu'à ce que vous arrêtez les sauvegardes et supprimez les données.
>

Arrêter la protection pour une machine virtuelle :

1. Sur le [tableau de bord de l’élément du coffre](#view-vms-on-the-dashboard), sélectionnez **arrêter la sauvegarde**.
2. Choisissez s’il faut conserver ou supprimer les données de sauvegarde et confirmez votre sélection en fonction des besoins. Ajouter un commentaire si vous le souhaitez. Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour afficher le nom.

    ![Arrêter la protection](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Une notification vous informe que les travaux de sauvegarde ont été arrêtés.

## <a name="resume-protection-of-a-vm"></a>Reprendre la protection d’une machine virtuelle

Si vous conservez les données de sauvegarde lorsque vous arrêtez la machine virtuelle, vous pouvez réactiver ultérieurement la protection. Si vous supprimez les données de sauvegarde, vous ne pouvez pas reprendre la protection.

Pour reprendre la protection d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément du coffre](#view-vms-on-the-dashboard), sélectionnez **reprendre la sauvegarde**.

2. Suivez les étapes de [gérer les stratégies de sauvegarde](#manage-backup-policy-for-a-vm) pour attribuer la stratégie pour la machine virtuelle. Vous n’avez pas besoin de choisir une stratégie de protection initiale de la machine virtuelle.
3. Après avoir appliqué la stratégie de sauvegarde à la machine virtuelle, vous voyez le message suivant :

    ![Message indiquant une machine virtuelle protégée](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Vous pouvez supprimer les données de sauvegarde d’une machine virtuelle pendant le **arrêter la sauvegarde** travail ou une fois le travail de sauvegarde terminée. Avant de supprimer les données de sauvegarde, n’oubliez pas ces détails :

- Il peut être judicieux d’attendre plusieurs jours ou semaines avant de supprimer les points de récupération.
- Contrairement au processus de restauration des points de récupération, lorsque vous supprimez des données de sauvegarde, vous ne pouvez pas choisir des points de récupération spécifique à supprimer. Si vous supprimez vos données de sauvegarde, vous supprimez tous les points de récupération associées.

Une fois que vous arrêtez ou désactivez le travail de sauvegarde de la machine virtuelle, vous pouvez supprimer les données de sauvegarde :


1. Sur le [tableau de bord](#view-vms-on-the-dashboard), sélectionnez **supprimer les données de sauvegarde**.

    ![Sélectionnez Supprimer la sauvegarde](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Tapez le nom de l’élément de sauvegarde pour confirmer que vous souhaitez supprimer les points de récupération.

    ![Confirmer que vous souhaitez supprimer les points de récupération](./media/backup-azure-manage-vms/item-verification-box.png)

1. Pour supprimer les données de sauvegarde pour l’élément, sélectionnez **supprimer**. Un message de notification vous informe que les données de sauvegarde a été supprimées.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [sauvegarder des machines virtuelles Azure à partir des paramètres de la machine virtuelle](backup-azure-vms-first-look-arm.md).
- Découvrez comment [restaurer des machines virtuelles](backup-azure-arm-restore-vms.md).
- Découvrez comment [surveiller les sauvegardes de machines virtuelles Azure](backup-azure-monitor-vms.md).

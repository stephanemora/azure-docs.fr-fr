---
title: Gérer et surveiller les sauvegardes de machines virtuelles Azure
description: Découvrez comment gérer et superviser les sauvegardes de machine virtuelle Azure à l’aide du service Sauvegarde Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989686"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Gérer les sauvegardes de machines virtuelles Azure avec le service Sauvegarde Azure

Cet article explique comment gérer des machines virtuelles qui sont sauvegardées à l’aide du [service Sauvegarde Azure](backup-overview.md). De plus, cet article récapitule les informations de sauvegarde que vous pouvez trouver sur le tableau de bord du coffre.

Dans le portail Azure, le tableau de bord du coffre Recovery Services permet d’accéder à des informations sur le coffre, notamment :

* La sauvegarde la plus récente, c’est-à-dire le dernier point de restauration.
* La stratégie de sauvegarde.
* La taille totale de tous les instantanés de sauvegarde.
* Le nombre de machines virtuelles qui sont activées pour les sauvegardes.

Vous pouvez gérer les sauvegardes à l’aide du tableau de bord et en accédant aux machines virtuelles individuelles. Pour commencer les sauvegardes des machines, ouvrez le coffre sur le tableau de bord.

![Vue complète du tableau de bord avec curseur](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Afficher les machines virtuelles sur le tableau de bord

Pour afficher les machines virtuelles sur le tableau de bord du coffre :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu hub, sélectionnez **Parcourir**. Dans la liste des ressources, tapez **Recovery Services**. Quand vous écrivez, au fur et à mesure de votre saisie, la liste est filtrée. Sélectionnez **Coffres Recovery Services**.

    ![Créer un coffre Recovery Services](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Pour faire simple, cliquez avec le bouton droit sur le coffre, puis sélectionnez **Épingler au tableau de bord**.
4. Ouvrez le tableau de bord du coffre.

    ![Ouvrir le tableau de bord du coffre et le volet Paramètres](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Sur la vignette **Éléments de sauvegarde**, sélectionnez **Machines virtuelles Azure**.

    ![Ouvrir la vignette Éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Dans le volet **Éléments de sauvegarde**, vous pouvez voir la liste des machines virtuelles protégées. Dans cet exemple, le coffre protège une machine virtuelle : demobackup.  

    ![Afficher le volet Éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. À partir du tableau de bord de l’élément de coffre, modifiez les stratégies de sauvegarde, exécutez une sauvegarde à la demande, arrêtez ou redémarrez la protection des machines virtuelles, supprimez des données de sauvegarde, affichez les points de restauration et exécutez une restauration.

    ![Tableau de bord Éléments de sauvegarde et volet Paramètres](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Gérer la stratégie de sauvegarde pour une machine virtuelle

Pour gérer une stratégie de sauvegarde :

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Ouvrez le tableau de bord du coffre.
2. Sur la vignette **Éléments de sauvegarde**, sélectionnez **Machines virtuelles Azure**.

    ![Ouvrir la vignette Éléments de sauvegarde](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Dans le volet **Éléments de sauvegarde**, vous pouvez voir la liste des machines virtuelles protégées et l’état de la dernière sauvegarde avec l’heure des derniers points de restauration.

    ![Afficher le volet Éléments de sauvegarde](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. À partir du tableau de bord de l’élément de coffre, vous pouvez sélectionner une stratégie de sauvegarde.

   * Pour changer de stratégie, sélectionnez une stratégie différente, puis sélectionnez **Enregistrer**. La nouvelle stratégie est appliquée immédiatement au coffre.

     ![Choisir une stratégie de sauvegarde](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Vous pouvez exécuter une sauvegarde à la demande d’une machine virtuelle une fois que vous avez configuré sa protection. Gardez à l’esprit les détails ci-après :

* Si la sauvegarde initiale est en attente, la sauvegarde à la demande crée une copie complète de la machine virtuelle dans le coffre Recovery Services.
* Si la sauvegarde initiale est terminée, une sauvegarde à la demande envoie au coffre Recovery Services uniquement les modifications par rapport à l’instantané précédent. Autrement dit, les sauvegardes ultérieures sont toujours incrémentielles.
* La plage de conservation pour une sauvegarde à la demande correspond à la valeur de conservation que vous spécifiez quand vous déclenchez la sauvegarde.

Pour déclencher une sauvegarde à la demande :

1. Sur le [tableau de bord de l’élément de coffre](#view-vms-on-the-dashboard), sous **Élément protégé**, sélectionnez **Sauvegarder maintenant**.

    ![Option Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-button.png)

2. À partir de **Type de gestion des sauvegardes**, sélectionnez **Machine virtuelle Azure**. Le volet **Élément de sauvegarde (Machine virtuelle Azure)** apparaît.
3. Sélectionnez une machine virtuelle, puis sélectionnez **Sauvegarder maintenant** pour créer une sauvegarde à la demande. Le volet **Sauvegarder maintenant** apparaît.
4. Dans le champ **Conserver la sauvegarde jusqu’au**, spécifiez une date pour la sauvegarde à conserver.

    ![Calendrier Sauvegarder maintenant](./media/backup-azure-manage-vms/backup-now-check.png)

5. Sélectionnez **OK** pour exécuter le travail de sauvegarde.

Pour suivre la progression du travail, sur le tableau de bord du coffre, sélectionnez la vignette **Travaux de sauvegarde**.

## <a name="stop-protecting-a-vm"></a>Arrêter la protection d’une machine virtuelle

Il existe deux façons d’arrêter la protection d’une machine virtuelle :

* **Arrêter la protection et conserver les données de sauvegarde**. Cette option empêche toutes les tâches de sauvegarde futures de protéger votre machine virtuelle. Toutefois, le service Sauvegarde Azure conserve les points de récupération qui ont été sauvegardés.  Vous devrez payer pour conserver ces points de récupération dans le coffre (consultez l’article [Tarification de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/) pour en savoir plus). Vous serez en mesure de restaurer la machine virtuelle si nécessaire. Si vous décidez de reprendre la protection des machines virtuelles, vous pourrez utiliser l’option *Reprendre la sauvegarde*.
* **Arrêter la protection et supprimer les données de sauvegarde**. Cette option empêche toutes les futures tâches de sauvegarde de protéger votre machine virtuelle et supprime tous les points de récupération. Vous ne pourrez pas restaurer la machine virtuelle, ni utiliser l’option *Reprendre la sauvegarde*.

>[!NOTE]
>Si vous supprimez une source de données sans arrêter les sauvegardes, les nouvelles sauvegardes échouent. Les anciens points de récupération expirent conformément à la stratégie, mais un dernier point de récupération est conservé jusqu’à ce que vous arrêtiez les sauvegardes et supprimiez les données.
>

### <a name="stop-protection-and-retain-backup-data"></a>Arrêter la protection et conserver les données de sauvegarde

Pour arrêter la protection et conserver les données d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément de coffre](#view-vms-on-the-dashboard), sélectionnez **Arrêter la sauvegarde**.
2. Choisissez **Conserver les données de sauvegarde** et confirmez votre choix si nécessaire. Ajoutez un commentaire si vous le souhaitez. Si vous n’êtes pas sûr du nom de l’élément, survolez le point d’exclamation pour en afficher le nom.

    ![Conserver les données de sauvegarde](./media/backup-azure-manage-vms/retain-backup-data.png)

Une notification vous informe que les travaux de sauvegarde ont été interrompus.

### <a name="stop-protection-and-delete-backup-data"></a>Arrêter la protection et supprimer les données de sauvegarde

Pour arrêter la protection et supprimer les données d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément de coffre](#view-vms-on-the-dashboard), sélectionnez **Arrêter la sauvegarde**.
2. Choisissez **Supprimer les données de sauvegarde** et confirmez votre choix si nécessaire. Entrez le nom de l’élément de sauvegarde et ajoutez un commentaire si vous le souhaitez.

    ![Supprimer les données de sauvegarde](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Reprendre la protection d’une machine virtuelle

Si vous avez choisi l’option [Arrêter la protection et conserver les données de sauvegarde](#stop-protection-and-retain-backup-data) lors de la configuration de l’arrêt de la protection de la machine virtuelle, vous pouvez utiliser l’option **Reprendre la sauvegarde**. Cette option n’est pas disponible si vous aviez choisi [Arrêter la protection et supprimer les données de sauvegarde](#stop-protection-and-delete-backup-data) ou [Supprimer les données de sauvegarde](#delete-backup-data).

Pour reprendre la protection d’une machine virtuelle :

1. Sur le [tableau de bord de l’élément de coffre](#view-vms-on-the-dashboard), sélectionnez **Reprendre la sauvegarde**.

2. Suivez les étapes de la section [Modifier les stratégies de sauvegarde](#manage-backup-policy-for-a-vm) pour affecter la stratégie de la machine virtuelle. Vous n’avez pas besoin de choisir la stratégie de protection initiale de la machine virtuelle.
3. Après avoir appliqué la stratégie de sauvegarde à la machine virtuelle, vous voyez le message suivant :

    ![Message indiquant que la protection d’une machine virtuelle a réussi](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Il existe deux façons de supprimer les données de sauvegarde d’une machine virtuelle :

* À partir du tableau de bord d’un élément du coffre, sélectionnez Arrêter la sauvegarde et suivez les instructions pour [Arrêter la protection et supprimer les données de sauvegarde](#stop-protection-and-delete-backup-data).

  ![Sélectionner Arrêter la sauvegarde](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Dans le tableau de bord d’un élément du coffre, sélectionnez Supprimer les données de sauvegarde. Cette option est disponible si vous aviez choisi [Arrêter la protection et conserver les données de sauvegarde](#stop-protection-and-retain-backup-data) lors de la configuration de l’arrêt de la protection de la machine virtuelle.

  ![Sélectionner l’option de suppression de la sauvegarde](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * Sur le [tableau de bord de l’élément de coffre](#view-vms-on-the-dashboard), sélectionnez **Supprimer les données de sauvegarde**.
  * Tapez le nom de l’élément de sauvegarde pour confirmer la suppression des points de récupération.

    ![Supprimer les données de sauvegarde](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Pour supprimer les données de sauvegarde relatives à l’élément, sélectionnez **Supprimer**. Un message de notification vous informe que les données de sauvegarde ont été supprimées.

Pour protéger vos données, Sauvegarde Azure comprend la fonctionnalité de suppression réversible. Avec la suppression réversible, même après la suppression de la sauvegarde (tous les points de récupération) d’une machine virtuelle, les données de sauvegarde sont conservées pendant 14 jours supplémentaires. Pour plus d’informations, consultez la [documentation relative à la suppression réversible](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > Lorsque vous supprimez les données de sauvegarde, vous supprimez tous les points de récupération associés. Vous ne pouvez pas choisir des points de récupération spécifiques à supprimer.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Élément de sauvegarde où la source de données primaire n’existe plus

* Si les machines virtuelles Azure configurées pour la sauvegarde Azure sont supprimées ou déplacées sans arrêter la protection, les travaux de sauvegarde planifiés et à la demande (ad hoc) échouent avec l’erreur UserErrorVmNotFoundV2. La prévérification de sauvegarde apparaît comme étant critique uniquement pour les travaux de sauvegarde à la demande qui ont échoué (les travaux planifiés qui ont échoué n’apparaissent pas).
* Ces éléments de sauvegarde restent actifs dans le système qui adhère à la stratégie de sauvegarde et de conservation définie par l’utilisateur. Les données sauvegardées pour ces machines virtuelles Azure sont conservées conformément à la stratégie de conservation. Les points de récupération qui ont expiré (à l’exception du dernier) sont nettoyés en fonction de la plage de conservation définie dans la stratégie de sauvegarde.
* Il est recommandé aux utilisateurs de supprimer les éléments de sauvegarde dans lesquels la source de données principale n’existe plus afin d’éviter tout surcoût, si l’élément/les données de sauvegarde des ressources supprimées ne sont plus nécessaires, car le dernier point de récupération est conservé indéfiniment et l’utilisateur est facturé conformément aux tarifs de sauvegarde applicables.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [sauvegarder une machine virtuelle Azure à partir des paramètres de celle-ci](backup-azure-vms-first-look-arm.md).
* Découvrez comment [restaurer des machines virtuelles](backup-azure-arm-restore-vms.md).
* Découvrez comment [superviser des sauvegardes de machines virtuelles Azure](backup-azure-monitor-vms.md).

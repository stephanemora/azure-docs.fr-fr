---
title: Gérer les disques managés Azure
description: Apprenez-en davantage sur la gestion de disque managé Azure à partir du Portail Azure.
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: 4b40a71a71ce543c66bf347ec745fea6ae384779
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708910"
---
# <a name="manage-azure-managed-disks"></a>Gérer les disques managés Azure

Cet article explique comment gérer un Disque managé Azure à partir du Portail Azure.

## <a name="stop-protection-preview"></a>Arrêter la protection (préversion)


Vous pouvez arrêter la protection d’un disque Azure de trois manières :

- **Arrêter la protection et conserver les données (conserver indéfiniment)**  : cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger votre disque. Cependant, le service Sauvegarde Azure conservera les points de récupération qui sont sauvegardés indéfiniment. Vous devrez payer pour conserver ces points de récupération dans le coffre (consultez l’article [Tarification de Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/) pour en savoir plus). Vous serez en mesure de restaurer le disque si nécessaire. Pour reprendre la protection des disques, utilisez l’option **Reprendre la sauvegarde**.

- **Arrêter la protection et conserver les données (conserver selon une stratégie)**  : cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger votre disque. Les points de récupération sont conservés conformément à une stratégie et sont facturables en fonction de la [tarification de la sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/). Toutefois, le dernier point de récupération est conservé définitivement.

- **Arrêter la protection et supprimer les données** : cette option vous permet d’empêcher tous les futurs travaux de sauvegarde de protéger vos disques, et de supprimer tous les points de récupération. Vous ne pourrez pas restaurer le disque, ni utiliser l’option **Reprendre la sauvegarde**.

### <a name="stop-protection-and-retain-data"></a>Arrêter la protection et conserver les données

1. Accédez au **Centre de sauvegarde** et sélectionnez **disques Azure**.

1. Dans la liste d’instances de sauvegarde de disques, sélectionnez l’instance que vous souhaitez conserver.

1. Sélectionnez **Arrêter la sauvegarde (préversion)** .

   :::image type="content" source="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-inline.png" alt-text="Capture d’écran montrant la sélection de l’instance de sauvegarde de disque Azure à arrêter." lightbox="./media/manage-azure-managed-disks/select-disk-backup-instance-to-stop-expanded.png":::
 
1. Sélectionnez l’une des options de conservation des données suivantes :

   1. Conserver indéfiniment
   1. Conserver conformément à une stratégie
 
   :::image type="content" source="./media/manage-azure-managed-disks/data-retention-options-for-disk-inline.png" alt-text="Capture d’écran montrant les options d’arrêt de la protection des instances de sauvegarde de disque." lightbox="./media/manage-azure-managed-disks/data-retention-options-for-disk-expanded.png":::

   Vous pouvez également sélectionner la raison de l’arrêt des sauvegardes dans la liste déroulante.

1. Cliquez sur **Arrêter la sauvegarde**.

1. Sélectionnez **Confirmer** pour arrêter la protection des données.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Capture d’écran montrant les options de conservation des instances de sauvegarde de disque à sélectionner." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>Arrêter la protection et supprimer les données

1. Accédez au **Centre de sauvegarde** et sélectionnez **disques Azure**.

1. Dans la liste d’instances de sauvegarde de disques, sélectionnez l’instance que vous souhaitez supprimer.

1. Cliquez sur **Arrêter la sauvegarde (préversion)** .

1. Sélectionnez **Supprimer les données de sauvegarde**.

   Indiquez le nom de l’instance de sauvegarde, la raison de la suppression et tout autre commentaire.

   :::image type="content" source="./media/manage-azure-managed-disks/details-to-stop-disk-backup-inline.png" alt-text="Capture d’écran de la confirmation de l’arrêt de la sauvegarde de disque." lightbox="./media/manage-azure-managed-disks/details-to-stop-disk-backup-expanded.png":::

1. Sélectionnez **Arrêter la sauvegarde**.

1. Sélectionnez **Confirmer** pour arrêter la protection des données.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-inline.png" alt-text="Capture d’écran montrant les options de conservation des instances de sauvegarde de disque à sélectionner." lightbox="./media/manage-azure-managed-disks/confirm-stopping-disk-backup-expanded.png":::

## <a name="resume-protection"></a>Reprendre la protection

Si vous avez sélectionné l’option **Arrêter la protection et conserver les données**, vous pouvez reprendre la protection de vos disques.

>[!Note]
>Lorsque vous commencez à protéger un disque, la stratégie de sauvegarde est également appliquée aux données conservées. Les points de récupération qui ont expiré conformément à la stratégie seront nettoyés.

Utiliser les étapes suivantes :

1. Accédez au **Centre de sauvegarde** et sélectionnez **disques Azure**.

1. Dans la liste d’instances de sauvegarde de disques, sélectionnez l’instance que vous souhaitez reprendre.

1. Sélectionnez **Reprendre la sauvegarde (préversion)** .

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-protection-inline.png" alt-text="Capture d’écran montrant l’option de reprise de la protection de disque." lightbox="./media/manage-azure-managed-disks/resume-disk-protection-expanded.png":::

1. Sélectionnez **Reprendre la sauvegarde**.

   :::image type="content" source="./media/manage-azure-managed-disks/resume-disk-backup-inline.png" alt-text="Capture d’écran montrant l’option de reprise de la sauvegarde de disque." lightbox="./media/manage-azure-managed-disks/resume-disk-backup-expanded.png":::

## <a name="delete-backup-instance"></a>Supprimer l’instance de sauvegarde

Si vous choisissez d’arrêter tous les travaux de sauvegarde planifiés, et de supprimer toutes les sauvegardes existantes, utilisez **Supprimer l’instance de sauvegarde**.

>[!Note]
>La suppression d’une instance de sauvegarde échoue si le groupe de ressources d’instantané est supprimé manuellement ou si l’autorisation d’accès à l’identité managée du coffre de sauvegarde est révoquée. En cas de défaillance, créez temporairement le groupe de ressources d’instantané (avec le même nom) et fournissez à l’identité managée du coffre de sauvegarde les autorisations de rôle requises, comme indiqué [ici](/azure/backup/backup-managed-disks-ps#assign-permissions). Vous trouverez le nom du groupe de ressources d’instantané dans l’onglet **Informations de base** de l’écran **Instance de sauvegarde**. 

Pour supprimer une instance de sauvegarde de disque, procédez comme suit :

1. Cliquez sur **Supprimer** sur l’écran d’instance de sauvegarde.

   :::image type="content" source="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-inline.png" alt-text="Capture d’écran montrant le processus de suppression d’une instance de sauvegarde." lightbox="./media/manage-azure-managed-disks/initiate-deleting-backup-instance-expanded.png":::

1. Fournissez des détails de confirmation, notamment le nom de l’instance de sauvegarde, la raison de la suppression et des commentaires supplémentaires.

   :::image type="content" source="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-inline.png" alt-text="Capture d’écran montrant la confirmation de la suppression d’instances de sauvegarde." lightbox="./media/manage-azure-managed-disks/confirm-deleting-backup-instance-expanded.png":::

1. Cliquez sur **Supprimer** pour confirmer et poursuivre la suppression de l’instance de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble des coffres de sauvegarde](backup-vault-overview.md)

---
title: Gérer les sauvegardes de partage de fichiers Azure
description: Cet article décrit les tâches courantes de gestion et de surveillance des partages de fichiers Azure sauvegardés par le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294891"
---
# <a name="manage-azure-file-share-backups"></a>Gérer les sauvegardes de partage de fichiers Azure

Cet article décrit les tâches courantes de gestion et de surveillance des partages de fichiers Azure sauvegardés par le service [Sauvegarde Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-overview). Vous allez apprendre à exécuter les tâches de gestion suivantes dans le coffre Microsoft Azure Recovery Services :

* [Surveiller des travaux](#monitor-jobs)
* [Créer une nouvelle stratégie](#create-a-new-policy)
* [Modifier la stratégie](#modify-policy)
* [Arrêter la protection sur un partage de fichiers](#stop-protection-on-a-file-share)
* [Reprendre la protection sur un partage de fichiers](#resume-protection-on-a-file-share)
* [Supprimer des données de sauvegarde](#delete-backup-data)
* [Annuler l’inscription du compte de stockage](#unregister-storage-account)

## <a name="monitor-jobs"></a>Surveiller des travaux

Lorsque vous déclenchez une opération de sauvegarde ou de restauration, le service de sauvegarde crée une tâche de suivi. Vous pouvez surveiller la progression de tous les travaux sur la page **Travaux de sauvegarde**.

Pour ouvrir la page **Travaux de sauvegarde** :

1. Ouvrez le coffre Microsoft Azure Recovery Services que vous avez utilisé pour configurer la sauvegarde de vos partages de fichiers. Dans le panneau de **Vue d’ensemble**, cliquez sur **Tâches de sauvegarde** dans la section **Analyse**.

   ![Travaux de sauvegarde dans la section analyse](./media/manage-afs-backup/backup-jobs.png)

2. Lorsque vous cliquez sur OK, le **Panneau des tâches de sauvegarde** s’affiche et indique l’état de toutes les tâches. Vous pouvez cliquer sur le nom de la charge de travail correspondant au partage de fichiers que vous souhaitez surveiller.

   ![Nom de la charge de travail](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Créer une nouvelle stratégie

Vous pouvez créer une nouvelle stratégie de sauvegarde des partages de fichiers Azure à partir des **Stratégies de sauvegarde** du coffre Recovery Services. Toutes les stratégies créées lorsque vous avez configuré la sauvegarde pour les partages de fichiers s’affichent avec le type de stratégie en tant que partage de fichiers Azure.

Pour afficher les stratégies de sauvegarde existantes :

1. Ouvrez le coffre Recovery Services que vous avez utilisé pour configurer la sauvegarde du partage de fichiers. Puis, dans le menu coffre Recovery Services, cliquez sur **Stratégies de sauvegarde** sous la section gérer. Toutes les stratégies de sauvegarde configurées dans le coffre sont répertoriées.

   ![Toutes les stratégies de sauvegarde](./media/manage-afs-backup/all-backup-policies.png)

2. Pour afficher les stratégies spécifiques au partage de fichiers Azure, sélectionnez **Partage de fichiers Azure** dans la liste déroulante en haut à droite.

   ![Choisir le partage de fichiers Azure](./media/manage-afs-backup/azure-file-share.png)

Pour créer une nouvelle stratégie de sauvegarde :

1. Cliquez sur **+A ajouter** dans le panneau Stratégies de sauvegarde.

   ![Nouvelle stratégie de sauvegarde](./media/manage-afs-backup/new-backup-policy.png)

2. Sélectionnez **Partage de fichiers Azure** comme **Type de stratégie** dans le panneau **Ajouter**. Le menu stratégie de sauvegarde pour le partage de fichiers Azure s’ouvre. Spécifiez le nom de la stratégie, la fréquence de sauvegarde et la durée de rétention des points de récupération. Lorsque vous avez défini la stratégie, cliquez sur **OK**.

   ![Définir la stratégie de sauvegarde](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modifier la stratégie

Vous pouvez modifier une stratégie de sauvegarde pour modifier la fréquence de sauvegarde ou la durée de rétention.

Pour modifier une stratégie :

1. Ouvrez le coffre Recovery Services que vous avez utilisé pour configurer la sauvegarde du partage de fichiers. Puis, dans le menu coffre Recovery Services, cliquez sur **Stratégies de sauvegarde** dans la section gérer. Toutes les stratégies de sauvegarde configurées dans le coffre sont répertoriées.

   ![Toutes les stratégies de sauvegarde dans le coffre](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Pour afficher les stratégies spécifiques au partage de fichiers Azure, sélectionnez **Partage de fichiers Azure** dans la liste déroulante en haut à droite. Cliquez sur la stratégie de sauvegarde que vous souhaitez modifier.

   ![Partage de fichiers Azure à modifier](./media/manage-afs-backup/azure-file-share-modify.png)

3. Le panneau de **Planification** s’ouvre. Modifiez la planification de sauvegarde ou la durée de rétention en fonction des besoins, puis cliquez sur **Enregistrer**. Vous verrez le message « mise à jour en cours » s’afficher dans le panneau, et lorsque les modifications de la stratégie seront correctement mises à jour, vous verrez le message « mise à jour réussie de la stratégie de sauvegarde ».

   ![Enregistrer la stratégie modifiée](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Arrêter la protection sur un partage de fichiers

Il existe deux façons de suspendre la protection des partages de fichiers Azure :

* Arrêter toutes les futures tâches de sauvegarde, et *Supprimer tous les points de récupération*
* Arrêter toutes les futures tâches de sauvegarde, mais *Conserver les points de récupération*

Il peut y avoir un coût résultant de la conservation des points de récupération dans le stockage, car les instantanés sous-jacents créés par la sauvegarde Azure seront conservés. Mais elle a l’avantage de vous permettre de restaurer ultérieurement le partage de fichiers, si vous le souhaitez. Pour plus d’informations sur le coût de sortie des points de récupération, consultez les [Détails de tarification](https://azure.microsoft.com/pricing/details/backup/). Si vous choisissez de supprimer tous les points de récupération, vous ne pourrez pas restaurer le partage de fichiers.

Pour arrêter la protection d’un partage de fichiers Azure :

1. Ouvrez le coffre Recovery Services qui contient les points de récupération de partage de fichiers, puis cliquez sur **Éléments de sauvegarde** dans la section éléments protégés. La liste des types d’éléments de sauvegarde s’affiche.

   ![Éléments de sauvegarde](./media/manage-afs-backup/backup-items.png)

2. Dans la liste **Type de gestion de sauvegarde**, sélectionnez **Stockage Azure (fichiers Azure)** . La liste des **Éléments de sauvegarde pour (stockage Azure [Azure Files])** s’affiche.

   ![Sélectionner le stockage Azure (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Dans la liste des **Éléments de sauvegarde (stockage Azure (Azure Files))** , sélectionnez l’élément de sauvegarde que vous ne souhaitez plus protéger.

4. Sélectionnez l’option **Arrêter la sauvegarde** dans le menu d’**Élément de sauvegarde**.

   ![Sélectionner Arrêter la sauvegarde](./media/manage-afs-backup/stop-backup.png)

5. Dans le panneau **Arrêter la sauvegarde**, choisissez de **Conserver les données de sauvegarde** ou **Supprimer les données de sauvegarde**, puis cliquez sur **Arrêter la sauvegarde**.

    ![Choisissez de conserver ou de supprimer les données de sauvegarde](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Reprendre la protection sur un partage de fichiers

Si vous avez choisi l’option **Conserver les données de sauvegarde** au moment de l’arrêt de la protection du partage de fichiers, vous avez alors la possibilité de restaurer la protection. Si vous avez choisi l’option **Supprimer les données de sauvegarde**, vous ne pourrez pas restaurer la protection du partage de fichiers.

Pour reprendre la protection d’un partage de fichiers Azure :

1. Ouvrez le coffre Recovery Services qui contient les points de récupération de partage de fichiers, puis cliquez sur **Éléments de sauvegarde** dans la section éléments protégés. La liste des types d’éléments de sauvegarde s’affiche.

   ![Éléments de sauvegarde pour restauration](./media/manage-afs-backup/backup-items-resume.png)

2. Dans la liste **Type de gestion de sauvegarde**, sélectionnez **Stockage Azure (fichiers Azure)** . La liste des **Éléments de sauvegarde pour (stockage Azure [Azure Files])** s’affiche.

   ![Liste des stockages Azure (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. Dans la liste des **Éléments de sauvegarde (stockage Azure (Azure Files))** , sélectionnez l’élément de sauvegarde que vous ne souhaitez plus protéger.

4. Sélectionnez l’option **Arrêter la sauvegarde** dans le menu d’**Élément de sauvegarde**.

   ![Sélectionnez Reprendre la sauvegarde](./media/manage-afs-backup/resume-backup.png)

5. La **Stratégie de sauvegarde** s’ouvre et vous pouvez choisir la stratégie de votre choix pour reprendre la sauvegarde.

6. Cliquez sur **Enregistrer** après avoir sélectionné la**Stratégie de sauvegarde** souhaitée. Vous verrez un message « mise à jour en cours » sur le portail. Une fois la sauvegarde reprise, vous verrez le message « stratégie de sauvegarde mise à jour avec succès pour le partage de fichiers Azure protégé ».

   ![Stratégie de sauvegarde mise à jour avec succès](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Vous pouvez supprimer la sauvegarde d’un partage de fichiers pendant la tâche d’**Arrêt la sauvegarde**, ou à tout moment après avoir arrêté la protection. Il peut même être préférable d’attendre plusieurs jours ou semaines avant de supprimer les points de récupération. Lorsque vous supprimez des données de sauvegarde, vous ne pouvez pas choisir de points de récupération spécifiques à supprimer. Si vous choisissez de supprimer vos données de sauvegarde, vous supprimez tous les points de récupération associés au partage de fichiers.

La procédure suivante suppose que le partage de fichiers n’est plus protégé.

Pour supprimer les données de sauvegarde pour le partage de fichiers  Azure :

1. Une fois la tâche de sauvegarde arrêtée, les options **Reprendre la sauvegarde** et **Supprimer les données de sauvegarde** sont accessibles dans le tableau de bord **Élément de sauvegarde**. Cliquez sur l’option **Supprimer les données de sauvegarde** dans le menu de l’**Élément de sauvegarde**.

   ![Supprimer les données de sauvegarde](./media/manage-afs-backup/delete-backup-data.png)

2. Le panneau **supprimer les données de sauvegarde** s’ouvre. Tapez le nom du partage de fichiers pour en confirmer la suppression. Vous pouvez, si vous le souhaitez, indiquer une **Raison** pour la suppression ou un **Commentaire**. Cliquez sur **Supprimer** lorsque que vous souhaitez vraiment supprimer les données de la sauvegarde.

   ![Confirmer la suppression des données](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Annuler l’inscription du compte de stockage

Si vous souhaitez protéger vos partages de fichiers dans un compte de stockage particulier à l’aide d’un coffre Recovery Services différent, commencez par [Arrêter la protection de tous les partages de fichiers](#stop-protection-on-a-file-share) dans ce compte de stockage. Désinscrivez ensuite le compte du coffre Recovery Services utilisé pour la protection.

La procédure suivante suppose que la protection a été arrêtée pour tous les partages de fichiers dans le compte de stockage dont vous souhaitez vous désinscrire.

Pour annuler l’inscription du compte de stockage :

1. Ouvrez le coffre Recovery Services dans lequel votre compte de stockage est inscrit.
2. Cliquez sur l’option d’**Infrastructure de sauvegarde** sous la section **Gestion** du **Panneau de vue d’ensemble**.

   ![Cliquez sur infrastructure de sauvegarde](./media/manage-afs-backup/backup-infrastructure.png)

3. Le panneau d’**infrastructure de sauvegarde** s’ouvre. Cliquez sur **Comptes de stockage** dans la section **Comptes de stockage Azure** dans ce panneau.

   ![Cliquez sur Comptes de stockage](./media/manage-afs-backup/storage-accounts.png)

4. Une fois que vous avez cliqué sur **Comptes de stockage**, une liste de comptes de stockage inscrits avec le coffre s’affiche.
5. Cliquez avec le bouton droit sur le compte de stockage dont vous souhaitez annuler l’inscription, puis choisissez **Annuler l’inscription**.

   ![Sélectionnez désinscrire](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [détecter un problème de sauvegarde/restauration de défaillance pour les partages de fichiers Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)

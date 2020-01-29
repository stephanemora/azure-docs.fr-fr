---
title: Sauvegarder des partages de fichiers Azure dans le portail Azure
description: Découvrir comment utiliser le portail Azure pour sauvegarder des partages de fichiers Azure sauvegardés dans le coffre Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294879"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Sauvegarder des partages de fichiers Azure dans un coffre Recovery Services

Cet article explique comment utiliser le portail Azure pour sauvegarder des [partages de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Dans ce guide, vous allez apprendre à :

* Créer un coffre Recovery Services
* Découvrir les partages de fichiers et configurer des sauvegardes
* Exécuter un travail de sauvegarde à la demande pour créer un point de restauration

## <a name="prerequisites"></a>Conditions préalables requises

* Identifiez ou créez un [coffre Recovery Services](#create-a-recovery-services-vault) dans la même région que le compte de stockage hébergeant le partage de fichiers.

* Assurez-vous que le partage de fichiers est présent dans l’un des [types de compte de stockage pris en charge](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitations pour la sauvegarde de partage de fichiers Azure en préversion

La sauvegarde des partages de fichiers Azure est disponible en préversion. Les partages de fichiers Azure dans les comptes de stockage v1 et v2 universels sont pris en charge. Voici les limitations liées à la sauvegarde des partages de fichiers Azure :

* La prise en charge de la sauvegarde des partages de fichiers Azure dans les comptes de stockage avec réplication de [stockage redondant interzone](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) est actuellement limitée à [ces régions](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Sauvegarde Azure prend actuellement en charge la configuration de sauvegardes planifiées une fois par jour des partages de fichiers Azure.
* Vous pouvez effectuer une seule sauvegarde planifiée par jour.
* Vous pouvez effectuer quatre sauvegardes à la demande par jour maximum.
* Utilisez les [verrous de ressources](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) sur le compte de stockage pour empêcher la suppression accidentelle des sauvegardes de votre coffre Recovery Services.
* Ne supprimez pas les instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration.
* Ne supprimez pas les partages de fichiers qui sont protégés par Sauvegarde Azure. La solution actuelle supprimera tous les instantanés pris par Sauvegarde Azure une fois que le partage de fichiers aura été supprimé et, dès lors, que tous les points de restauration seront perdus.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modifier la réplication du stockage

Par défaut, les coffres utilisent le [stockage géoredondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Si le coffre est votre principal mécanisme de sauvegarde, nous vous recommandons d’utiliser le GRS.

* Vous pouvez utiliser le [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en guise d’option plus économique.

Modifiez le type de réplication de stockage comme suit :

1. Dans le nouveau coffre, cliquez sur **Propriétés** dans la section **Paramètres**.

2. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

    ![Mettre à jour la configuration de sauvegarde](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Vous ne pouvez pas modifier le type de réplication de stockage une fois que le coffre est configuré et qu’il contient des éléments de sauvegarde. Pour ce faire, vous devez recréer le coffre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Détecter les partages de fichiers et configurer la sauvegarde

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le coffre Recovery Services que vous souhaitez utiliser pour sauvegarder le partage de fichiers.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **+Sauvegarde**.

   ![Coffre Recovery Services](./media/backup-afs/recovery-services-vault.png)

   a. Dans **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur **Azure**.

    ![Choisir le partage de fichiers Azure comme objectif de sauvegarde](./media/backup-afs/backup-goal.png)

    b.    Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **Partage de fichiers Azure** à partir du menu déroulant.

    c.    Cliquez sur **Sauvegarde** pour inscrire l’extension de partage de fichiers Azure dans le coffre.

      ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-afs/register-extension.png)

3. Lorsque vous cliquez sur **Sauvegarde**, le panneau sauvegarde s’ouvre et vous invite à sélectionner un compte de stockage dans la liste des comptes de stockage pris en charge découverts. Ils sont associés à ce coffre ou présents dans la même région que le coffre, mais pas encore associés à un coffre Recovery Services.

   ![Sélectionner le compte de stockage](./media/backup-afs/select-storage-account.png)

4. Dans la liste des comptes de stockage détectés, sélectionnez un compte et cliquez sur **OK**. Azure recherche le compte de stockage pour les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et que vous ne les voyez pas dans la liste, patientez jusqu'à ce qu’ils s’affichent.

    ![Découverte des partages de fichiers](./media/backup-afs/discovering-file-shares.png)

5. À partir de la liste **Partages de fichiers**, sélectionnez un ou plusieurs des partages de fichiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

6. Après avoir choisi vos partages de fichiers, le menu **Sauvegarde** bascule vers **Stratégie de sauvegarde**. Dans ce menu, sélectionnez une stratégie de sauvegarde existante, ou créez-en une, puis cliquez sur **Activer la sauvegarde**.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-afs/select-backup-policy.png)

Une fois la stratégie de sauvegarde établie, un instantané des partages de fichiers sera effectué à l’heure planifiée, et le point de récupération est conservé pour la période définie.

## <a name="create-an-on-demand-backup"></a>Créer une sauvegarde à la demande

Vous souhaiterez parfois générer un instantané de sauvegarde ou un point de récupération en dehors des heures planifiées dans la stratégie de sauvegarde. Souvent que la génération d'une sauvegarde à la demande intervient immédiatement après que vous avez configuré la stratégie de sauvegarde. En fonction du calendrier dans la stratégie de sauvegarde, des heures ou des jours peuvent s’écouler avant qu’un instantané soit généré. Pour protéger vos données en attendant que la stratégie de sauvegarde génère un instantané, effectuez une sauvegarde à la demande. La création d’une sauvegarde à la demande est souvent nécessaire avant d’apporter des modifications prévues à vos partages de fichiers.

### <a name="to-create-an-on-demand-backup"></a>Pour créer une sauvegarde à la demande

1. Ouvrez le coffre Recovery Services que vous avez utilisé pour sauvegarder votre partage de fichiers, puis cliquez sur **Éléments de sauvegarde** sous la section **Éléments protégés** du panneau **Vue d'ensemble**.

   ![Cliquez sur éléments de sauvegarde](./media/backup-afs/backup-items.png)

2. Une fois que vous avez cliqué sur **Éléments de sauvegarde**, un nouveau panneau répertoriant tous les **types de gestion des sauvegardes** s’affiche à côté du panneau **Vue d’ensemble** comme suit :

   ![Liste des types de gestion des sauvegardes](./media/backup-afs/backup-management-types.png)

3. Dans la liste **Type de gestion de sauvegarde**, sélectionnez **Stockage Azure (Azure Files)** . Vous verrez une liste de tous les partages de fichiers et de leurs comptes de stockage correspondants sauvegardés avec ce coffre.

   ![Éléments de sauvegarde Stockage Azure (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

4. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers désiré. Les détails associés à **Élément de sauvegarde** s’affichent. Dans le menu **Élément de sauvegarde**, cliquez sur **Sauvegarder maintenant**. Comme il s’agit d’un travail de sauvegarde à la demande, il n’existe aucune stratégie de rétention associée au point de récupération.

   ![Cliquez sur Sauvegarder maintenant](./media/backup-afs/backup-now.png)

5. Le panneau **Sauvegarder maintenant** s’affiche. Spécifiez le dernier jour que vous souhaitez conserver dans le point de récupération. La conservation maximale d'une sauvegarde à la demande est de 10 ans.

   ![Choisissez la date de conservation](./media/backup-afs/retention-date.png)

6. Cliquez sur **OK** pour confirmer l'exécution de la tâche de sauvegarde à la demande.

7. Surveillez les notifications du portail pour conserver une trace de la bonne exécution des tâches de sauvegarde. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**.

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [Restaurer des partages de fichiers Azure](restore-afs.md)

* Découvrir comment [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)

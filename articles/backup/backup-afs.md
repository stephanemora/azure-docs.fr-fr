---
title: Sauvegarder des partages de fichiers Azure dans le portail Azure
description: Découvrir comment utiliser le portail Azure pour sauvegarder des partages de fichiers Azure sauvegardés dans le coffre Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938161"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Sauvegarder des partages de fichiers Azure dans un coffre Recovery Services

Cet article explique comment utiliser le portail Azure pour sauvegarder des [partages de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Dans cet article, vous allez apprendre à :

* Créez un coffre Recovery Services.
* Détectez les partages de fichiers et configurez les sauvegardes.
* Exécutez un travail de sauvegarde à la demande pour créer un point de restauration.

## <a name="prerequisites"></a>Conditions préalables requises

* Identifiez ou créez un [coffre Recovery Services](#create-a-recovery-services-vault) dans la même région que le compte de stockage hébergeant le partage de fichiers.
* Assurez-vous que le partage de fichiers est présent dans l’un des [types de comptes de stockage pris en charge](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limites des sauvegardes de partage de fichiers Azure dans la préversion

La sauvegarde de partages de fichiers Azure est disponible en préversion. Les partages de fichiers Azure dans les comptes de stockage v1 et v2 universels sont pris en charge. Voici les limitations liées à la sauvegarde des partages de fichiers Azure :

* La prise en charge de la sauvegarde des partages de fichiers Azure dans des comptes de stockage avec réplication de [stockage redondant interzone](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) est actuellement limitée à [ces régions](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Sauvegarde Azure prend actuellement en charge la configuration de sauvegardes planifiées une fois par jour des partages de fichiers Azure.
* Vous pouvez effectuer une seule sauvegarde planifiée par jour.
* Vous pouvez effectuer quatre sauvegardes à la demande par jour maximum.
* Utilisez les [verrous de ressources](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) sur le compte de stockage pour empêcher la suppression accidentelle des sauvegardes de votre coffre Recovery Services.
* Ne supprimez pas d’instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération ou des échecs de restauration.
* Ne supprimez pas de partages de fichiers protégés par Sauvegarde Azure. La solution actuelle supprime tous les instantanés pris par Sauvegarde Azure une fois le partage de fichiers supprimé, entraînant la perte de tous les points de restauration.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modifier la réplication du stockage

Par défaut, les coffres utilisent le [stockage géoredondant (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Si le coffre est votre principal mécanisme de sauvegarde, nous vous suggérons d’utiliser l’option GRS.
* Vous pouvez utiliser le [stockage localement redondant (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en guise d’option plus économique.

Modifiez le type de réplication de stockage :

1. Dans le nouveau coffre, dans la section **Paramètres**, sélectionnez **Propriétés**.

1. Dans la page **Propriétés**, sous **Configuration de la sauvegarde**, sélectionnez **Mettre à jour**.

1. Choisissez le type de réplication de stockage, puis sélectionnez **Enregistrer**.

    ![Mettre à jour la configuration de sauvegarde](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Vous ne pouvez pas modifier le type de réplication de stockage une fois que le coffre est configuré et qu’il contient des éléments de sauvegarde. Pour ce faire, vous devez recréer le coffre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Détecter les partages de fichiers et configurer la sauvegarde

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le coffre Recovery Services que vous souhaitez utiliser pour sauvegarder le partage de fichiers.

1. Dans le tableau de bord **coffre Recovery Services**, sélectionnez **+Sauvegarde**.

   ![Coffre Recovery Services](./media/backup-afs/recovery-services-vault.png)

    a. Dans **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur **Azure**.

    ![Choisir un partage de fichiers Azure comme objectif de sauvegarde](./media/backup-afs/backup-goal.png)

    b.  Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **Partage de fichiers Azure** à partir de la liste déroulante.

    c.  Sélectionnez **Sauvegarde** pour inscrire l’extension de partage de fichiers Azure dans le coffre.

    ![Sélectionnez Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-afs/register-extension.png)

1. Lorsque vous sélectionnez **Sauvegarde**, le panneau **Sauvegarde** s’ouvre et vous invite à sélectionner un compte de stockage dans la liste des comptes de stockage pris en charge découverts. Ceux-ci sont associés à ce coffre ou présents dans la même région que le coffre, mais pas encore associés à un coffre Recovery Services.

   ![Sélectionner le compte de stockage](./media/backup-afs/select-storage-account.png)

1. Dans la liste des comptes de stockage détectés, sélectionnez un compte, puis **OK**. Azure recherche dans le compte de stockage les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et ne les voyez pas dans la liste, patientez un peu jusqu’à ce qu’ils s’affichent.

    ![Découverte de partages de fichiers](./media/backup-afs/discovering-file-shares.png)

1. Dans la liste **Partages de fichiers**, sélectionnez un ou plusieurs partages de fichiers à sauvegarder. Sélectionnez **OK**.

1. Une fois vos partages de fichiers choisis, le menu **Sauvegarde** bascule vers **Stratégie de sauvegarde**. Dans ce menu, sélectionnez ou créez une stratégie de sauvegarde. Sélectionnez **Activer la sauvegarde**.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-afs/select-backup-policy.png)

Une fois que vous avez défini une stratégie de sauvegarde, un instantané des partages de fichiers est pris à l’heure planifiée. Le point de récupération est également conservé pendant la période choisie.

## <a name="create-an-on-demand-backup"></a>Créer une sauvegarde à la demande

Vous pouvez occasionnellement générer un instantané de sauvegarde ou un point de récupération en dehors des heures planifiées dans la stratégie de sauvegarde. Souvent que la génération d'une sauvegarde à la demande intervient immédiatement après que vous avez configuré la stratégie de sauvegarde. Selon la planification définie dans la stratégie de sauvegarde, des heures ou des jours peuvent s’écouler avant la prise d’un instantané. Pour protéger vos données en attendant que la stratégie de sauvegarde génère un instantané, effectuez une sauvegarde à la demande. La création d’une sauvegarde à la demande est souvent nécessaire avant d’apporter des modifications prévues à vos partages de fichiers.

### <a name="create-a-backup-job-on-demand"></a>Créer un travail de sauvegarde à la demande

1. Ouvrez le coffre de Recovery Services que vous avez utilisé pour sauvegarder votre partage de fichiers. Dans le volet **Vue d’ensemble**, dans la section **Éléments protégés**, sélectionnez **Éléments de sauvegarde**.

   ![Sélectionner les éléments de sauvegarde](./media/backup-afs/backup-items.png)

1. Une fois les **Éléments de sauvegarde** sélectionnés, un nouveau volet répertoriant tous les **Types de gestion des sauvegardes** apparaît en regard du volet **Vue d’ensemble**.

   ![Liste des types de gestion des sauvegardes](./media/backup-afs/backup-management-types.png)

1. Dans la liste **Type de gestion de sauvegarde**, sélectionnez **Stockage Azure (Azure Files)** . Vous voyez s’afficher une liste de tous les partages de fichiers et des comptes de stockage correspondants sauvegardés à l’aide de ce coffre.

   ![Éléments de sauvegarde Stockage Azure (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers de votre choix. Les détails associés à **Élément de sauvegarde** s’affichent. Dans le menu **Élément de sauvegarde**, sélectionnez **Sauvegarder maintenant**. Comme il s’agit d’un travail de sauvegarde à la demande, aucune stratégie de rétention n’est associée au point de récupération.

   ![Sélectionner Sauvegarder maintenant](./media/backup-afs/backup-now.png)

1. Le volet **Sauvegarder maintenant** s’ouvre. Spécifiez le dernier jour que vous souhaitez conserver dans le point de récupération. La conservation maximale d'une sauvegarde à la demande est de 10 ans.

   ![Choisissez la date de conservation](./media/backup-afs/retention-date.png)

1. Cliquez sur **OK** pour confirmer l’exécution du travail de sauvegarde à la demande.

1. Surveillez les notifications du portail pour conserver une trace de la bonne exécution des tâches de sauvegarde. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre. Sélectionnez **Travaux de sauvegarde** > **En cours**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :
* [Restaurer des partages de fichiers Azure](restore-afs.md)
* [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)

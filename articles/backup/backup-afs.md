---
title: Sauvegarder des partages de fichiers Azure dans le portail Azure
description: Découvrir comment utiliser le portail Azure pour sauvegarder des partages de fichiers Azure sauvegardés dans le coffre Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 76bf8e00dede5f227cb862f9c9474844e349e298
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391149"
---
# <a name="back-up-azure-file-shares"></a>Sauvegarder des partages de fichiers Azure

Cet article explique comment utiliser le portail Azure pour sauvegarder des [partages de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Dans cet article, vous allez apprendre à :

* Créez un coffre Recovery Services.
* Détectez les partages de fichiers et configurez les sauvegardes.
* Exécutez un travail de sauvegarde à la demande pour créer un point de restauration.

## <a name="prerequisites"></a>Prérequis

* Identifiez ou créez un [coffre Recovery Services](#create-a-recovery-services-vault) dans la même région que le compte de stockage hébergeant le partage de fichiers.
* Assurez-vous que le partage de fichiers est présent dans l’un des [types de comptes de stockage pris en charge](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-file-shares-and-configure-backup"></a>Détecter les partages de fichiers et configurer la sauvegarde

1. Dans le [Portail Azure](https://portal.azure.com/), ouvrez le coffre Recovery Services que vous souhaitez utiliser pour configurer la sauvegarde de votre partage de fichiers.

1. Dans le volet **Coffre Recovery Services**, sélectionnez **+Sauvegarde** dans le menu situé en haut.

   ![Coffre Recovery Services](./media/backup-afs/recovery-services-vault.png)

    1. Dans le volet **Objectif de sauvegarde**, définissez **Où s'exécute votre charge de travail ?** sur **Azure** en sélectionnant l’option **Azure** dans la liste déroulante.

          ![Choisir Azure en tant que charge de travail](./media/backup-afs/backup-goal.png)

    2. Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **Partage de fichiers Azure** à partir de la liste déroulante.

          ![Sélectionner Azure FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Sélectionnez **Sauvegarde** pour inscrire l’extension de partage de fichiers Azure dans le coffre.

          ![Sélectionnez Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-afs/register-extension.png)

1. Une fois que vous avez sélectionné **Sauvegarde**, le volet **Sauvegarde** s’ouvre. Pour sélectionner le compte de stockage hébergeant le partage de fichiers que vous souhaitez protéger, cliquez sur le texte du lien **Sélectionner** sous la zone de texte **Compte de stockage**.

   ![Choisir le lien Sélectionner](./media/backup-afs/choose-select-link.png)

1. Le volet **Sélectionner un compte de stockage** qui s’ouvre à droite répertorie l’ensemble de comptes de stockage pris en charge qui ont été détectés. Ceux-ci sont associés à ce coffre ou présents dans la même région que le coffre, mais pas encore associés à un coffre Recovery Services.

1. Dans la liste des comptes de stockage détectés, sélectionnez un compte, puis **OK**.

   ![Sélectionner parmi les comptes de stockage détectés](./media/backup-afs/select-discovered-storage-account.png)

1. L’étape suivante consiste à sélectionner les partages de fichiers que vous souhaitez sauvegarder. Cliquez sur le bouton **Ajouter** dans la section **Partages de fichiers à sauvegarder**.

   ![Sélectionner le partage de fichiers à sauvegarder](./media/backup-afs/select-file-shares-to-back-up.png)

1. Le volet contextuel **Sélectionner les partages de fichiers** s’ouvre à droite. Azure recherche dans le compte de stockage les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et ne les voyez pas dans la liste, patientez un peu jusqu’à ce qu’ils s’affichent.

1. Dans la liste **Sélectionner les partages de fichiers**, sélectionnez un ou plusieurs partages de fichiers à sauvegarder. Sélectionnez **OK**.

   ![Sélectionner les partages de fichiers](./media/backup-afs/select-file-shares.png)

1. Pour choisir une stratégie de sauvegarde pour votre partage de fichiers, vous avez trois options :

   * Choisissez la stratégie par défaut.<br>
   Cette option vous permet d’activer des sauvegardes quotidiennes qui seront conservées pendant 30 jours. Si vous n’avez pas de stratégie de sauvegarde existante dans le coffre, le volet de sauvegarde s’ouvre avec les paramètres de stratégie par défaut. Si vous souhaitez choisir les paramètres par défaut, vous pouvez cliquer directement sur **Activer la sauvegarde**.

   * Créer une nouvelle stratégie <br>

      1. Pour créer une nouvelle stratégie de sauvegarde pour votre partage de fichiers, cliquez sur le texte du lien sous la liste déroulante dans la section **Stratégie de sauvegarde**.<br>

         ![Créez une stratégie.](./media/backup-afs/create-new-policy.png)

      1. Le volet contextuel **Stratégie de sauvegarde** s’ouvre à droite. Spécifiez un nom de stratégie dans la zone de texte et choisissez la période de rétention en fonction de vos besoins. Seule l’option de rétention quotidienne est activée par défaut. Si vous souhaitez une rétention hebdomadaire, mensuelle ou annuelle, activez la case à cocher correspondante et indiquez la valeur de rétention souhaitée.

      1. Après avoir spécifié les valeurs de rétention et un nom de stratégie valide, cliquez sur OK.<br>

         ![Fournir les valeurs de noms de stratégie et de rétention](./media/backup-afs/policy-name.png)

   * Choisir l’une des stratégies de sauvegarde existantes <br>

   Pour choisir l’une des stratégies de sauvegarde existantes pour la configuration de la protection, sélectionnez la stratégie de votre choix dans la liste déroulante **Stratégie de sauvegarde**.<br>

   ![Choisir une stratégie existante](./media/backup-afs/choose-existing-policy.png)

1. Cliquez sur **Activer la sauvegarde** pour démarrer la protection du partage de fichiers.

   ![Choisir Activer la sauvegarde](./media/backup-afs/enable-backup.png)

Une fois que vous avez défini une stratégie de sauvegarde, un instantané des partages de fichiers est pris à l’heure planifiée. Le point de récupération est également conservé pendant la période choisie.

>[!NOTE]
>Sauvegarde Azure prend désormais en charge les stratégies de conservation quotidienne, hebdomadaire, mensuelle et annuelle pour la sauvegarde des fichiers partagés Azure.

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

>[!NOTE]
>Sauvegarde Azure verrouille le compte de stockage lorsque vous configurez la protection pour tout partage de fichiers dans le compte correspondant. Cette méthode offre une protection contre la suppression accidentelle d'un compte de stockage avec des partages de fichiers sauvegardés.

## <a name="best-practices"></a>Meilleures pratiques

* Ne supprimez pas d’instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration.

* Ne supprimez pas le verrou effectué par Sauvegarde Azure au niveau du compte de stockage. Si vous supprimez le verrou, votre compte de stockage risque d’être supprimé accidentellement et, dans ce cas, vous perdrez vos instantanés ou vos sauvegardes.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

* [Restaurer des partages de fichiers Azure](restore-afs.md)
* [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)

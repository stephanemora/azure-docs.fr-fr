---
title: Sauvegarder et restaurer des partages de fichiers Azure
description: Cet article explique comment sauvegarder et restaurer vos partages de fichiers Azure et explique les tâches de gestion.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 59dd46c42cdf94f06709d95f68cfb8d8693b9cce
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173091"
---
# <a name="back-up-and-restore-azure-file-shares"></a>Sauvegarder et restaurer des partages de fichiers Azure

Cet article explique comment utiliser le portail Azure pour sauvegarder et restaurer des [partages de fichiers Azure](../storage/files/storage-files-introduction.md).

Dans ce guide, vous apprendrez comment :
> [!div class="checklist"]
>
> * Configurer un coffre Recovery Services pour sauvegarder des fichiers Azure
> * Exécuter un travail de sauvegarde à la demande pour créer un point de restauration
> * Restaurer un ou plusieurs fichiers à partir d’un point de restauration
> * Gérer les travaux de sauvegarde
> * Arrêter la protection sur des fichiers Azure
> * Supprimer vos données de sauvegarde

## <a name="prerequisites"></a>Prérequis

Avant de sauvegarder un partage de fichiers Azure, assurez-vous qu’il est présent dans l’un des [types de compte de stockage pris en charge](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Après avoir vérifié ce point, vous pouvez protéger vos partages de fichiers.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitations pour la sauvegarde de partage de fichiers Azure en préversion

La sauvegarde des partages de fichiers Azure est disponible en préversion. Les partages de fichiers Azure dans les comptes de stockage v1 et v2 universels sont pris en charge. Les scénarios de sauvegarde suivants ne sont pas pris en charge pour les partages de fichiers Azure :

* La prise en charge de la sauvegarde des partages de fichiers Azure dans les comptes de stockage avec réplication de [stockage redondant interzone](../storage/common/storage-redundancy-zrs.md) (ZRS) est actuellement limitée à [ces régions](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares).
* Il n’y a aucune interface CLI disponible pour la protection d’Azure Files à l’aide de Sauvegarde Azure.
* La sauvegarde Azure prend actuellement en charge la configuration de sauvegardes planifiées une fois par jour des partages de fichiers Azure.
* Vous pouvez effectuer une seule sauvegarde planifiée par jour.
* Vous pouvez effectuer quatre sauvegardes à la demande par jour maximum.
* Utilisez les [verrous de ressources](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) sur le compte de stockage pour empêcher la suppression accidentelle des sauvegardes de votre coffre Recovery Services.
* Ne supprimez pas les instantanés créés par Sauvegarde Azure. La suppression d’instantanés peut provoquer une perte de points de récupération et/ou des échecs de restauration.
* Ne supprimez pas les partages de fichiers qui sont protégés par Sauvegarde Azure. La solution actuelle supprimera tous les instantanés pris par Sauvegarde Azure une fois que le partage de fichiers aura été supprimé. Tous les points de restauration seront donc perdus.

## <a name="configuring-backup-for-an-azure-file-share"></a>Configuration de la sauvegarde d’un partage de fichiers Azure

Cet article suppose que vous ayez déjà établi un partage de fichiers Azure. Pour sauvegarder votre partage de fichiers Azure :

1. Créez un coffre Recovery Services dans la même région que votre partage de fichiers. Si vous disposez déjà d’un coffre, ouvrez la page de vue d’ensemble de votre coffre et cliquez sur **Sauvegarde**.

    ![Choisir le partage de fichiers Azure comme objectif de sauvegarde](./media/backup-file-shares/overview-backup-page.png)

2. Dans le menu de l’**objectif de sauvegarde**, à partir de **Que voulez-vous sauvegarder ?** , choisissez le partage de fichiers Azure.

    ![Choisir le partage de fichiers Azure comme objectif de sauvegarde](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Cliquez sur **Sauvegarde** pour configurer le partage de fichiers Azure pour votre coffre Recovery Services.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/set-backup-goal.png)

    Une fois le coffre associé au partage de fichiers Azure, le menu de sauvegarde s’ouvre et vous demande de sélectionner un compte de stockage. Le menu affiche tous les comptes de stockage pris en charge dans la région où se trouve votre coffre, et qui ne sont pas déjà associés à un coffre Recovery Services.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/list-of-storage-accounts.png)

4. Dans la liste Comptes de stockage, sélectionnez un compte et cliquez sur **OK**. Azure recherche le compte de stockage pour les partages de fichiers qui peuvent être sauvegardés. Si vous avez récemment ajouté vos partages de fichiers et que vous ne les voyez pas dans la liste, laissez un peu de temps pour qu’ils s’affichent.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/discover-file-shares.png)

5. À partir de la liste **Partages de fichiers**, sélectionnez un ou plusieurs des partages de fichiers que vous souhaitez sauvegarder, puis cliquez sur **OK**.

6. Après avoir choisi vos partages de fichiers, le menu de sauvegarde bascule vers la **stratégie de sauvegarde**. Dans ce menu, sélectionnez une stratégie de sauvegarde existante, ou bien créez-en une, puis cliquez sur **Activer la sauvegarde**.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/apply-backup-policy.png)

    Une fois la stratégie de sauvegarde établie, un instantané des partages de fichiers sera effectué à l’heure planifiée, et le point de récupération est conservé pour la période définie.

## <a name="create-an-on-demand-backup"></a>Créer une sauvegarde à la demande

Vous souhaiterez parfois générer un instantané de sauvegarde ou un point de récupération en dehors des heures planifiées dans la stratégie de sauvegarde. Le moment suivant la configuration de la stratégie de sauvegarde est un temps commun pour générer une sauvegarde à la demande. En fonction du calendrier dans la stratégie de sauvegarde, des heures ou des jours peuvent s’écouler avant qu’un instantané soit généré. Pour protéger vos données en attendant que la stratégie de sauvegarde génère un instantané, effectuez une sauvegarde à la demande. La création d’une sauvegarde à la demande est souvent nécessaire avant d’apporter des modifications prévues à vos partages de fichiers.

### <a name="to-create-an-on-demand-backup"></a>Pour créer une sauvegarde à la demande

1. Ouvrez le coffre Recovery Services contenant les points de récupération du partage de fichiers, puis cliquez sur **Éléments de sauvegarde**. La liste des types d’éléments de sauvegarde s’affiche.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/list-of-backup-items.png)

2. Dans la liste, sélectionnez **Stockage Azure (fichiers Azure)** . La liste des partages de fichiers Azure s’affiche.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers désiré. Les détails associés à **Élément de sauvegarde** s’affichent. Dans le menu **Élément de sauvegarde**, cliquez sur **Sauvegarder maintenant**. Comme il s’agit d’un travail de sauvegarde à la demande, il n’existe aucune stratégie de rétention associée au point de récupération.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/backup-item-menu.png)

4. La boîte de dialogue **Sauvegarder maintenant** s’ouvre. Spécifiez le dernier jour que vous souhaitez conserver dans le point de récupération.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/backup-now-menu.png)

5. Cliquez sur **OK** pour confirmer la tâche de sauvegarde à la demande.

## <a name="restore-from-backup-of-azure-file-share"></a>Restauration à partir d’une sauvegarde de partage de fichiers Azure

Si vous devez restaurer un partage de fichiers entier ou des fichiers ou dossiers individuels à partir d’un point de restauration, accédez à l’élément de sauvegarde, comme détaillé dans la section précédente. Choisissez **Restaurer un partage** pour restaurer un partage de fichiers entier depuis un moment précis. Dans la liste des points de restauration qui s’affichent, sélectionnez-en un qui est en mesure de remplacer votre partage de fichiers actuel ou bien restaurez-le dans un autre partage de fichier de la même région.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Restauration de fichiers ou dossiers individuels à partir d’une sauvegarde de partages de fichiers Azure

Azure Backup offre la possibilité de parcourir un point de restauration au sein du portail Azure. Pour restaurer un fichier ou un dossier de votre choix, cliquez sur Récupération de fichiers à partir de la page Élément de sauvegarde et faites votre choix dans la liste des points de restauration. Sélectionnez la destination de récupération, puis cliquez sur **Sélectionner un fichier** pour rechercher le point de restauration. Sélectionnez le fichier ou le dossier de votre choix puis **Restaurer**.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Gérer les sauvegardes de partage de fichiers Azure

Vous pouvez exécuter plusieurs tâches de gestion pour les sauvegardes de partage de fichiers sur la page **Travaux de sauvegarde**, y compris :

* [Surveiller des travaux](backup-azure-files.md#monitor-jobs)
* [Créer une nouvelle stratégie](backup-azure-files.md#create-a-new-policy)
* [Arrêter la protection sur un partage de fichiers](backup-azure-files.md#stop-protecting-an-azure-file-share)
* [Reprendre la protection sur un partage de fichiers](backup-azure-files.md#resume-protection-for-azure-file-share)
* [Supprimer des données de sauvegarde](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Surveiller des travaux

Vous pouvez surveiller la progression de tous les travaux sur la page **Travaux de sauvegarde**.

Pour ouvrir la page **Travaux de sauvegarde** :

* Ouvrez le coffre Recovery Services que vous souhaitez analyser puis, dans le menu du coffre Recovery Services, cliquez sur **Travaux** puis sur **Travaux de sauvegarde**.

   ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/open-backup-jobs.png)

    La liste des travaux de sauvegarde et leur état s’affichent.

    ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Créer une nouvelle stratégie

Vous pouvez créer une nouvelle stratégie de sauvegarde des partages de fichiers Azure à partir des **Stratégies de sauvegarde** du coffre Recovery Services. Toutes les stratégies créées lorsque vous configurez la sauvegarde pour des partages de fichiers s’affichent avec le type de stratégie en tant que partage de fichiers Azure.

Pour afficher les stratégies de sauvegarde existantes :

* Ouvrez le coffre Recovery Services désiré puis, dans le menu du coffre Recovery Services, cliquez sur **Stratégies de sauvegarde**. Toutes les stratégies de sauvegarde sont répertoriées.

   ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/list-of-backup-policies.png)

Pour créer une nouvelle stratégie de sauvegarde :

1. Dans le menu du coffre Recovery Services, cliquez sur **Stratégies de sauvegarde**.
2. Dans la liste des stratégies de sauvegarde, cliquez sur **Ajouter**.

   ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/new-backup-policy.png)

3. Dans le menu **Ajouter**, sélectionnez **Partage de fichiers Azure**. Le menu Stratégie de sauvegarde pour le partage de fichiers Azure s’ouvre. Indiquez le nom de la stratégie, la fréquence de sauvegarde et la durée de rétention des points de récupération. Lorsque vous avez défini la stratégie, cliquez sur OK.

   ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Arrêt de la protection d’un partage de fichiers Azure

Si vous décidez d’arrêter la protection d’un partage de fichiers Azure, vous devrez indiquer si vous souhaitez conserver les points de récupération. Il existe deux façons de suspendre la protection des partages de fichiers Azure :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération, ou
* Arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération

Le fait de laisser des points de récupération dans le stockage peut avoir un coût, étant donné que les instantanés sous-jacents créés par Azure Backup seront conservés. Mais elle a l’avantage de vous permettre de restaurer ultérieurement le partage de fichiers, si vous le souhaitez. Pour plus d’informations sur les coûts de conservation des points de récupération, consultez la tarification. Si vous choisissez de supprimer tous les points de récupération, vous ne pourrez pas restaurer le partage de fichiers.

Pour arrêter la protection d’un partage de fichiers Azure :

1. Ouvrez le coffre Recovery Services contenant les points de récupération du partage de fichiers, puis cliquez sur **Éléments de sauvegarde**. La liste des types d’éléments de sauvegarde s’affiche.

   ![Cliquez sur Sauvegarde pour associer le partage de fichiers Azure au coffre](./media/backup-file-shares/list-of-backup-items.png)

2. Dans la liste **Type de gestion de sauvegarde**, sélectionnez **Stockage Azure (fichiers Azure)** . La liste des éléments de sauvegarde pour (Stockage Azure [fichiers Azure]) s’affiche.

   ![Cliquez sur l’élément pour ouvrir un menu supplémentaire](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Dans la liste des éléments de sauvegarde (stockage Azure [fichiers Azure]), sélectionnez l’élément de sauvegarde que vous souhaitez arrêter.

4. Dans les éléments du partage de fichiers Azure, cliquez sur le menu **Plus**, puis sélectionnez **Arrêter la sauvegarde**.

   ![Cliquez sur l’élément pour ouvrir un menu supplémentaire](./media/backup-file-shares/stop-backup.png)

5. Dans le menu Arrêter la sauvegarde, choisissez de **Conserver** ou de **Supprimer les données de sauvegarde** et cliquez sur **Arrêter la sauvegarde**.

   ![Cliquez sur l’élément pour ouvrir un menu supplémentaire](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Reprendre la protection d’un partage de fichiers Azure

Si vous avez choisi l’option Conserver les données de sauvegarde au moment de l’arrêt de la protection du partage de fichiers, vous avez la possibilité de restaurer la protection. Si vous avez choisi l’option **Supprimer les données de sauvegarde**, vous ne pourrez pas restaurer la protection du partage de fichiers.

Pour reprendre la protection du partage de fichiers, accédez à l’élément de sauvegarde, puis cliquez sur Reprendre la sauvegarde. La stratégie de sauvegarde s’ouvre et vous pouvez choisir la stratégie de votre choix pour reprendre la sauvegarde.

   ![Sélectionner le travail que vous souhaitez surveiller](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Supprimer les données de sauvegarde

Vous pouvez supprimer la sauvegarde d’un partage de fichiers lors du travail d’arrêt de la sauvegarde, ou à tout moment après avoir arrêté la protection. Il peut même être préférable d’attendre plusieurs jours ou semaines avant de supprimer les points de récupération. Contrairement à la restauration des points de récupération, lors de la suppression des données de sauvegarde, vous ne pouvez pas choisir des points de récupération spécifiques à supprimer. Si vous choisissez de supprimer vos données de sauvegarde, vous supprimerez tous les points de récupération associés à l’élément.

La procédure suivante suppose que le travail de sauvegarde de la machine virtuelle a été arrêté. Les options Reprendre la sauvegarde et Supprimer les données de sauvegarde sont accessibles dans le tableau de bord de l’élément de sauvegarde seulement lorsque le travail de sauvegarde a été arrêté. Cliquez sur Supprimer les données de sauvegarde et tapez le nom du partage de fichiers pour confirmer la suppression. Vous pouvez, si vous le souhaitez, indiquer une Raison pour la suppression ou un Commentaire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les partages de fichiers Azure, consultez les références suivantes

* [Questions sur la sauvegarde des fichiers Azure](backup-azure-files-faq.md)
* [Résoudre les problèmes de sauvegarde des fichiers Azure](troubleshoot-azure-files.md)

---
title: Restaurer des partages de fichiers Azure
description: Découvrez comment utiliser le portail Azure pour restaurer un partage de fichiers entier ou des fichiers spécifiques à partir d’un point de restauration créé par le service Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294843"
---
# <a name="restore-azure-file-shares"></a>Restaurer des partages de fichiers Azure

Cet article explique comment restaurer l’intégralité d’un partage de fichiers ou des fichiers spécifiques à partir d’un point de restauration créé par le service de [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview) à l’aide d’Azure CLI.

Dans ce guide, vous allez apprendre à :

* Restaurer un partage de fichiers Azure complet
* Restaurer des fichiers ou des dossiers individuels
* Suivre le statut de l’opération de restauration

## <a name="steps-to-perform-restore"></a>Étapes d’exécution de la restauration

### <a name="select-the-file-share-to-restore"></a>Sélectionner le partage de fichiers à restaurer

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le coffre Recovery Services que vous avez utilisé pour configurer la sauvegarde de votre partage de fichiers.

2. Cliquez sur **Éléments sauvegardés** dans la section **Éléments protégés** du panneau **Vue d’ensemble**.

    ![Cliquez sur éléments de sauvegarde](./media/restore-afs/backup-items.png)

3. Une fois que vous avez cliqué sur **Éléments de sauvegarde**, un nouveau panneau répertoriant tous les types de gestion des sauvegardes s’affiche à côté du panneau **Vue d’ensemble** comme indiqué ci-dessous :

    ![Types de gestion des sauvegardes](./media/restore-afs/backup-management.png)

4. Dans **Éléments de sauvegarde**, sous **Type de gestion de sauvegarde**, sélectionnez **Sauvegarde Azure (Azure Files)** . Vous verrez une liste de tous les partages de fichiers et de leur compte de stockage correspondant sauvegardés avec ce coffre.

    ![Liste de tous les partages de fichiers](./media/restore-afs/file-shares.png)

5. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers souhaité pour lequel vous souhaitez effectuer l’opération de restauration.

### <a name="full-share-recovery"></a>Récupération complète du partage

Vous pouvez utiliser cette option de restauration pour restaurer l’intégralité du partage de fichiers dans l’emplacement d’origine ou dans un autre emplacement.

1. Sélectionnez l’option **Restaurer le partage** dans le panneau **Élément de sauvegarde** qui s’affiche après avoir sélectionné le partage de fichiers à restaurer à l’étape 5 de la section [Sélectionner le partage de fichiers à restaurer](#select-the-file-share-to-restore).

   ![Sélectionner Restaurer le partage](./media/restore-afs/restore-share.png)

2. Une fois que vous cliquez sur **Restaurer le partage**, le panneau **Restaurer** s’ouvre avec un menu de **point de restauration** affichant la liste des points de restauration disponibles pour le partage de fichiers sélectionné.

3. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis cliquez sur **OK**.

    ![Sélectionner un point de restauration](./media/restore-afs/restore-point.png)

4. Une fois que vous avez cliqué sur OK, le menu restaurer le panneau passe à **Emplacement de restauration**. Dans **Emplacement de restauration**, indiquez où (ou comment) restaurer les données. Vous pouvez choisir l'une des deux options suivantes :

    * **Emplacement d’origine** : Restaurez le partage de fichiers complet au même emplacement que la source d’origine.
    * **Autre emplacement** : Restaurez le partage de fichiers complet à un autre emplacement et conservez le partage de fichiers d’origine **tel quel**.

#### <a name="restore-to-original-location"></a>Restaurer à l’emplacement d’origine

1. Choisissez **Emplacement d’origine** comme **Destination de récupération** et indiquez si vous souhaitez ignorer ou remplacer en cas de conflit. Cliquez sur **OK** après avoir effectué la sélection appropriée.

    ![Choisir l’emplacement d’origine](./media/restore-afs/original-location.png)

2. Cliquez sur **Restaurer** pour démarrer l’opération de restauration.

    ![Cliquez sur restaurer pour démarrer](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Choisissez **Autre emplacement** comme destination de récupération.
2. Sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé, dans le menu déroulant du champ du **Compte de stockage** .
3. En fonction du compte de stockage que vous avez sélectionné à l’étape 2, le **Sélectionner le partage de fichiers** du menu déroulant affiche la liste des partages de fichiers présents dans le compte de stockage sélectionné. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
4. Dans le champ **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
5. Indiquez si les conflits doivent être ignorés ou remplacés.
6. Cliquez sur **OK** après avoir entré les valeurs appropriées dans tous les champs.

    ![Sélectionner un autre emplacement](./media/restore-afs/alternate-location.png)

7. Cliquez sur restaurer pour démarrer l’opération de restauration.

    ![Cliquez sur restaurer pour démarrer](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Récupération au niveau de l’élément

Vous pouvez utiliser cette option de restauration pour restaurer des fichiers ou dossiers individuels à l’emplacement d’origine ou dans un autre emplacement.

1. Sélectionnez l’option de **Récupération de fichiers** dans le panneau d’**Élément de sauvegarde** qui s’affiche après avoir sélectionné le partage de fichiers à restaurer à l’étape 5 de la section [Sélectionner le partage de fichiers à restaurer](#select-the-file-share-to-restore).

    ![Sélectionner la récupération de fichier](./media/restore-afs/file-recovery.png)

2. Une fois que vous cliquez sur **Récupération de fichiers**, le panneau **Restaurer** s’ouvre avec un menu de **Point de restauration** affichant la liste des points de restauration disponibles pour le partage de fichiers sélectionné.

3. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis cliquez sur **OK**.

    ![Sélectionner un point de restauration](./media/restore-afs/restore-point.png)

4. Une fois que vous avez cliqué sur OK, le menu restaurer le panneau passe à **Emplacement de restauration**. Dans **Emplacement de restauration**, indiquez où (ou comment) restaurer les données. Vous pouvez choisir l'une des deux options suivantes :

    * **Emplacement d’origine** : Restaurez les fichiers/dossiers sélectionnés dans le même partage de fichiers que la source d’origine.
    * **Autre emplacement** : Restaurez les fichiers ou dossiers sélectionnés à un autre emplacement et conservez le contenu du partage de fichiers d’origine **tel quel**.

#### <a name="restore-to-original-location"></a>Restaurer dans l’emplacement d’origine

1. Choisissez **Emplacement d’origine** comme **Destination de récupération** et indiquez si vous souhaitez ignorer ou remplacer en cas de conflit.

    ![Emplacement d’origine pour la récupération au niveau de l’élément](./media/restore-afs/original-location-item-level.png)

2. Cliquez sur **Sélectionnez fichier** pour choisir les fichiers ou dossiers à restaurer.

    ![Cliquez sur Sélectionner un fichier](./media/restore-afs/select-file.png)

3. Lorsque vous cliquez **Sélectionnez fichier**, un panneau partage de fichiers, affichant le contenu du point de récupération de partage de fichiers que vous avez sélectionné pour la restauration, s’affiche.

4. Cochez la case correspondant au fichier/dossier que vous souhaitez restaurer, puis cliquez sur **Sélectionnez**.

    ![Sélectionner un fichier ou un dossier](./media/restore-afs/select-file-folder.png)

5. Répétez les étapes 2 à 4 pour sélectionner plusieurs fichiers ou dossiers à restaurer.
6. Après avoir sélectionné tous les éléments que vous souhaitez restaurer, cliquez sur **OK**.

    ![Après avoir sélectionné tous les éléments à restaurer, cliquez sur OK](./media/restore-afs/after-selecting-items.png)

7. Cliquez sur restaurer pour démarrer l’opération de restauration.

    ![Cliquez sur restaurer pour démarrer](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Choisissez **Autre emplacement** comme destination de récupération.
2. Sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé, dans le menu déroulant du champ du **Compte de stockage** .
3. En fonction du compte de stockage que vous avez sélectionné à l’étape 2, le **Sélectionner le menu déroulant** le partage de fichiers affiche la liste des partages de fichiers présents dans le compte de stockage sélectionné. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
4. Dans le champ **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
5. Indiquez si les conflits doivent être ignorés ou remplacés.
6. Cliquez sur **Sélectionnez fichier** pour choisir les fichiers ou dossiers à restaurer.

    ![Sélectionner les éléments à restaurer à un autre emplacement](./media/restore-afs/restore-to-alternate-location.png)

7. Lorsque vous cliquez **Sélectionnez fichier**, un panneau partage de fichiers, affichant le contenu du point de récupération de partage de fichiers que vous avez sélectionné pour la restauration, s’affiche.
8. Cochez la case correspondant au fichier ou au dossier que vous souhaitez restaurer, puis cliquez sur **Sélectionnez**.

    ![Sélectionner la destination de récupération](./media/restore-afs/recovery-destination.png)

9. Répétez les étapes 6 à 8 pour sélectionner plusieurs fichiers ou dossiers pour la restauration.
10. Après avoir sélectionné tous les éléments que vous souhaitez restaurer, cliquez sur **OK**.

    [Cliquez sur OK après avoir sélectionné tous les fichiers](./media/restore-afs/after-selecting-all-items.png)

11. Cliquez sur **Restaurer** pour démarrer l’opération de restauration.

## <a name="track-restore-operation"></a>Suivre l’opération de restauration

Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée un travail à des fins de suivi. Sauvegarde Azure affiche des notifications sur le travail dans le portail. Pour afficher les opérations du travail, cliquez sur le lien hypertexte de notifications.

![Cliquez sur le lien hypertexte de notifications](./media/restore-afs/notifications-link.png)

Vous pouvez également surveiller la progression de la restauration à partir du coffre Recovery Services. Pour vérifier l’état de l’opération de restauration, procédez comme suit :

1. Ouvrez le coffre Recovery Services à partir duquel vous avez déclenché l’opération de restauration.
2. Cliquez sur **Tâches de sauvegarde** sous la **Section de surveillance** du panneau **Vue d’ensemble** pour afficher l’état des opérations exécutées sur différentes charges de travail.

    ![Cliquez sur travaux de sauvegarde](./media/restore-afs/backup-jobs.png)

3. Cliquez sur le nom de la charge de travail correspondant à votre partage de fichiers afin d’afficher plus de détails sur l’opération de restauration, comme les données transférées, le nombre de fichiers restaurés, etc.

    ![Voir les détails restaurés](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md)

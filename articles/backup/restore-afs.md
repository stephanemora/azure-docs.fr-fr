---
title: Restaurer des partages de fichiers Azure
description: Découvrez comment utiliser le portail Azure pour restaurer un partage de fichiers entier ou des fichiers spécifiques à partir d’un point de restauration créé par le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586857"
---
# <a name="restore-azure-file-shares"></a>Restaurer des partages de fichiers Azure

Cet article explique comment utiliser le portail Azure pour restaurer l’intégralité d’un partage de fichiers ou des fichiers spécifiques à partir d’un point de restauration créé par le service [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview).

Dans cet article, vous allez apprendre à :

* restaurer un partage de fichiers Azure complet ;
* restaurer des fichiers ou dossiers individuels ;
* suivre le statut de l’opération de restauration.

## <a name="steps-to-perform-a-restore-operation"></a>Procédure d’exécution d’une opération de restauration

Pour exécuter une opération de restauration, procédez comme suit.

### <a name="select-the-file-share-to-restore"></a>Sélectionner le partage de fichiers à restaurer

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le coffre Recovery Services que vous avez utilisé pour configurer la sauvegarde de votre partage de fichiers.

1. Dans le volet Vue d’ensemble, dans la section **Éléments protégés**, sélectionnez **Éléments de sauvegarde**.

    ![Sélectionner les éléments de sauvegarde](./media/restore-afs/backup-items.png)

1. Une fois les **Éléments de sauvegarde** sélectionnés, un nouveau volet répertoriant tous les Types de gestion des sauvegardes s’ouvre en regard du volet Vue d’ensemble.

    ![Types de gestion des sauvegardes](./media/restore-afs/backup-management.png)

1. Dans le volet **Éléments de sauvegarde**, sous **Type de gestion de sauvegarde**, sélectionnez **Sauvegarde Azure (Azure Files)** . Vous voyez s’afficher une liste de tous les partages de fichiers et les comptes de stockage correspondants sauvegardés à l’aide de ce coffre.

    ![Liste de tous les partages de fichiers](./media/restore-afs/file-shares.png)

1. Dans la liste des partages de fichiers Azure, sélectionnez le partage de fichiers pour lequel vous souhaitez effectuer l’opération de restauration.

### <a name="full-share-recovery"></a>Récupération complète du partage

Vous pouvez utiliser cette option de restauration pour restaurer l’intégralité du partage de fichiers dans l’emplacement d’origine ou dans un autre emplacement.

1. Sélectionnez l’option **Restaurer le partage** dans le volet **Élément de sauvegarde** qui s’affiche lorsque vous sélectionnez le partage de fichiers à restaurer à l’étape 5 de la section [Sélectionner le partage de fichiers à restaurer](#select-the-file-share-to-restore).

   ![Sélectionner Restaurer le partage](./media/restore-afs/restore-share.png)

1. Après que vous avez sélectionné **Restaurer le partage**, le volet **Restaurer** s’ouvre sur un menu **Point de restauration** affichant la liste des points de restauration disponibles pour le partage de fichiers sélectionné.

1. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis sélectionnez **OK**.

    ![Sélectionner le point de restauration](./media/restore-afs/restore-point.png)

1. Une fois que vous avez sélectionné **OK**, le menu du volet **Restaurer** bascule vers **Emplacement de restauration**. Dans **Emplacement de restauration**, indiquez où (ou comment) restaurer les données. Sélectionnez l’une des deux options suivantes :

    * **Emplacement d’origine** : Restaurez le partage de fichiers complet au même emplacement que la source d’origine.
    * **Autre emplacement** : Restaurez le partage de fichiers complet à un autre emplacement et conservez le partage de fichiers d’origine tel quel.

#### <a name="restore-to-the-original-location"></a>Restaurer à l’emplacement d’origine

1. Sélectionnez **Emplacement d’origine** comme **Destination de récupération** et indiquez si vous souhaitez ignorer ou remplacer en cas de conflit. Après que vous avez effectué la sélection appropriée, sélectionnez **OK**.

    ![Sélectionner l’emplacement d’origine](./media/restore-afs/original-location.png)

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

    ![Sélectionner Restaurer pour démarrer](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Sélectionnez **Autre emplacement** comme **Destination de récupération**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. La liste déroulante **Sélectionner le partage de fichiers** affiche les partages de fichiers présents dans le compte de stockage que vous avez sélectionné à l’étape 2. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. Dans la zone **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
1. Indiquez si les conflits doivent être ignorés ou remplacés.
1. Après avoir entré les valeurs appropriées dans toutes les zones, sélectionnez **OK**.

    ![Sélectionner un autre emplacement](./media/restore-afs/alternate-location.png)

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

    ![Sélectionner Restaurer pour démarrer](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Récupération au niveau de l'élément

Vous pouvez utiliser cette option de restauration pour restaurer des fichiers ou dossiers individuels à l’emplacement d’origine ou dans un autre emplacement.

1. Sélectionnez l’option **Récupération de fichiers** dans le volet **Élément de sauvegarde** qui s’affiche lorsque vous sélectionnez le partage de fichiers à restaurer à l’étape 5 de la section [Sélectionner le partage de fichiers à restaurer](#select-the-file-share-to-restore).

    ![Sélectionner la récupération de fichier](./media/restore-afs/file-recovery.png)

1. Après que vous avez sélectionné **Récupération de fichiers**, le volet **Restaurer** s’ouvre sur un menu **Point de restauration** affichant la liste des points de restauration disponibles pour le partage de fichiers sélectionné.

1. Sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis sélectionnez **OK**.

    ![Sélectionner le point de restauration](./media/restore-afs/restore-point.png)

1. Une fois que vous avez sélectionné **OK**, le menu du volet Restaurer bascule vers **Emplacement de restauration**. Dans **Emplacement de restauration**, indiquez où (ou comment) restaurer les données. Sélectionnez l’une des deux options suivantes :

    * **Emplacement d’origine** : Restaurez les fichiers ou dossiers sélectionnés dans le même partage de fichiers que la source d’origine.
    * **Autre emplacement** : Restaurez les fichiers ou dossiers sélectionnés dans un autre emplacement et conservez le contenu du partage de fichiers d’origine tel quel.

#### <a name="restore-to-the-original-location"></a>Restaurer à l’emplacement d’origine

1. Sélectionnez **Emplacement d’origine** comme **Destination de récupération** et indiquez si vous souhaitez ignorer ou remplacer en cas de conflit.

    ![Emplacement d’origine pour la récupération au niveau de l’élément](./media/restore-afs/original-location-item-level.png)

1. Choisissez **Sélectionner un fichier** pour sélectionner les fichiers ou dossiers à restaurer.

    ![Choisir Sélectionner un fichier](./media/restore-afs/select-file.png)

1. Lorsque vous choisissez **Sélectionnez un fichier**, un volet de partage de fichiers affiche le contenu du point de récupération du partage de fichiers que vous avez sélectionné pour la restauration.

1. Activez la case à cocher correspondant au fichier ou au dossier que vous souhaitez restaurer, puis choisissez **Sélectionner**.

    ![Sélectionner un fichier ou un dossier](./media/restore-afs/select-file-folder.png)

1. Répétez les étapes 2 à 4 pour sélectionner plusieurs fichiers ou dossiers à restaurer.
1. Après avoir sélectionné tous les éléments à restaurer, sélectionnez **OK**.

    ![Après sélection de tous les éléments à restaurer, sélectionner OK](./media/restore-afs/after-selecting-items.png)

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

    ![Sélectionner Restaurer pour démarrer](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Sélectionnez **Autre emplacement** comme **Destination de récupération**.
1. Dans la liste déroulante **Compte de stockage**, sélectionnez le compte de stockage de destination dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. La liste déroulante **Sélectionner le partage de fichiers** affiche les partages de fichiers présents dans le compte de stockage que vous avez sélectionné à l’étape 2. Sélectionnez le partage de fichiers dans lequel vous souhaitez restaurer le contenu sauvegardé.
1. Dans la zone **Nom du dossier**, spécifiez le nom du dossier que vous souhaitez créer dans le partage de fichiers de destination avec le contenu restauré.
1. Indiquez si les conflits doivent être ignorés ou remplacés.
1. Choisissez **Sélectionner un fichier** pour sélectionner les fichiers ou dossiers à restaurer.

    ![Sélectionner les éléments à restaurer à un autre emplacement](./media/restore-afs/restore-to-alternate-location.png)

1. Après que vous avez choisi **Sélectionnez un fichier**, un volet de partage de fichiers affiche le contenu du point de récupération du partage de fichiers que vous avez sélectionné pour la restauration.
1. Activez la case à cocher correspondant au fichier ou au dossier que vous souhaitez restaurer, puis choisissez **Sélectionner**.

    ![Sélectionner la destination de récupération](./media/restore-afs/recovery-destination.png)

1. Répétez les étapes 6 à 8 pour sélectionner plusieurs fichiers à restaurer.
1. Après avoir sélectionné tous les éléments à restaurer, sélectionnez **OK**.

    ![Sélectionner OK après avoir sélectionné tous les fichiers](./media/restore-afs/after-selecting-all-items.png)

1. Sélectionnez **Restaurer** pour démarrer l’opération de restauration.

## <a name="track-a-restore-operation"></a>Suivre une opération de restauration

Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée un travail à des fins de suivi. Sauvegarde Azure affiche des notifications sur le travail dans le portail. Pour afficher les opérations du travail, sélectionner le lien hypertexte notifications.

![Sélectionner le lien hypertexte notifications](./media/restore-afs/notifications-link.png)

Vous pouvez également surveiller la progression de la restauration à partir du coffre Recovery Services :

1. Ouvrez le coffre Recovery Services à partir duquel vous avez déclenché l’opération de restauration.
1. Dans le volet Vue d’ensemble, dans la section **Surveillance** sélectionnez **Travaux de sauvegarde** pour afficher l’état des opérations en cours d’exécution sur différentes charges de travail.

    ![Sélectionner des travaux de sauvegarde](./media/restore-afs/backup-jobs.png)

1. Sélectionnez le nom de la charge de travail correspondant à votre partage de fichiers afin d’afficher plus de détails sur l’opération de restauration, par exemple, les **Données transférées** et le **Nombre de fichiers restaurés**.

    ![Voir les détails restaurés](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Étapes suivantes

* Apprendre à [Gérer les sauvegardes de partage de fichiers Azure](manage-afs-backup.md).

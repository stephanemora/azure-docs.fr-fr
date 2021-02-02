---
title: Restaurer tous les fichiers dans un volume à l’aide de MARS
description: Découvrez comment restaurer tous les fichiers d’un volume à l’aide de l’agent MARS.
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98612584"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>Restaurer tous les fichiers d’un volume à l’aide de l’agent MARS

Cet article explique comment restaurer tous les fichiers sauvegardés dans un volume entier à l’aide de l’Assistant Récupérer des données de l’agent Microsoft Azure Recovery Services (MARS). Vous pouvez :

- Restaurer tous les fichiers sauvegardés dans un volume sur l’ordinateur à partir duquel les sauvegardes ont été effectuées.
- Restaurer tous les fichiers sauvegardés dans un volume sur un autre ordinateur.

>[!TIP]
>L’option **Volume** récupère toutes les données sauvegardées dans un volume spécifié. Cette option offre des vitesses de transfert supérieures (40 Mbits/s maximum), et est recommandée pour la récupération de données de grande taille ou de volumes entiers.
>
>L’**option Fichiers et dossiers individuels** autorise un accès rapide aux données de point de récupération. Elle convient à la récupération de fichiers individuels et est recommandée lorsque la taille totale est inférieure à 80 Go. Elle offre des vitesses de transfert ou de copie de 6 Mbits/s maximum lors de la récupération.

## <a name="volume-level-restore-to-the-same-machine"></a>Restauration au niveau du volume sur le même ordinateur

Les étapes suivantes vous aideront à récupérer tous les fichiers sauvegardés dans un volume :

1. Ouvrez le composant logiciel enfichable **Sauvegarde Microsoft Azure**. Si vous ne savez pas où il a été installé, recherchez **Sauvegarde Microsoft Azure** sur l’ordinateur ou le serveur. L’application de bureau devrait apparaître dans les résultats de recherche.

1. Sélectionnez **Récupérer des données** pour lancer l’Assistant.

    ![Menu Récupérer des données](./media/restore-all-files-volume-mars/recover.png)

1. Dans la page **Prise en main**, pour restaurer les données sur le même serveur ou ordinateur, sélectionnez **Ce serveur (nom du serveur)**  > **Suivant**.

    ![Page Mise en route](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. Dans la page **Sélectionner le mode de récupération**, choisissez **Volume** > **Suivant**.

    ![Sélectionner le mode de récupération](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Dans la page **Sélectionner le volume et la date**, sélectionnez le volume que vous souhaitez restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Si plusieurs points de récupération sont disponibles pour une même date, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

     ![Sélectionnez un volume et une date](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Dans la page **Spécifier les options de récupération**, configurez le comportement de restauration.
    1. Choisissez la destination de récupération :
        - **Emplacement d’origine** : Restaurez les données sous le chemin d’accès d’origine.
        - **Un autre emplacement** : Indiquez un autre emplacement pour la restauration des données.
    1. Choisissez le comportement pour l’option **Lorsque les éléments de la sauvegarde figurent déjà dans la destination de récupération** :
        - **Créer des copies pour avoir les deux versions** : Si un fichier du même nom existe déjà, les données du point de récupération seront restaurées en tant que copie. La copie aura un préfixe de nom de fichier localisé utilisant l’heure locale du travail de restauration dans l’un des formats suivants :
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Remplacer les versions existantes avec les versions récupérées** : Si un fichier du même nom existe déjà, le contenu est remplacé par les données du point de récupération.
        - **Ne pas récupérer les éléments existant déjà sur la destination de récupération** : Si un fichier du même nom existe déjà, il est ignoré.
    1. **Activez les autorisations Restaurer la liste de contrôle d’accès (ACL) pour le fichier ou le dossier en cours de récupération** si le fichier doit être restauré avec les autorisations d’origine dans le point de récupération.
        ![Spécifier les options de récupération](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Vérifiez les détails de la récupération dans le volet **Confirmation** et sélectionnez **Récupérer**.

    ![Détails de la confirmation](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Sur la page **Progression de la récupération**, surveillez la progression de la tâche de récupération. L’Assistant peut également être fermé en toute sécurité, et l’opération de récupération se poursuit en arrière-plan. Vous pouvez réafficher la progression en double-cliquant sur la tâche de récupération dans le tableau de bord.

## <a name="volume-level-restore-to-an-alternate-machine"></a>Restauration au niveau du volume sur un autre ordinateur

Les étapes suivantes vous aideront à récupérer tous les fichiers sauvegardés dans un volume sur un autre ordinateur. Vous pouvez utiliser ces étapes pour récupérer des données à partir de Sauvegarde Azure si l’intégralité de votre serveur est perdue.

Ces étapes incluent la terminologie suivante :

- *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.
- *Ordinateur cible* : ordinateur sur lequel les données sont récupérées.
- *Exemple d’archivage* : coffre Recovery Services dans lequel l’ordinateur source et l’ordinateur cible sont enregistrés.

> [!NOTE]
> Il est impossible de restaurer les sauvegardes sur un ordinateur cible qui fonctionne avec une version antérieure du système d’exploitation. Par exemple, une sauvegarde effectuée à partir d’un ordinateur Windows 7 ne peut pas être restaurée sur un ordinateur sous Windows 7 (ou une version ultérieure). Il n’est pas possible de restaurer sur un ordinateur Windows 7 une sauvegarde effectuée à partir d’un ordinateur Windows 10.

1. Ouvrez le composant logiciel enfichable **Sauvegarde Microsoft Azure** sur l’ordinateur cible.

1. Vérifiez que l’ordinateur cible et l’ordinateur source sont inscrits auprès du même coffre Recovery Services.

1. Sélectionnez **Récupérer des données** pour ouvrir **l’Assistant Récupération de données**.

    ![Capture d’écran de Sauvegarde Azure, avec l’option Récupérer des données en surbrillance (restaurer sur une autre machine)](./media/backup-azure-restore-windows-server/recover.png)

1. Dans la page **Prise en main**, sélectionnez **Autre serveur**.

    ![Capture d’écran de la page Prise en main de l’Assistant Récupérer des données (restaurer sur une autre machine)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. Fournissez le fichier d’informations d’identification correspondant à l’exemple de coffre, puis cliquez sur **Suivant**.

    Si le fichier d’informations d’identification du coffre n’est pas valide (ou a expiré), [téléchargez un nouveau fichier d’informations d’identification de coffre à partir de l’exemple de coffre](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) dans le portail Azure. Une fois que vous avez fourni des informations d’identification de coffre valides, le nom du coffre de sauvegarde correspondant s’affiche.

1. Dans la page **Sélectionner un serveur de sauvegarde**, sélectionnez l’ordinateur source dans la liste des ordinateurs affichés et fournissez la phrase secrète. Sélectionnez ensuite **Suivant**.

    ![Capture d’écran de la page Sélectionner le serveur de sauvegarde de l’Assistant Récupérer des données (restaurer sur une autre machine)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. Dans la page **Sélectionner le mode de récupération**, choisissez **Volume** > **Suivant**.

    ![Sélectionner le mode de récupération](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. Dans la page **Sélectionner le volume et la date**, sélectionnez le volume que vous souhaitez restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Si plusieurs points de récupération sont disponibles pour une même date, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

     ![Sélectionnez un volume et une date](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. Dans la page **Spécifier les options de récupération**, configurez le comportement de restauration.
    1. Choisissez la destination de récupération :
        - **Emplacement d’origine** : Restaurez les données sous le chemin d’accès d’origine.
        - **Un autre emplacement** : Indiquez un autre emplacement pour la restauration des données.
    1. Choisissez le comportement pour l’option **Lorsque les éléments de la sauvegarde figurent déjà dans la destination de récupération** :
        - **Créer des copies pour avoir les deux versions** : Si un fichier du même nom existe déjà, les données du point de récupération seront restaurées en tant que copie. La copie aura un préfixe de nom de fichier localisé utilisant l’heure locale du travail de restauration dans l’un des formats suivants :
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **Remplacer les versions existantes avec les versions récupérées** : Si un fichier du même nom existe déjà, le contenu est remplacé par les données du point de récupération.
        - **Ne pas récupérer les éléments existant déjà sur la destination de récupération** : Si un fichier du même nom existe déjà, il est ignoré.
    1. **Activez les autorisations Restaurer la liste de contrôle d’accès (ACL) pour le fichier ou le dossier en cours de récupération** si le fichier doit être restauré avec les autorisations d’origine dans le point de récupération.
        ![Spécifier les options de récupération](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. Vérifiez les détails de la récupération dans le volet **Confirmation** et sélectionnez **Récupérer**.

    ![Détails de la confirmation](./media/restore-all-files-volume-mars/confirmation-details.png)

1. Sur la page **Progression de la récupération**, surveillez la progression de la tâche de récupération. L’Assistant peut également être fermé en toute sécurité, et l’opération de récupération se poursuit en arrière-plan. Vous pouvez réafficher la progression en double-cliquant sur la tâche de récupération dans le tableau de bord.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez restauré vos fichiers et vos dossiers, vous pouvez [gérer vos sauvegardes](backup-azure-manage-windows-server.md).
- Consultez les [questions courantes sur la sauvegarde des fichiers et des dossiers](backup-azure-file-folder-backup-faq.md).

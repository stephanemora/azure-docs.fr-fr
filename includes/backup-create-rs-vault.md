---
title: Fichier Include
description: Fichier Include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664925"
---
## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Un coffre Recovery Services est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre Recovery Services contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

Pour créer un archivage de Recovery Services :

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, sélectionnez **Tous les services**.

    ![Choisissez l’option Tous les services dans le menu principal](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Dans la boîte de dialogue Tous les services, tapez *Recovery Services*. Au fur et à mesure des caractères saisis, la liste des ressources est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

    ![Dans la boîte de dialogue Tous les services, tapez Recovery Services](./media/backup-create-rs-vault/all-services.png) <br/>

    La liste des coffres Recovery Services de l’abonnement s’affiche.
4. Dans le menu **Coffres Recovery Services**, sélectionnez **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    Le menu **Coffres Recovery Services** s’ouvre. Il vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Volet « Coffres Recovery Services »](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom qui contient au moins deux caractères, mais pas plus de 50. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
6. Dans **Abonnement**, choisissez l’abonnement que vous souhaitez utiliser. Si vous êtes membre d’un seul abonnement, le nom de celui-ci s’affichera. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
7. Dans **Groupe de ressources**, vous pouvez utiliser un groupe de ressources existant ou en créer un. Pour afficher la liste des groupes de ressources disponibles de votre abonnement, sélectionnez **Utiliser existant** et cliquez sur le menu déroulant. Pour créer un groupe de ressources, sélectionnez **Créer** et tapez le nom. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. Dans **Emplacement**, sélectionnez la région géographique du coffre. Si vous créez un coffre pour protéger des machines virtuelles, le coffre *doit* se trouver dans la même région que les machines virtuelles.

   > [!IMPORTANT]
   > Si vous ne savez pas où se trouve votre machine virtuelle, fermez la boîte de dialogue de création de coffres et accédez à la liste des machines virtuelles dans le portail. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’entre elles. Créez l’archivage dans le premier emplacement avant de passer à l'emplacement suivant. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et le service Sauvegarde Azure gèrent cela automatiquement.
   >
   >

9. Lorsque vous êtes prêt à créer le coffre Recovery Services, cliquez sur **Créer**.

    ![Liste des coffres de sauvegarde](./media/backup-create-rs-vault/click-create-button.png)

    La création de l’archivage de Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans la section Notifications (en haut à droite du portail). Une fois votre coffre créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez toujours pas votre coffre, cliquez sur **Actualiser**.

     ![Liste des coffres de sauvegarde](./media/backup-create-rs-vault/refresh-button.png)

---
title: 'Sauvegarde Azure : Créer des coffres Recovery Services'
description: Création des coffres Recovery Services qui stocke les sauvegardes et les points de récupération
services: backup
author: sogup
manager: vijayts
keywords: Coffre Recovery Services ; Sauvegarde des machines virtuelles Azure ; Restauration de machine virtuelle Azure ;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: b345f5e7f6f32d7fbb76d4f0227c8f4f925ff884
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400295"
---
# <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre Recovery Services contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

Pour créer un archivage de Recovery Services :

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).

2. Dans le menu de gauche, sélectionnez **Tous les services**.

    ![Sélectionner Tous les services](./media/backup-create-rs-vault/click-all-services.png)

3. Dans la boîte de dialogue **Tous les services**, entrez **Recovery Services**. Liste des filtres de ressources variant en fonction de votre entrée. Dans la liste des ressources, sélectionnez **Coffres Recovery Services**.

    ![Entrée et choisir Coffres Recovery Services](./media/backup-create-rs-vault/all-services.png)

    La liste des coffres Recovery Services de l’abonnement s’affiche.

4. Dans le tableau de bord **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Ajouter un coffre Recovery Services](./media/backup-create-rs-vault/add-button-create-vault.png)

    La boîte de dialogue **Coffre Recovery Services** s’ouvre. Attribuez des valeurs aux champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Configurer le coffre Recovery Services](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nom** : entrez un nom convivial pour identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union.
   - **Abonnement**: choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
   - **Groupe de ressources** : Utilisez un groupe de ressources existant ou créez-en un. Pour afficher la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la zone de liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour obtenir des informations complètes sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Emplacement** : sélectionnez la région géographique du coffre. Pour créer un coffre en vue de protéger des machines virtuelles, le coffre **doit** se trouver dans la même région que les machines virtuelles.

      > [!IMPORTANT]
      > Si vous ne connaissez pas avec certitude l’emplacement de vos machines virtuelles, fermez la boîte de dialogue. Accédez à la liste des machines virtuelles dans le portail. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’elles. Créez le coffre du premier emplacement avant de créer celui d’un autre emplacement. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et le service Azure Backup gèrent cela automatiquement.
      >
      >

5. Quand vous êtes prêt à créer le coffre Recovery Services, sélectionnez **Créer**.

    ![Créer le coffre Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    La création du coffre Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans la zone **Notifications** dans l’angle supérieur droit du portail. Une fois que le coffre est créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

     ![Actualiser la liste des coffres de sauvegarde](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Définir la redondance du stockage

Sauvegarde Azure gère automatiquement le stockage pour le coffre. Vous devez spécifier la manière dont ce stockage est répliqué.

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Sous le **paramètres** , cliquez sur **propriétés**.
2. Dans **propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **enregistrer**.

     ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Nous recommandons que si vous utilisez Azure comme un point de terminaison de stockage de sauvegarde principal, continuer à utiliser la valeur par défaut **géo-redondant** paramètre.
   - Sinon, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure.
   - En savoir plus sur [géo](../storage/common/storage-redundancy-grs.md) et [local](../storage/common/storage-redundancy-lrs.md) redondance.

> [!NOTE]
> Une fois que vous choisissez la **type de réplication de stockage** de **géo-redondant** à **localement redondant**, vous ne pouvez pas revenir en arrière.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur](backup-azure-recovery-services-vault-overview.md) coffres Recovery Services.
[En savoir plus sur](backup-azure-delete-vault.md) coffres supprimer les Services de récupération.

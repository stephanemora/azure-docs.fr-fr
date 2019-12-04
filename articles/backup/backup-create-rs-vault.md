---
title: Créer des coffres Recovery Services
description: Dans cet article, découvrez comment créer des coffres Recovery Services stockant les sauvegardes et points de récupération.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 144d8cdb870e12474dfc47784749b5f0e466f8bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273390"
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

   - **Nom** : entrez un nom convivial pour identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union.
   - **Abonnement**: choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
   - **Groupe de ressources** : Utilisez un groupe de ressources existant ou créez-en un. Pour afficher la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la zone de liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour obtenir des informations complètes sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Emplacement** : sélectionnez la région géographique du coffre. Pour créer un coffre en vue de protéger des machines virtuelles, le coffre **doit** se trouver dans la même région que les machines virtuelles.

      > [!IMPORTANT]
      > Si vous ne connaissez pas avec certitude l’emplacement de vos machines virtuelles, fermez la boîte de dialogue. Accédez à la liste des machines virtuelles dans le portail. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’elles. Créez le coffre du premier emplacement avant de créer celui d’un autre emplacement. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et le service Azure Backup gèrent cela automatiquement.
      >
      >

5. Quand vous êtes prêt à créer le coffre Recovery Services, sélectionnez **Créer**.

    ![Créer le coffre Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    La création du coffre Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans la zone **Notifications** dans l’angle supérieur droit du portail. Une fois que le coffre est créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

     ![Actualiser la liste des coffres de sauvegarde](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Définir la redondance du stockage

La Sauvegarde Azure gère automatiquement le stockage du coffre. Vous devez spécifier le mode de réplication de ce stockage.

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Dans la section **Paramètres**, cliquez sur **Propriétés**.
2. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.

3. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

     ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, nous vous recommandons de continuer à utiliser le paramètre **Géoredondant** par défaut.
   - Sinon, choisissez l’option **Localement redondant**, qui réduit les coûts de stockage Azure.
   - Informez-vous sur la redondance [géo](../storage/common/storage-redundancy-grs.md) et [locale](../storage/common/storage-redundancy-lrs.md).

> [!NOTE]
> Vous devez remplacer la valeur du paramètre **Type de réplication du stockage** (Localement redondant/Géoredondant) par un coffre Recovery Services avant de configurer les sauvegardes dans le coffre. Une fois que vous avez configuré la sauvegarde, l’option de modification est désactivée et vous ne pouvez pas modifier le paramètre **Type de réplication de stockage**.

## <a name="modifying-default-settings"></a>Modification des paramètres par défaut

Nous vous recommandons vivement de consulter les paramètres par défaut pour **type de réplication de stockage** et **Paramètres de sécurité** avant de configurer les sauvegardes dans le coffre. 
* **Le type de réplication de stockage** par défaut est défini sur **Géo-redondant**. Une fois que vous avez configuré la sauvegarde, l’option de modification est désactivée. Procédez [comme suit](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) pour évaluer et modifier les paramètres. 
* **La suppression réversible** par défaut est **activée** sur les coffres nouvellement créés pour protéger les données de sauvegarde des suppressions accidentelles ou malveillantes. Procédez [comme suit](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) pour évaluer et modifier les paramètres.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez](backup-azure-recovery-services-vault-overview.md) les coffres Recovery Services.
[Découvrez](backup-azure-delete-vault.md) comment supprimer les coffres Recovery Services.

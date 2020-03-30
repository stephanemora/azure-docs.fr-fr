---
title: Créer des coffres Recovery Services
description: Dans cet article, découvrez comment créer des coffres Recovery Services stockant les sauvegardes et points de récupération.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e722996f516d21445d8e0028df925ca44eb02bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295011"
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
   - **Abonnement** : choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
   - **Groupe de ressources** : utilisez un groupe de ressources existant ou créez-en un. Pour afficher la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la zone de liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour obtenir des informations complètes sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
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

## <a name="set-cross-region-restore"></a>Définir la restauration interrégion

Parmi les options de restauration, la fonction de restauration interrégion (CRR) vous permet de restaurer des machines virtuelles Azure dans une région secondaire, qui est une [région jumelée Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Cette option vous permet d’effectuer les opérations suivantes :

- effectuer des recherches dans le cadre d’un audit ou d’une condition de conformité
- restaurer la machine virtuelle ou son disque en cas de sinistre dans la région primaire.

Pour choisir cette fonctionnalité, sélectionnez **Activer la restauration interrégion** dans le panneau **Configuration de la sauvegarde**.

Dans le cadre de ce processus, les implications tarifaires sont liées au niveau de stockage.

>[!NOTE]
>Avant de commencer :
>
>- Examinez la [matrice de prise en charge](backup-support-matrix.md#cross-region-restore) pour obtenir la liste des types et des régions managés pris en charge.
>- La fonctionnalité de restauration interrégion (CRR) est actuellement disponible uniquement dans les régions suivantes : 
>    - Centre-USA Ouest
>    - USA Ouest 2
>    - États-Unis - partie centrale méridionale
>    - USA Est
>    - USA Est 2
>    - Centre-Nord des États-Unis
>    - Centre du Canada
>    - Est du Canada
>    - Australie Est
>    - Sud-Australie Est
>    - Inde centrale
>    - Inde Sud
>    - Japon Est
>    - OuJapon Est
>    - Asie Sud-Est
>    - Sud du Royaume-Uni
>    - Ouest du Royaume-Uni
>    - France Centre
>    - Centre de la Corée
>    - Corée du Sud
>- La CRR est une fonctionnalité d’abonnement au niveau du coffre pour tout coffre GRS (désactivé par défaut).
>- Utilisez la commande suivante afin d’intégrer votre abonnement pour cette fonctionnalité :<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Si vous êtes intégré à cette fonctionnalité pendant la préversion publique limitée, l’e-mail d’approbation de la validation inclut les détails de la stratégie de tarification.
>- Après l’inscription, il peut s’écouler jusqu’à 48 heures avant que les éléments de sauvegarde ne soient disponibles dans les régions secondaires.
>- Actuellement, la CRR est prise en charge uniquement pour Type de gestion des sauvegardes : Machine virtuelle Azure ARM (les machines virtuelles Azure classiques ne sont pas prises en charge).  Lorsque d’autres types de gestion prendront en charge la CRR, ils seront **automatiquement** enregistrés.

### <a name="configure-cross-region-restore"></a>Configurer la restauration interrégion

Un coffre créé avec la redondance GRS comprend l’option permettant de configurer la fonctionnalité de restauration interrégion. Chaque coffre GRS aura une bannière, laquelle fera le lien avec la documentation. Pour configurer la CRR pour le coffre, accédez au panneau Configuration de la sauvegarde, qui contient l’option permettant d’activer cette fonctionnalité.

 ![Bannière Configuration de la sauvegarde](./media/backup-azure-arm-restore-vms/banner.png)

1. À partir du portail, accédez à Coffre Recovery Services > Paramètres > Propriétés.
2. Cliquez sur **Activer la restauration interrégion dans ce coffre** pour activer la fonctionnalité.

   ![Avant de cliquer sur Activer la restauration interrégion dans ce coffre](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Après avoir cliqué sur Activer la restauration interrégion dans ce coffre](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Découvrez comment [afficher les éléments de sauvegarde dans la région secondaire](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Découvrez comment [restaurer dans la région secondaire](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Découvrez comment [surveiller les travaux de restauration de la région secondaire](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Modification des paramètres par défaut

Nous vous recommandons vivement de consulter les paramètres par défaut pour **type de réplication de stockage** et **Paramètres de sécurité** avant de configurer les sauvegardes dans le coffre.

- **Le type de réplication de stockage** par défaut est défini sur **Géo-redondant**. Une fois que vous avez configuré la sauvegarde, l’option de modification est désactivée. Procédez [comme suit](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) pour évaluer et modifier les paramètres.

- **La suppression réversible** par défaut est **activée** sur les coffres nouvellement créés pour protéger les données de sauvegarde des suppressions accidentelles ou malveillantes. Procédez [comme suit](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) pour évaluer et modifier les paramètres.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez](backup-azure-recovery-services-vault-overview.md) les coffres Recovery Services.
[Découvrez](backup-azure-delete-vault.md) comment supprimer les coffres Recovery Services.

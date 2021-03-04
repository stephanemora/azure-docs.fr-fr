---
title: Restauration de Disques managés Azure
description: Découvrez comment restaurer des Disques managés Azure sur le Portail Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 995217cd17d1e2a16cd7a5f963ee88aa7116d4a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703747"
---
# <a name="restore-azure-managed-disks-in-preview"></a>Restauration de Disques managés Azure (préversion)

>[!IMPORTANT]
>La sauvegarde de disque Azure, en préversion sans contrat de niveau de service, n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour la disponibilité des régions, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).
>
>[Remplissez ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) pour vous inscrire à la préversion.

Cet article explique comment restaurer des [Disques managés Azure](../virtual-machines/managed-disks-overview.md) à partir d’un point de restauration créé par la Sauvegarde Azure.

Actuellement, l’option OLR (Original-Location Recovery) de restauration par remplacement du disque source existant à partir duquel les sauvegardes ont été effectuées n’est pas prise en charge. Il est possible d’effectuer une restauration à partir d’un point de récupération pour créer un disque dans le même groupe de ressources que celui du disque source à partir duquel les sauvegardes ont été effectuées ou dans un autre groupe de ressources. C’est ce que l’on appelle l’option ALR (Alternate-Location Recovery), qui permet de conserver à la fois le disque source et le (nouveau) disque restauré.

Dans cet article, vous allez apprendre à :

- effectuer une restauration pour créer un disque ;

- suivre l’état de l’opération de restauration.

## <a name="restore-to-create-a-new-disk"></a>Restauration visant à créer un disque

Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour effectuer une restauration à partir d’une sauvegarde, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur le groupe de ressources dans lequel le disque doit être restauré.

Le coffre Sauvegarde utilise une identité managée affectée par le système, limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Pour plus d’informations, consultez [Identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Voici les prérequis d’une opération de restauration :

1. Attribuez le rôle **Opérateur de restauration de disque** à l’identité managée du coffre Sauvegarde sur le groupe de ressources dans lequel le disque sera restauré par le service Sauvegarde Azure.

    >[!NOTE]
    > Vous pouvez choisir le même groupe de ressources que celui du disque source à partir duquel les sauvegardes sont effectuées ou un autre groupe de ressources, au sein du même abonnement ou non.

    1. Accédez au groupe de ressources dans lequel le disque doit être restauré, par exemple *TargetRG*.

    1. Accédez à **Contrôle d’accès (IAM)** , puis sélectionnez **Ajouter des attributions de rôles**.

    1. Dans le volet contextuel droit, sélectionnez **Opérateur de restauration de disque** dans la liste déroulante **Rôle**. Sélectionnez l’identité managée du coffre Sauvegarde, puis **Enregistrer**.

        >[!TIP]
        >Tapez le nom du coffre Sauvegarde pour sélectionner l’identité managée du coffre.

        ![Sélection du rôle Opérateur de restauration de disque](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Vérifiez que l’identité managée du coffre Sauvegarde possède le bon ensemble d’attributions de rôles sur le groupe de ressources dans lequel le disque sera restauré.

    1. Accédez à **Coffre Sauvegarde -> Identité**, puis sélectionnez **Attributions de rôles Azure**.

        ![Sélection de Attributions de rôles Azure](./media/restore-managed-disks/azure-role-assignments.png)

    1. Vérifiez que le rôle, le nom de la ressource et le type de ressource s’affichent correctement.

        ![Vérification du rôle, du nom de la ressource et du type de ressource](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Même si les attributions de rôles apparaissent correctement sur le portail, l’application de l’autorisation sur l’identité managée du coffre Sauvegarde peut prendre environ 15 minutes.
    >
    >Lors des sauvegardes planifiées ou d’une opération de sauvegarde à la demande, la Sauvegarde Azure stocke les instantanés incrémentiels de disque dans le groupe de ressources d’instantanés fourni lors de la configuration de la sauvegarde du disque. Elle utilise ces instantanés incrémentiels au cours de l’opération de restauration. Si les instantanés sont déplacés ou supprimés du groupe de ressources d’instantanés ou que les attributions de rôles du coffre Sauvegarde sont révoquées sur le groupe de ressources d’instantanés, l’opération de restauration échoue.

1. Si le disque à sauvegarder est chiffré avec des [clés managées par le client (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) ou un [chiffrement double reposant sur des clés managées par la plateforme et des clés managées par le client](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md), attribuez l’autorisation de rôle **Lecteur** à l’identité managée du coffre Sauvegarde sur la ressource **Jeu de chiffrement de disque**.

Une fois les prérequis respectés, procédez comme suit pour effectuer l’opération de restauration.

1. Sur le [Portail Azure](https://portal.azure.com/), accédez à **Centre de sauvegarde**. Sélectionnez **Instances de sauvegarde** sous la section **Gérer**. Dans la liste des instances de sauvegarde, sélectionnez l’instance de sauvegarde de disque pour laquelle vous souhaitez effectuer l’opération de restauration.

    ![Liste des instances de sauvegarde](./media/restore-managed-disks/backup-instances.png)

    Vous pouvez également effectuer cette opération à partir du coffre Sauvegarde que vous avez utilisé pour configurer la sauvegarde du disque.

1. Sur l’écran **Instance de sauvegarde**, sélectionnez le point de restauration que vous souhaitez utiliser pour effectuer l’opération de restauration, puis **Restaurer**.

    ![Sélectionner le point de restauration](./media/restore-managed-disks/select-restore-point.png)

1. Dans le workflow **Restaurer**, vérifiez les informations des onglets **Informations de base** et **Sélectionner le point de récupération**, puis sélectionnez **Suivant : Paramètres de restauration**.

    ![Vérification des informations Informations de base et Sélectionner le point de récupération](./media/restore-managed-disks/review-information.png)

1. Dans l’onglet **Paramètres de restauration**, sélectionnez **l’Abonnement cible** et le **Groupe de ressources cible** dans lequel vous souhaitez restaurer la sauvegarde. Indiquez le nom du disque à restaurer. Sélectionnez **Suivant : Vérifier + restaurer**.

    ![Paramètres de restauration](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Les disques sauvegardés par la Sauvegarde Azure à l’aide de la solution Sauvegarde de disque peuvent également être sauvegardés par la Sauvegarde Azure à l’aide de la solution de sauvegarde de machine virtuelle Azure avec le coffre Recovery Services. Si vous avez configuré la protection de la machine virtuelle Azure à laquelle ce disque est attaché, vous pouvez aussi utiliser l’opération de restauration de machines virtuelles Azure. Vous avez le choix entre restaurer la machine virtuelle et restaurer les disques et les fichiers ou dossiers à partir du point de récupération de l’instance de sauvegarde de machine virtuelle Azure correspondante. Pour plus d’informations, consultez [Sauvegarde de machine virtuelle Azure](./about-azure-vm-restore.md).

1. Une fois la validation terminée, sélectionnez **Restaurer** pour commencer l’opération de restauration.

    ![Lancement de l’opération de restauration](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > La validation peut prendre quelques minutes avant qu’il ne soit possible de déclencher l’opération de restauration. La validation peut échouer dans les cas suivants :
    >
    > - Il existe déjà dans le **Groupe de ressources cible** un disque portant le même nom dans **Nom du disque restauré**.
    > - L’identité managée du coffre Sauvegarde ne dispose pas d’attributions de rôles valides sur le **Groupe de ressources cible**
    > - Les attributions de rôles de l’identité managée du coffre Sauvegarde sont révoquées sur le **Groupe de ressources d’instantanés** dans lequel sont stockés les instantanés incrémentiels.
    > - Les instantanés incrémentiels ont été déplacés ou supprimés du groupe de ressources d’instantanés.

La restauration a pour effet de créer un disque à partir du point de récupération sélectionné dans le groupe de ressources cible indiqué lors de l’opération de restauration. Pour pouvoir utiliser le disque restauré sur une machine virtuelle existante, vous devez effectuer des étapes supplémentaires :

- Si le disque restauré est un disque de données, vous pouvez attacher un disque existant à une machine virtuelle. Si le disque restauré est un disque de système d’exploitation, vous pouvez échanger le disque de système d’exploitation d’une machine virtuelle sur le Portail Azure dans le menu **Disques** de la section **Paramètres** du volet **Machine virtuelle**.

    ![Échange de disques de système d’exploitation](./media/restore-managed-disks/swap-os-disks.png)

- Dans le cas des machines virtuelles Windows, si le disque restauré est un disque de données, suivez les instructions pour [détacher le disque de données d’origine](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) de la machine virtuelle. Ensuite, [attachez le disque de données](../virtual-machines/windows/attach-managed-disk-portal.md) à la machine virtuelle. Suivez les instructions pour [échanger le disque de système d’exploitation](../virtual-machines/windows/os-disk-swap.md) de la machine virtuelle avec le disque restauré.

- Dans le cas des machines virtuelles Linux, si le disque restauré est un disque de données, suivez les instructions pour [détacher le disque de données d’origine](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) de la machine virtuelle. Ensuite, [attachez le disque de données](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) à la machine virtuelle. Suivez les instructions pour [échanger le disque de système d’exploitation](../virtual-machines/linux/os-disk-swap.md) de la machine virtuelle avec le disque restauré.

Il est recommandé de révoquer l’attribution de rôle **Opérateur de restauration de disque** de l’identité managée du coffre Sauvegarde sur le **Groupe de ressources cible** après la fin de l’opération de restauration.

## <a name="track-a-restore-operation"></a>Suivre une opération de restauration

Une fois que vous déclenchez l’opération de restauration, le service de sauvegarde crée un travail à des fins de suivi. Sauvegarde Azure affiche des notifications sur le travail dans le portail. Pour voir la progression du travail de restauration, procédez comme suit :

1. Accédez à l’écran **Instance de sauvegarde**. Il affiche le tableau de bord des travaux avec l’opération et l’état des sept derniers jours.

    ![Tableau de bord des travaux](./media/restore-managed-disks/jobs-dashboard.png)

1. Pour voir l’état de l’opération de restauration, sélectionnez **Tout afficher** afin de faire apparaître les travaux en cours et passés de cette instance de sauvegarde.

    ![Sélection de Tout afficher](./media/restore-managed-disks/view-all.png)

1. Passez en revue la liste des travaux de sauvegarde et de restauration, ainsi que leur état. Sélectionnez un travail dans la liste des travaux pour en afficher les détails.

    ![Liste des travaux](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Étapes suivantes

- [FAQ sur la sauvegarde de disque Azure](disk-backup-faq.md)
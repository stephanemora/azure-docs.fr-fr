---
title: Créer et configurer des coffres Recovery Services
description: Dans cet article, découvrez comment créer et configurer des coffres Recovery Services stockant les sauvegardes et points de récupération.
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 65f7265dccc5fe28d3503e72bdd6e49123871594
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970522"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Créer et configurer un coffre Recovery Services

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Définir la redondance du stockage

La Sauvegarde Azure gère automatiquement le stockage du coffre. Vous devez spécifier le mode de réplication de ce stockage.

> [!NOTE]
> Vous devez remplacer la valeur du paramètre **Type de réplication du stockage** (Localement redondant/Géoredondant) par un coffre Recovery Services avant de configurer les sauvegardes dans le coffre. Une fois que vous avez configuré la sauvegarde, l’option de modification est désactivée.
>
>- Si vous n’avez pas encore configuré la sauvegarde, [procédez comme suit](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) pour vérifier et modifier les paramètres.
>- Si vous avez déjà configuré la sauvegarde et que vous devez passer de GRS à LRS, [consultez ces solutions de contournement](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Dans le panneau **Coffres Recovery Services**, cliquez sur le nouveau coffre. Dans la section **Paramètres**, cliquez sur **Propriétés**.
1. Dans **Propriétés**, sous **Configuration de la sauvegarde**, cliquez sur **Mise à jour**.

1. Sélectionnez le type de réplication de stockage, puis cliquez sur **Enregistrer**.

     ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, nous vous recommandons de continuer à utiliser le paramètre **Géoredondant** par défaut.
   - Sinon, choisissez l’option **Redondant en local** qui réduit les coûts de stockage Azure.
   - Informez-vous sur la redondance [géo](../storage/common/storage-redundancy-grs.md) et [locale](../storage/common/storage-redundancy-lrs.md).

>[!NOTE]
>Les paramètres de réplication de stockage pour le coffre ne sont pas pertinents pour la sauvegarde de partage de fichiers Azure, car la solution actuelle est basée sur un instantané et aucune donnée n’est transférée vers le coffre. Les instantanés sont stockés dans le même compte de stockage que le partage de fichiers sauvegardé.

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
>- La fonctionnalité de restauration inter-régions (CRR) est désormais disponible en préversion dans toutes les régions publiques Azure.
>- La CRR est une fonctionnalité d’abonnement au niveau du coffre pour tout coffre GRS (désactivé par défaut).
>- Après l’inscription, il peut s’écouler jusqu’à 48 heures avant que les éléments de sauvegarde ne soient disponibles dans les régions secondaires.
>- Actuellement, la CRR est prise en charge uniquement pour Type de gestion des sauvegardes : Machine virtuelle Azure ARM (les machines virtuelles Azure classiques ne sont pas prises en charge).  Lorsque d’autres types de gestion prendront en charge la CRR, ils seront **automatiquement** enregistrés.
>- Actuellement, la restauration interrégion ne peut pas être rétablie sur GRS ou LRS une fois la protection initiée pour la première fois.

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

- **Le type de réplication de stockage** par défaut est défini sur **Géoredondant** (GRS). Une fois que vous avez configuré la sauvegarde, l’option de modification est désactivée.
  - Si vous n’avez pas encore configuré la sauvegarde, [procédez comme suit](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) pour vérifier et modifier les paramètres.
  - Si vous avez déjà configuré la sauvegarde et que vous devez passer de GRS à LRS, [consultez ces solutions de contournement](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- **La suppression réversible** par défaut est **activée** sur les coffres nouvellement créés pour protéger les données de sauvegarde des suppressions accidentelles ou malveillantes. [Procédez comme suit](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) pour évaluer et modifier les paramètres.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Comment passer de GRS à LRS après avoir configuré la sauvegarde

Avant de décider de passer de GRS à un stockage localement redondant (LRS), examinez les compromis entre un coût moindre et une meilleure durabilité des données qui correspondent à votre scénario. Si vous devez passer de GRS à LRS, vous avez deux possibilités. Elles dépendent des besoins de votre entreprise pour conserver les données de sauvegarde :

- [Vous n’avez pas besoin de conserver les données de sauvegarde précédentes](#dont-need-to-preserve-previous-backed-up-data)
- [Vous devez conserver les données de sauvegarde précédentes](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Vous n’avez pas besoin de conserver les données de sauvegarde précédentes

Pour protéger les charges de travail dans un nouveau coffre LRS, vous devez supprimer la protection et les données actuelles dans le coffre GRS et configurer à nouveau les sauvegardes.

>[!WARNING]
>L’opération suivante est destructrice et ne peut pas être annulée. Toutes les données de sauvegarde et tous les éléments de sauvegarde associés au serveur protégé seront définitivement supprimés. Procédez avec prudence.

Arrêtez et supprimez la protection actuelle sur le coffre GRS :

1. Désactivez la suppression réversible dans les propriétés du coffre GRS. Pour désactiver la suppression réversible, [procédez comme suit](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal).

1. Arrêtez la protection et supprimez les sauvegardes du coffre GRS existant. Dans le menu du tableau de bord du coffre, sélectionnez **Éléments de sauvegarde**. Les éléments répertoriés ici qui doivent être déplacés vers le coffre LRS doivent être supprimés avec leurs données de sauvegarde. Voir comment [supprimer des éléments protégés dans le cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) et [supprimer des éléments protégés sur site](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Si vous envisagez de déplacer des partages de fichiers Azure, des serveurs SQL ou des serveurs SAP HANA, vous devrez également les désinscrire. Dans le menu du tableau de bord du coffre, sélectionnez **Infrastructure de sauvegarde**. Voir comment [désinscrire le serveur SQL](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [désinscrire un compte de stockage associé à des partages de fichiers Azure](manage-afs-backup.md#unregister-a-storage-account) et [désinscrire une instance SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Une fois qu’ils sont supprimés du coffre GRS, procédez à la configuration des sauvegardes de votre charge de travail dans le nouveau coffre LRS.

#### <a name="must-preserve-previous-backed-up-data"></a>Vous devez conserver les données de sauvegarde précédentes

Si vous devez conserver les données protégées actuelles dans le coffre GRS et continuer la protection dans un nouveau coffre LRS, il existe des options limitées pour certaines des charges de travail :

- Pour MARS, vous pouvez [arrêter la protection avec conservation des données](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) et inscrire l’agent dans le nouveau coffre LRS.

  - Le service Sauvegarde Azure conservera tous les points de récupération existants du coffre GRS.
  - Vous devrez payer pour conserver les points de récupération dans le coffre GRS.
  - Vous pourrez restaurer les données sauvegardées uniquement pour les points de récupération non expirés dans le coffre GRS.
  - Un nouveau réplica initial des données devra être créé sur le coffre LRS.

- Pour une machine virtuelle Azure, vous pouvez [arrêter la protection avec conservation des données](backup-azure-manage-vms.md#stop-protecting-a-vm) pour la machine virtuelle dans le coffre GRS, déplacer la machine virtuelle vers un autre groupe de ressources, puis protéger la machine virtuelle dans le coffre LRS. Voir [les conseils et les limites](https://docs.microsoft.com/azure/azure-resource-manager/management/move-limitations/virtual-machines-move-limitations) pour déplacer une machine virtuelle vers un autre groupe de ressources.

  Une machine virtuelle ne peut être protégée que dans un coffre à la fois. Toutefois, la machine virtuelle dans le nouveau groupe de ressources peut être protégée dans le coffre LRS, car elle est considérée comme une machine virtuelle différente.

  - Le service Sauvegarde Azure conservera les points de récupération qui ont été sauvegardés dans le coffre GRS.
  - Vous devrez payer pour conserver ces points de récupération dans le coffre GRS (voir [Tarification de Sauvegarde Azure](azure-backup-pricing.md) pour en savoir plus).
  - Vous serez en mesure de restaurer la machine virtuelle si nécessaire, à partir du coffre GRS.
  - La première sauvegarde dans le coffre LRS de la machine virtuelle placée dans la nouvelle ressource sera un réplica initial.


## <a name="next-steps"></a>Étapes suivantes

[Découvrez](backup-azure-recovery-services-vault-overview.md) les coffres Recovery Services.
[Découvrez](backup-azure-delete-vault.md) comment supprimer les coffres Recovery Services.

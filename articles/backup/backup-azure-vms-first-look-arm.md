---
title: 'Premiers pas : Protéger les machines virtuelles Azure avec un coffre Recovery Services'
description: Protégez les machines virtuelles Azure avec un coffre Recovery Services. Utilisez les sauvegardes des machines virtuelles déployées à l’aide de Resource Manager, des machines virtuelles déployées à l’aide du modèle Classic et des machines virtuelles, machines virtuelles chiffrées et machines virtuelles sur disques gérés du service de stockage Premium pour protéger vos données. Créez et enregistrez un coffre Recovery Services. Enregistrez des machines virtuelles, créez une stratégie et protégez des machines virtuelles dans Azure.
services: backup
author: rayne-wiselman
manager: carmonm
keyword: backups; vm backup
ms.service: backup
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: raynew
ms.custom: H1Hack27Feb2017
keywords: sauvegardes; sauvegarde de machine virtuelle
ms.openlocfilehash: 2c6b881e5717c0f4600b4e3c2f47c19b5d2dae51
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869926"
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vault"></a>Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services

Cet article explique comment configurer la protection d’une machine virtuelle à partir du menu des opérations sur les machines virtuelles ou du coffre Recovery Services. Les coffres Recovery Services protègent :

* Machines virtuelles déployées à l’aide de Resource Manager
* les machines virtuelles Classic,
* les machines virtuelles de stockage standard,
* les machines virtuelles Premium Storage.
* Machines virtuelles exécutées sur des disques gérés
* Les machines virtuelles chiffrées à l’aide d’Azure Disk Encryption
* Sauvegarde cohérente des applications des machines virtuelles Windows à l’aide de machines virtuelles VSS et Linux avec des scripts pré et post-instantané personnalisés

Pour plus d’informations sur la protection des machines virtuelles Stockage Premium, consultez la section [Sauvegarder et restaurer des machines virtuelles Stockage Premium](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Pour plus d’informations sur la prise en charge des machines virtuelles sur disques gérés, consultez la section [Back up and restore VMs on managed disks](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) (Sauvegarder et restaurer des machines virtuelles sur des disques gérés). Pour plus d’informations sur l’infrastructure pré et post-script pour la sauvegarde de machine virtuelle Linux, consultez l’article [Sauvegarde de machine virtuelle Linux cohérente dans l’application à l’aide de pré/post-scripts](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Pour en savoir plus sur ce que vous pouvez et ne pouvez pas sauvegarder, consultez [Préparation de votre environnement pour la sauvegarde des machines virtuelles Azure](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

> [!NOTE]
> Le service de sauvegarde crée un groupe de ressources distinct du groupe de ressources de la machine virtuelle afin de stocker la collection de points de restauration. Les clients sont invités à ne pas verrouiller le groupe de ressources créé pour une utilisation par le service de sauvegarde.
Le format d’affectation des noms du groupe de ressources créé par le service de sauvegarde est : AzureBackupRG_`<Geo>`_`<number>`
<br>Par exemple : AzureBackupRG_northeurope_1
>
>

Votre point de départ varie selon le nombre de machines virtuelles que vous souhaitez protéger. Si vous voulez sauvegarder plusieurs machines virtuelles en une seule opération, accédez au coffre Recovery Services et [lancez la sauvegarde à partir du tableau de bord du coffre](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Si vous ne voulez sauvegarder qu’une seule machine virtuelle, [effectuez la sauvegarde directement dans le menu des opérations sur les machines virtuelles](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>Configurer la sauvegarde à partir du menu des opérations sur les machines virtuelles

La procédure ci-après vous guide dans la configuration du travail de sauvegarde à partir du menu des opérations sur les machines virtuelles. Les étapes s’appliquent uniquement aux machines virtuelles dans le portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le Menu hub, cliquez sur **Tous les services** et, dans la boîte de dialogue Filtrer, tapez **Machines virtuelles**. Lorsque vous tapez cette chaîne, la liste des ressources est filtrée en conséquence. Dès que vous voyez apparaître le service Machines virtuelles, sélectionnez-le.

  ![Capture d’écran montrant comment accéder aux machines virtuelles à partir de Tous les services](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  La liste des machines virtuelles de l’abonnement apparaît.

  ![Affichage de la liste des machines virtuelles de l’abonnement](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Dans la liste, sélectionnez la machine virtuelle que vous souhaitez sauvegarder.

  ![Affichage de la liste des machines virtuelles de l’abonnement](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Quand vous sélectionnez la machine virtuelle, la liste des machines virtuelles se déplace vers la gauche, tandis que le menu de gestion et le tableau de bord de la machine virtuelle s’affichent.

4. Dans le menu de gestion de la machine virtuelle, dans la section **Opérations**, cliquez sur **Sauvegarde**. </br>

  ![Option de sauvegarde dans le menu de gestion de la machine virtuelle](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Le menu Activer la sauvegarde s’ouvre.

  ![Option de sauvegarde dans le menu de gestion de la machine virtuelle](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. Dans la zone Coffre Recovery Services, cliquez sur **Sélectionner**, puis choisissez un coffre dans la liste déroulante.

  ![Assistant Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  S’il n’existe aucun coffre Recovery Services ou que vous souhaitez utiliser un nouveau coffre, cliquez sur **Créer** et fournissez le nom du nouveau coffre. Un coffre est créé dans le même groupe de ressources et dans la même région que la machine virtuelle. Si vous souhaitez créer un coffre Recovery Services avec d’autres valeurs, consultez la section décrivant comment [créer un coffre Recovery Services](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Dans le menu Choisir une stratégie de sauvegarde, sélectionnez une stratégie. Les détails de la stratégie sélectionnée s’affichent sous le menu déroulant.

  Si vous souhaitez créer une stratégie ou modifier la stratégie existante, cliquez sur **Créer (ou modifier) une stratégie** pour ouvrir l’éditeur de stratégie de sauvegarde. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Pour enregistrer les modifications de la stratégie de sauvegarde et revenir au menu Activer la sauvegarde, cliquez sur **OK**.

  ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Pour appliquer le coffre Recovery Services et la stratégie de sauvegarde à la machine virtuelle, cliquez sur **Activer la sauvegarde** pour déployer la stratégie. En déployant la stratégie, vous l’associez au coffre et aux machines virtuelles.

  ![Bouton Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Vous pouvez suivre la progression de la configuration par le biais des notifications qui apparaissent dans le portail. L’exemple ci-après indique que le déploiement a démarré.

  ![Notification Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Une fois la configuration terminée, cliquez sur **Sauvegarde** dans le menu de gestion de la machine virtuelle pour ouvrir le menu Sauvegarde et visualiser les détails disponibles.

  ![Affichage des éléments de sauvegarde de la machine virtuelle](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  Jusqu’à la fin de la sauvegarde initiale, la zone **État de la dernière sauvegarde** présente la valeur **Avertissement (Sauvegarde initiale en attente)**. Pour connaître l’heure du prochain travail de sauvegarde planifié, sous **Récapitulatif**, cliquez sur le nom de la stratégie. Le menu Stratégie de sauvegarde s’affiche et indique l’heure de la sauvegarde planifiée.

10. Pour protéger la machine virtuelle, cliquez sur **Sauvegarder maintenant**.

  ![Clic sur l’option Sauvegarder maintenant pour exécuter la sauvegarde initiale](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Le menu Sauvegarder maintenant s’ouvre.

  ![Affichage du panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. Dans le menu Sauvegarder maintenant, cliquez sur l’icône de calendrier. Utilisez le contrôle de calendrier pour sélectionner le dernier jour de conservation de ce point de récupération, puis cliquez sur **OK**.

  ![Définition du dernier jour de conservation du point de récupération dans le panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Les notifications de déploiement vous informent que la sauvegarde a été déclenchée et que vous pouvez surveiller la progression du travail sur la page Travaux de sauvegarde.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Configurer le travail de sauvegarde à partir du coffre Recovery Services
Pour configurer la tâche de sauvegarde, vous procédez comme suit.

1. Vous créez un coffre Recovery Services pour une machine virtuelle.
2. Utilisez le portail Azure pour sélectionner un scénario, définir une stratégie de sauvegarde, puis identifier les éléments à protéger.
3. Effectuez la sauvegarde initiale.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Création d’un coffre Recovery Services pour une machine virtuelle
Un archivage de Recovery Services est une entité qui stocke l’ensemble des sauvegardes et des points de récupération créés au fil du temps. L’archivage de Recovery Services contient également la stratégie de sauvegarde appliquée aux machines virtuelles protégées.

> [!NOTE]
> La sauvegarde de machines virtuelles est un processus local. Vous ne pouvez pas sauvegarder de machines virtuelles d’une région dans un archivage de Recovery Services situé dans une autre région. Donc, dans chaque région Azure contenant des machines virtuelles à sauvegarder, il doit exister au moins un archivage de Recovery Services.
>
>

Pour créer un archivage de Recovery Services :

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/) à l’aide de votre abonnement Azure.
2. Dans le Menu hub, cliquez sur **Tous les services** et, dans la boîte de dialogue Filtrer, tapez **Recovery Services**. Lorsque vous tapez cette chaîne, la liste des ressources est filtrée en conséquence. Dès que vous voyez la ressource Archivages de Recovery Services apparaître dans la liste, cliquez dessus.

    ![Créer un coffre Recovery Services - Étape 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Si l’abonnement inclut des coffres Recovery Services, ces derniers sont répertoriés.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Le menu du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Créer un coffre Recovery Services - Étape 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.

5. Dans la section **Abonnement**, utilisez le menu déroulant pour choisir l’abonnement Azure. Si vous n’utilisez qu’un seul abonnement, ce dernier s’affiche et vous pouvez passer directement à l’étape suivante. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.

6. Dans la section **Groupe de ressources** :

    * Sélectionnez **Créer** si vous voulez créer un groupe de ressources.
    Ou
    * sélectionnez **Utiliser existant** et cliquez sur le menu déroulant pour afficher la liste des groupes de ressources disponibles.

  Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre. Ce choix définit la région géographique où vos données de sauvegarde sont envoyées.

  > [!IMPORTANT]
  > Si vous ne savez pas où se trouve votre machine virtuelle, fermez la boîte de dialogue de création d’archivage et accédez à la liste des machines virtuelles dans le portail. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’entre elles. Créez l’archivage dans la première région avant de passer à la région suivante. Il est inutile de spécifier les comptes de stockage dans lesquels stocker les données de sauvegarde : le coffre Recovery Services et le service de sauvegarde Azure gèrent cela automatiquement.
  >

8. En bas du menu Coffre Recovery Services, cliquez sur **Créer**.

    La création du coffre Recovery Services peut prendre plusieurs minutes. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre archivage créé, il apparaît dans la liste des archivages de Recovery Services. Si vous ne voyez pas votre coffre après quelques minutes, cliquez sur **Actualiser**.

    ![Bouton Actualiser](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Une fois que votre coffre apparaît dans la liste des coffres Recovery Services, vous êtes prêt à définir la redondance du stockage.

Maintenant que vous avez créé votre archivage, découvrez comment définir la réplication du stockage.

### <a name="set-storage-replication"></a>Définir la réplication du stockage
L’option de réplication du stockage vous permet de choisir entre stockage géo-redondant et stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si le coffre Recovery Services est votre sauvegarde principale, laissez l’option de réplication de stockage définie sur le stockage géoredondant. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy-grs.md) et [localement redondant](../storage/common/storage-redundancy-lrs.md), consultez l’article [Réplication Azure Storage](../storage/common/storage-redundancy.md).

Pour modifier le paramètre de réplication du stockage :

1. Dans le menu **Archivages de Recovery Services**, sélectionnez le nouveau coffre.

  ![Sélectionnez le nouveau coffre dans la liste des coffres Recovery Services.](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Quand vous cliquez sur le coffre, le menu Paramètres (*dont la partie supérieure porte le nom du coffre*) et le tableau de bord du coffre s’ouvrent.

  ![Afficher la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. Dans le menu Gestion du nouveau coffre, utilisez le curseur vertical pour faire défiler l’écran jusqu’à la section Gestion, puis cliquez sur **Infrastructure de sauvegarde** pour ouvrir le menu Infrastructure de sauvegarde.

   ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. Dans le menu Infrastructure de sauvegarde, cliquez sur **Configuration de la sauvegarde** pour ouvrir le menu **Configuration de la sauvegarde**.

    ![Définir la configuration de stockage du nouveau coffre](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Choisissez l’option de réplication de stockage à appliquer à votre archivage.

    ![Options de configuration du stockage](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, laissez cette option **inchangée**. Sinon, choisissez l’option **Redondant en local** qui réduit les coûts de stockage Azure. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy-grs.md) et [localement redondant](../storage/common/storage-redundancy-lrs.md), consultez la [présentation de la redondance du stockage](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Sélectionner l’objectif d’une sauvegarde, définir la stratégie et définir les éléments à protéger
Avant d’enregistrer une machine virtuelle dans un coffre, lancez le processus de découverte pour vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont bien identifiées. Le processus interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement ainsi que des informations supplémentaires, comme le nom du service cloud et la région. Dans le portail Azure, l’objectif fait référence à ce que vous allez placer dans l’archivage de Recovery Services. La stratégie permet de planifier la fréquence et l’heure de la création des points de récupération. Elle inclut également la durée de rétention de ces derniers.

1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape 2. Sinon, cliquez sur **Tous les services**. Tapez **Recovery Services** et cliquez sur **Coffres Recovery Services**.

    ![Créer un coffre Recovery Services - Étape 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    La liste des coffres Recovery Services s’affiche.

    ![Affichage de la liste des coffres Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Dans la liste des coffres Recovery Services, sélectionnez un coffre pour ouvrir son tableau de bord.

     ![Ouvrir le menu du coffre](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Dans le menu du tableau de bord du coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarder.

    ![Ouvrir le menu Sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Les menus Sauvegarde et Objectif de sauvegarde s’ouvrent.

    ![Ouvrir le menu Scénario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. Dans le menu Objectif de sauvegarde, dans le menu déroulant **Où s’exécute votre charge de travail ?**, choisissez Azure. Au niveau du menu déroulant **Que voulez-vous sauvegarder ?**, sélectionnez Machine virtuelle, puis cliquez sur **OK**.

    Ces actions ont pour conséquence d’inscrire l’extension de machine virtuelle avec le coffre. Le menu Objectif de sauvegarde se ferme et le menu **Stratégie de sauvegarde** s’ouvre.

    ![Ouvrir le menu Scénario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Dans le menu Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Cliquez sur **OK** pour associer la stratégie de sauvegarde au coffre.

    Le menu Stratégie de sauvegarde se ferme et le menu **Sélectionner les machines virtuelles** s’ouvre.
5. Dans le menu **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles à associer à la stratégie spécifiée, puis cliquez sur **OK**.

    ![Sélectionner la charge de travail](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    La machine virtuelle sélectionnée est validée. Si vous ne voyez pas les machines virtuelles que vous espériez voir, vérifiez qu’elles existent dans le même emplacement Azure que le coffre Recovery Services et qu’elles ne sont pas déjà protégées. L’emplacement du coffre Recovery Services est indiqué dans le tableau de bord associé.

6. Tous les paramètres du coffre étant définis, cliquez sur **Activer la sauvegarde** dans le menu Sauvegarde pour déployer la stratégie sur le coffre et les machines virtuelles. Le déploiement de la stratégie de sauvegarde ne crée pas le point de récupération initial pour la machine virtuelle.

    ![Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après l’activation de la sauvegarde, votre stratégie de sauvegarde s’exécutera selon la planification. Toutefois, procédez au lancement du premier travail de sauvegarde.

## <a name="initial-backup"></a>Sauvegarde initiale
Le déploiement d’une stratégie de sauvegarde sur la machine virtuelle ne signifie pas que les données ont été sauvegardées. Par défaut, la première sauvegarde planifiée (définie dans la stratégie de sauvegarde) est la sauvegarde initiale. Jusqu’à celle-ci, l’état de la dernière sauvegarde dans le menu **Travaux de sauvegarde** est défini sur **Avertissement (sauvegarde initiale en attente)**.

![Sauvegarde en attente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

À moins que votre sauvegarde initiale ne soit prévue prochainement, il est recommandé de sélectionner l’option **Sauvegarder maintenant**.

Pour exécuter le travail de sauvegarde initial :

1. Dans le tableau de bord du coffre, cliquez sur le numéro sous **Éléments de sauvegarde** ou cliquez sur la vignette **Éléments de sauvegarde**. <br/>
  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Le menu **Éléments de sauvegarde** s’ouvre.

  ![Éléments de sauvegarde](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Dans le menu **Éléments de sauvegarde**, sélectionnez l’élément.

  ![Icône Paramètres](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  La liste **Éléments de sauvegarde** s’affiche. <br/>

  ![Travail de sauvegarde déclenché](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Dans la liste **Éléments de sauvegarde**, cliquez sur le bouton de sélection **...** pour ouvrir le menu contextuel.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Le menu contextuel s’affiche.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Dans le menu contextuel, cliquez sur **Sauvegarder maintenant**.

  ![Menu contextuel](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Le menu Sauvegarder maintenant s’ouvre.

  ![Affichage du menu Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Dans le menu Sauvegarder maintenant, cliquez sur l’icône de calendrier. Utilisez le contrôle de calendrier pour sélectionner le dernier jour de conservation de ce point de récupération, puis cliquez sur **Sauvegarde**.

  ![Définition du dernier jour de conservation du point de récupération dans le panneau Sauvegarder maintenant](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Les notifications de déploiement vous informent que la sauvegarde a été déclenchée et que vous pouvez surveiller la progression du travail sur la page Travaux de sauvegarde. Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

  > [!NOTE]
  > Toutes les données sauvegardées par sauvegarde Azure sont chiffrées au repos par le biais de [Storage Service Encryption (SSE)](../storage/common/storage-service-encryption.md).
  >
  >

6. Pour afficher ou suivre l’état de la sauvegarde initiale, dans le tableau de bord du coffre, au niveau de la vignette **Travaux de sauvegarde**, cliquez sur **En cours**.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Le menu Travaux de sauvegarde s’ouvre.

  ![Vignette Travaux de sauvegarde](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Le menu **Travaux de sauvegarde** indique l’état de tous les travaux. Vérifiez si le travail de sauvegarde de votre machine virtuelle est en cours d’exécution ou s’il est terminé. Lorsqu’un travail de sauvegarde est achevé, il présente l’état *Terminé*.

  > [!NOTE]
  > Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installer l’agent de machine virtuelle sur la machine virtuelle
Ces informations sont fournies en cas de nécessité. L’agent de machine virtuelle Azure doit être installé sur la machine virtuelle Azure pour permettre la prise en charge de l’extension Backup. Cependant, si votre machine virtuelle a été créée à partir de la galerie Azure, l’agent y est déjà installé. L’agent de machine virtuelle n’est pas préinstallé sur les machines virtuelles qui ont été migrées à partir de centres de données locaux. Dans ce cas, il faut l’installer de manière explicite. Si vous rencontrez des problèmes de sauvegarde de la machine virtuelle Azure, vérifiez que l’agent de machine virtuelle Azure est correctement installé sur celle-ci (voir le tableau ci-dessous). Si vous créez une machine virtuelle personnalisée, installez l’agent de machine virtuelle avant son approvisionnement.

En savoir plus sur l’[agent de machine virtuelle](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) et [comment l’installer](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Le tableau suivant fournit des informations supplémentaires sur l’agent de machine virtuelle pour les machines virtuelles Windows et Linux.

| **opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle |<li>Téléchargez et installez le fichier [MSI de l’agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation. <li>[Mettez à jour la propriété de la machine virtuelle](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |<li> Installez l’ [agent Linux](https://github.com/Azure/WALinuxAgent) le plus récent à partir de GitHub. Vous avez besoin de privilèges d’administrateur pour terminer l’installation. <li> [Mettez à jour la propriété de la machine virtuelle](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé. |
| Mise à jour de l’agent de machine virtuelle |La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |Suivez les instructions fournies dans l’article [Mise à jour d’un agent de machine virtuelle Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Validation de l’installation de l’agent de machine virtuelle |<li>Accédez au dossier *C:\WindowsAzure\Packages* sur la machine virtuelle Azure. <li>Le fichier WaAppAgent.exe doit être présent.<li> Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure. |N/A |

### <a name="backup-extension"></a>Extension de sauvegarde
Une fois l’agent de machine virtuelle installé sur la machine virtuelle, le service Azure Backup installe l’extension de sauvegarde vers l’agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l'extension de sauvegarde sans intervention supplémentaire de l'utilisateur.

Le service de sauvegarde installe l’extension de sauvegarde, même si la machine virtuelle n’est pas en cours d’exécution. Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de la machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée, Ce type de sauvegarde est appelé sauvegarde en mode hors connexion de machine virtuelle, et le point de récupération est *cohérent en cas d’incident*.

## <a name="troubleshooting-information"></a>Résolution de problèmes
Si vous rencontrez des problèmes pour accomplir certaines tâches décrites dans cet article, consultez les [instructions pour la résolution des problèmes](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Tarifs
Le coût de la sauvegarde des machines virtuelles Azure dépend du nombre d’instances protégées. Pour obtenir une définition d’une instance protégée, voir [Qu’est-ce qu’une instance protégée](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Pour obtenir un exemple de calcul du coût de la sauvegarde d’une machine virtuelle, voir [Mode de calcul des instances protégées](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). Pour plus d’informations sur la [tarification de la sauvegarde](https://azure.microsoft.com/pricing/details/backup/), voir la page de la tarification de la sauvegarde Azure.

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](https://aka.ms/azurebackup_feedback).

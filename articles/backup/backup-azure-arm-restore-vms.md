---
title: 'Sauvegarde Azure : Restaurer des machines virtuelles à l’aide du portail Azure'
description: Restaurer une machine virtuelle Azure à partir d’un point de récupération à l’aide du portail Azure
services: backup
author: geethalakshmig
manager: vijayts
keywords: restauration de sauvegarde ; restauration ; point de récupération ;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793374"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Utiliser le portail Azure pour restaurer des machines virtuelles
Protégez vos données en prenant des instantanés de vos données à des intervalles définis. Ces instantanés sont considérés comme des points de récupération stockés dans des coffres Recovery Services. Lorsqu’il est nécessaire de réparer ou de générer de nouveau une machine virtuelle, sa restauration peut s’effectuer à partir des points de récupération enregistrés. Lorsque vous restaurez un point de récupération, vous pouvez :

* Créer une machine virtuelle : À partir d’un point de récupération jusqu’à une date et heure de votre machine virtuelle sauvegardée.
* Restaurer des disques : Utiliser le modèle fourni avec le processus pour personnaliser la machine virtuelle restaurée ou effectuer une récupération de fichier individuel.

Cet article explique comment restaurer une machine virtuelle sur une nouvelle machine virtuelle ou restaurer tous les disques de secours. Pour la récupération de fichier individuel, consultez [Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure](backup-azure-restore-files-from-vm.md).

![Trois méthodes pour restaurer à partir d’une sauvegarde de machine virtuelle](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


La restauration d’une machine virtuelle ou de tous les disques à partir de la sauvegarde de machine virtuelle implique deux étapes :

* Sélectionnez un point pour la restauration.
* Sélectionnez le type de restauration.
    - Option 1 : Créer une machine virtuelle
    - Option 2 : Restaurer des disques

## <a name="select-a-restore-point-for-restore"></a>Sélectionner un point pour la restauration
1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

2. Dans le menu Azure, sélectionnez **Tous les services**. Dans la liste des services, tapez **Recovery Services** ou accédez à **STOCKAGE**, où le service **Coffres Recovery Service** est répertorié, et sélectionnez-le.

    ![Coffre Recovery Services](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. La liste des coffres de l’abonnement s’affiche.

    ![Liste des coffres Recovery Services](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Dans la liste des coffres Recovery Service, sélectionnez le coffre associé à la machine virtuelle que vous souhaitez restaurer. Lorsque vous sélectionnez le coffre, son tableau de bord s’affiche.

    ![Coffre Recovery Services sélectionné](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. Dans la vignette **Éléments de sauvegarde** du tableau de bord du coffre, sélectionnez **Machine virtuelle Azure**.

    ![Tableau de bord du coffre](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. Le panneau **Éléments de sauvegarde**, avec la liste des machines virtuelles Azure est ouvert.

    ![Liste des machines virtuelles du coffre](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Dans la liste, sélectionnez une machine virtuelle pour ouvrir le tableau de bord. Le tableau de bord de la machine virtuelle s’ouvre sur la zone de supervision qui contient les **Points de restauration**. Toutes les opérations au niveau de la machine virtuelle, comme **Sauvegarder maintenant**, **Récupération de fichiers**, **Arrêter la sauvegarde**, peuvent être effectuées à partir de ce panneau.
La restauration peut être effectuée de plusieurs façons à partir de ce panneau. Notez que ce panneau ne répertorie que les points de récupération des 30 derniers jours.

    - Pour restaurer à l’aide d’un point de restauration des 30 derniers jours, cliquez avec le bouton droit sur la machine virtuelle > **Restaurer la machine virtuelle**.
    - Pour restaurer à l’aide d’un point de restauration datant de plus de 30 jours, cliquez sur le lien situé sous **Points de restauration**.
    - Pour lister et filtrer les machines virtuelles avec des dates personnalisées, cliquez sur **Restaurer la machine virtuelle** dans le menu. Utilisez le filtre pour modifier l’intervalle de temps des points de restauration affichés. Vous pouvez également filtrer différents types de cohérence des données.
8. Passez en revue les paramètres de point de restauration :
    - La cohérence des données montre le [type de cohérence](backup-azure-vms-introduction.md#consistency-types) dans le point de récupération.
    - Le **type de récupération** montre l’emplacement auquel le point est stocké (dans un compte de stockage, dans le coffre, ou les deux). [Découvrez-en plus](https://azure.microsoft.com/blog/large-disk-support/) sur les points de récupération instantanée.

  ![Points de restauration](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Sélectionnez un point de restauration.

10. Sélectionnez **Configuration de la restauration**. Le panneau **Configuration de la restauration** s’ouvre.

## <a name="choose-a-vm-restore-configuration"></a>Choisir une configuration de restauration de machine virtuelle
Après avoir sélectionné le point de restauration, choisissez une configuration de restauration de machine virtuelle. Pour configurer la machine virtuelle restaurée, vous pouvez utiliser le portail Azure ou PowerShell.

### <a name="restore-options"></a>Options de restauration

**Option** | **Détails**
--- | ---
**Créer-Créer une machine virtuelle** | Équivaut à la création rapide d’une machine virtuelle. Permet d’obtenir une machine virtuelle de base opérationnelle à partir d’un point de restauration.<br/><br/> Il est possible de modifier les paramètres de la machine virtuelle dans le cadre du processus de restauration.
**Créer-Restaurer le disque** | Crée une machine virtuelle que vous pouvez personnaliser dans le cadre du processus de restauration.<br/><br/> Cette option copie des disques durs virtuels dans le compte de stockage que vous spécifiez.<br/><br/> - Le compte de stockage doit se trouver au même emplacement que le coffre.<br/><br/> Si vous n’avez pas de compte de stockage approprié, vous devez en créer un.<br/><br/> Le type de réplication du compte de stockage est indiqué entre parenthèses. Le stockage redondant interzone (ZRS) n’est pas pris en charge.<br/><br/> À partir du compte de stockage, vous pouvez soit attacher les disques restaurés à une machine virtuelle existante, soit créer une machine virtuelle à partir des disques restaurés à l’aide de PowerShell.
**Remplacer l’existant** | Avec cette option, vous n’avez pas besoin de créer une machine virtuelle.<br/><br/> La machine virtuelle actuelle doit exister. Si elle a été supprimée, vous ne pouvez pas utiliser cette option.<br/><br/> Sauvegarde Azure prend un instantané de la machine virtuelle existante et le stocke à l’emplacement intermédiaire spécifié. Les disques existants connectés à la machine virtuelle sont ensuite remplacés par le point de restauration sélectionné. L’instantané précédemment créé est copié dans le coffre et stocké sous forme de point de récupération conformément à votre stratégie de conservation de sauvegarde.<br/><br/> L’option Remplacer l’existant est prise en charge pour les machines virtuelles managées non chiffrées. Elle ne l’est pas pour les disques non managés, les [machines virtuelles généralisées](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) ou les machines virtuelles [créées à l’aide d’images personnalisées](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Si le point de restauration a plus ou moins de disques que la machine virtuelle actuelle, alors le nombre de disques du point de restauration reflète uniquement la machine virtuelle.

> [!NOTE]
> Si vous voulez restaurer une machine virtuelle dont les paramètres de mise en réseau sont avancés, par exemple s’ils sont gérés par un équilibreur de charge interne ou externe ou si elle comporte plusieurs cartes réseau ou plusieurs adresses IP réservées, effectuez la restauration avec PowerShell. [Plus d’informations](#restore-vms-with-special-network-configurations)
> Si une machine virtuelle Windows utilise des [licences HUB](../virtual-machines/windows/hybrid-use-benefit-licensing.md), utilisez l’option **Créer-Restaurer le disque**, puis utilisez PowerShell ou le modèle de restauration pour créer la machine virtuelle avec un **type de licence** défini sur **Windows_Server**. Vous pouvez également appliquer ce paramètre après avoir créé la machine virtuelle.


Spécifiez le paramètre de restauration comme suit :

1. Dans **Restaurer**, sélectionnez un [point de restauration](#select-a-restore-point-for-restore) > **Configuration de la restauration**.
2. Dans **Configuration de la restauration**, sélectionnez la façon dont vous souhaitez restaurer la machine virtuelle conformément aux paramètres résumés dans le tableau ci-dessus.

    ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Créer-Créer une machine virtuelle

1. Dans **Configuration de la restauration** > **Créer** > **Type de restauration**, sélectionnez **Créer une machine virtuelle**.
2. Dans **Nom de la machine virtuelle**, spécifiez une machine virtuelle qui n’existe pas dans l’abonnement.
3. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant pour la nouvelle machine virtuelle ou créez-en un avec un nom global unique. Si vous attribuez un nom qui existe déjà, Azure attribue au groupe le même nom que celui de la machine virtuelle.
4. Dans **Réseau virtuel**, sélectionnez le réseau virtuel dans lequel la machine virtuelle sera placée. Tous les réseaux virtuels associés à l’abonnement sont affichés. Sélectionnez le sous-réseau. Le premier sous-réseau est sélectionné par défaut.
5. Dans **Emplacement de stockage**, spécifiez le type de stockage utilisé pour la machine virtuelle.

    ![Assistant Configuration de restauration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Dans **Configuration de la restauration**, sélectionnez **OK**. Dans **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.



## <a name="create-new-restore-disks"></a>Créer-Restaurer des disques

1. Dans **Configuration de la restauration** > **Créer** > **Type de restauration**, sélectionnez **Restaurer des disques**.
2. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant pour les disques restaurés ou créez-en un avec un nom global unique.
3. Dans **Compte de stockage**, spécifiez le compte dans lequel copier les disques durs virtuels. Vérifiez que le compte se trouve dans la même région que le coffre.

    ![Configuration de la récupération terminée](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Dans **Configuration de la restauration**, sélectionnez **OK**. Dans **Restaurer**, cliquez sur **Restaurer** pour déclencher l’opération de restauration.
5. Une fois que le disque est restauré, vous pouvez effectuer tout ce qui suit pour terminer l’opération de restauration de la machine virtuelle.

    - Utilisez le modèle qui a été généré dans le cadre de l’opération de restauration pour personnaliser les paramètres et déclencher le déploiement de machine virtuelle. Vous modifiez les paramètres de modèle par défaut, puis vous envoyez le modèle pour le déploiement de machine virtuelle.
    - Vous pouvez [attacher les disques restaurés](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) à une machine virtuelle existante.
    - Vous pouvez [créer une machine virtuelle](backup-azure-vms-automation.md#restore-an-azure-vm) à partir des disques restaurés à l’aide de PowerShell.


## <a name="replace-existing-disks"></a>Remplacer les disques existants

Utilisez cette option pour remplacer les disques existants dans la machine virtuelle actuelle par le point de restauration sélectionné.

1. Dans **Configuration de la restauration**, cliquez sur **Remplacer l’existant**.
2. Dans **Type de restauration**, sélectionnez **Remplacer les disques** (le point de restauration qui remplacera le ou les disques de machine virtuelle existants).
3. Dans **Emplacement intermédiaire**, spécifiez l’emplacement auquel les instantanés des disques managés actuels doivent être enregistrés.

   ![Assistant Configuration de restauration avec l’option Replace existing (Remplacer l’existant)](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Suivi de l’opération de restauration
Après avoir déclenché l’opération de restauration, le service de sauvegarde crée une tâche pour le suivi de l’opération de restauration. Par ailleurs, le service de sauvegarde crée et affiche temporairement la notification dans la zone **Notifications** du portail. Si vous ne voyez pas la notification, sélectionnez le symbole **Notifications** pour afficher vos notifications.

![Restauration déclenchée](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Cliquez sur le lien hypertexte des notifications pour accéder à la liste **BackupJobs**. Tous les détails des opérations pour un travail donné sont disponibles dans les listes **BackupJobs** (Travaux de sauvegarde). Vous pouvez accéder à **BackupJobs** à partir du tableau de bord du coffre en cliquant sur la vignette Travaux de sauvegarde ; sélectionnez **Machine virtuelle Azure** pour afficher les tâches associées au coffre.

Le panneau **Travaux de sauvegarde** s’ouvre et affiche la liste des travaux.

![Liste des machines virtuelles d’un coffre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

La **barre de progression** est désormais disponible dans le panneau **Détails de la restauration**. Vous pouvez ouvrir le panneau **Détails de la restauration** en cliquant sur un travail de restauration dont l’état est **En cours**. Le **barre de progression** est disponible dans toutes les variantes des restaurations, notamment **Créer**, **Restaurer le disque** et **Remplacer l’existant**. Les détails indiqués par la barre de progression d’une restauration sont la **durée estimée de la restauration**, le **pourcentage de restauration** et le **nombre d’octets transférés**.

Les détails de la barre de progression de la restauration sont précisés ci-dessous :

- La **durée estimée de la restauration** indique initialement le temps nécessaire pour terminer l’opération de restauration. Au fur et à mesure que l’opération progresse, le temps nécessaire se réduit pour atteindre 0 une fois l’opération de restauration terminée.
- Le **pourcentage de restauration** indique le pourcentage terminé de l’opération de restauration.
- Le **nombre d’octets transférés** est disponible dans la sous-tâche quand la restauration est effectuée par le biais de l’option Créer une machine virtuelle. Est indiqué le nombre d’octets transférés par rapport au nombre total d’octets à transférer.


## <a name="use-templates-to-customize-a-restored-vm"></a>Utiliser des modèles pour personnaliser une machine virtuelle restaurée
À la [fin de l’opération de restauration des disques](#Track-the-restore-operation), utilisez le modèle qui a été généré dans le cadre de l’opération de restauration pour créer une machine virtuelle avec une configuration différente de la configuration de sauvegarde. Vous pouvez également l’utiliser pour personnaliser les noms des ressources qui ont été créés pendant le processus de création d’une machine virtuelle à partir d’un point de restauration.

![Détail du travail de restauration](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Pour obtenir le modèle qui a été généré dans le cadre de l’option de restauration des disques :

1. Accédez à **Restore Job Details** (Détails du travail de restauration) correspondant à la tâche.

2. Dans la fenêtre **Détails du travail de restauration**, sélectionnez **Déployer un modèle** pour lancer le déploiement du modèle.

3. Sur le panneau **Déployer un modèle**, utilisez la procédure de déploiement pour [modifier et déployer le modèle](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), ou ajoutez des personnalisations en [créant un modèle](../azure-resource-manager/resource-group-authoring-templates.md) avant d’effectuer le déploiement.

  ![Charger un déploiement de modèle](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Après avoir entré les valeurs requises, acceptez les **Conditions générales**, puis sélectionnez **Acheter**.

  ![Envoyer un déploiement de modèle](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Étapes post-restauration
* Si vous utilisez une distribution Linux basée sur cloud-init telle qu’Ubuntu, le mot de passe est bloqué après la restauration pour des raisons de sécurité. Utilisez l’extension VMAccess sur la machine virtuelle restaurée pour [réinitialiser le mot de passe](../virtual-machines/linux/reset-password.md). Nous vous recommandons d’utiliser des clés SSH sur ces distributions pour éviter de réinitialiser le mot de passe après la restauration.
* Les extensions présentes au cours de la configuration de sauvegarde sont installées, mais pas activées. Si vous rencontrez un problème, réinstallez les extensions.
* Si la machine virtuelle sauvegardée a une IP statique, la machine virtuelle restaurée possède une adresse IP dynamique après la restauration pour éviter tout conflit lorsque vous créez une machine virtuelle restaurée. Découvrez comment [ajouter une adresse IP statique à une machine virtuelle restaurée](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* Une machine virtuelle restaurée n’a aucune valeur de disponibilité définie. Nous recommandons d’utiliser l’option de restauration des disques et [d’ajouter un groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) lorsque vous créez une machine virtuelle depuis PowerShell ou de modèles à l’aide de disques restaurés.


## <a name="backup-for-restored-vms"></a>Sauvegarde de machines virtuelles restaurées
Si vous avez restauré une machine virtuelle sur le même groupe de ressources avec le même nom que la machine virtuelle sauvegardée d’origine, la sauvegarde se poursuit sur la machine virtuelle après la restauration. Si vous avez restauré la machine virtuelle sur un groupe de ressources différent, ou que vous avez spécifié un nom différent pour la machine virtuelle restaurée, la machine virtuelle est traitée comme une nouvelle machine virtuelle. Vous devez configurer la sauvegarde pour la machine virtuelle restaurée.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Restaurer une machine virtuelle en cas de défaillance du centre de données Azure
Azure Backup permet de restaurer des machines virtuelles sauvegardées sur le centre de données associé en cas de défaillance du centre de données principal sur lequel s’exécutent les machines virtuelles et si vous avez configuré le coffre de sauvegarde pour qu’il soit géo-redondant. Pour ces scénarios, sélectionnez un compte de stockage situé dans un centre de données associé. Le reste du processus de restauration ne change pas. Azure Backup utilise le service de calcul du centre de données géo-redondant associé pour créer la machine virtuelle restaurée. Pour plus d’informations, consultez [Résilience du centre de données Azure](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Restaurer les machines virtuelles d’un contrôleur de domaine
Azure Backup prend en charge la sauvegarde de machines virtuelles d’un contrôleur de domaine. Vous devez toutefois faire preuve de prudence au cours du processus de restauration. Le processus de restauration correct dépend de la structure du domaine. Dans le cas le plus simple, vous disposez d’un seul contrôleur de domaine dans un domaine unique. Plus communément pour les charges de production, vous disposez d’un domaine unique avec plusieurs contrôleurs de domaine, dont certains sont peut-être locaux. Enfin, vous disposez peut-être d’une forêt avec plusieurs domaines.

Du point de vue d’Active Directory, la machine virtuelle Azure est semblable à toute autre machine virtuelle sur un hyperviseur moderne pris en charge. La principale différence avec les hyperviseurs locaux est qu’aucune console de machine virtuelle n’est disponible dans Azure. Une console est nécessaire pour certains scénarios tels que la récupération à l’aide d’une sauvegarde de type Récupération complète (BMR, Bare Metal Recovery). Toutefois, la restauration d’une machine virtuelle à partir du coffre de sauvegarde consiste en un remplacement total de la récupération complète. Le mode DSRM (Directory Restore Mode) étant également disponible, tous les scénarios de récupération Active Directory sont viables. Pour plus d’informations, voir les [considérations en matière de sauvegarde et de restauration pour les contrôleurs de domaine virtualisés](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) et la [planification de la récupération de forêt Active Directory](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Contrôleur de domaine unique dans un seul domaine
La machine virtuelle peut être restaurée (comme toute autre machine virtuelle) depuis le portail Azure ou à l’aide de PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Contrôleurs de domaine multiples dans un seul domaine
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. S’il s’agit du dernier contrôleur de domaine dans le domaine, ou si une récupération dans un réseau isolé est effectuée, il convient de suivre une procédure de récupération de forêt.

### <a name="multiple-domains-in-one-forest"></a>Domaines multiples dans une seule forêt
Lorsque d’autres contrôleurs de domaine du même domaine sont accessibles sur le réseau, le contrôleur de domaine peut être restauré comme n’importe quelle machine virtuelle. Dans tous les autres cas, une récupération de forêt est recommandée.

## <a name="restore-vms-with-special-network-configurations"></a>Restaurer des machines virtuelles avec des configurations réseau spéciales
Il est possible de sauvegarder et de restaurer des machines virtuelles avec les configurations réseau spéciales suivantes. Toutefois, ces configurations requièrent une attention particulière au cours du processus de restauration :

* Machines virtuelles sous des équilibreurs de charge (internes et externes)
* Machines virtuelles avec plusieurs adresses IP réservées
* Machines virtuelles avec plusieurs cartes d'interface réseau

> [!IMPORTANT]
> Lorsque vous créez la configuration réseau spéciale pour les machines virtuelles, vous devez utiliser PowerShell pour créer des machines virtuelles à partir des disques restaurés.
>
>

Pour pouvoir recréer entièrement les machines virtuelles après les avoir restaurées sur le disque, procédez comme suit :

1. Restaurez les disques à partir d’un coffre Recovery Services à l’aide de [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Créez la configuration de machine virtuelle requise pour l’équilibreur de charge/plusieurs cartes réseau/plusieurs adresses IP réservées à l’aide des applets de commande PowerShell. Utilisez-la pour créer la machine virtuelle avec la configuration souhaitée :

   a. Créer une machine virtuelle dans le service cloud avec un [équilibreur de charge interne](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Créer une machine virtuelle pour vous connecter à [l’équilibreur de charge accessible sur Internet](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Créer une machine virtuelle avec [plusieurs cartes d’interface réseau](../virtual-machines/windows/multiple-nics.md).

   d. Créer une machine virtuelle avec [plusieurs adresses IP réservées](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous pouvez restaurer vos machines virtuelles, consultez l’article de résolution des problèmes pour en savoir plus sur les erreurs courantes relatives aux machines virtuelles. En outre, consultez l’article sur la gestion des tâches avec vos machines virtuelles.

* [Résolution des erreurs](backup-azure-vms-troubleshoot.md#restore)
* [Gestion des machines virtuelles](backup-azure-manage-vms.md)

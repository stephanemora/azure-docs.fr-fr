---
title: Sauvegarde des Disques managés Azure
description: Découvrez comment sauvegarder des Disques managés Azure sur le Portail Azure.
ms.topic: conceptual
ms.date: 05/27/2021
ms.openlocfilehash: c47499c371a9eccfd97224344a48c166d0e1f811
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653628"
---
# <a name="back-up-azure-managed-disks"></a>Sauvegarder les disques managés Azure

Cet article explique comment sauvegarder un [Disque managé Azure](../virtual-machines/managed-disks-overview.md) sur le Portail Azure.

Dans cet article, vous allez apprendre à :

- Créer un coffre de sauvegarde

- Créer une stratégie de sauvegarde

- Configurer une sauvegarde d’un disque Azure

- Exécuter un travail de sauvegarde à la demande

Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations de la sauvegarde de disque Azure, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un coffre Sauvegarde est une entité de stockage dans Azure qui contient les données de sauvegarde de diverses charges de travail plus récentes prises en charge par la Sauvegarde Azure, notamment des serveurs Azure Database pour PostgreSQL et des disques Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Les coffres Sauvegarde sont basés sur le modèle Azure Resource Manager, qui fournit des fonctionnalités améliorées pour sécuriser les données de sauvegarde.

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).
1. Tapez **Centre de sauvegarde** dans la zone de recherche.
1. Sous **Services**, sélectionnez **Centre de sauvegarde**.
1. Sur la page **Centre de sauvegarde**, sélectionnez **Coffre**.

   ![Sélection de Coffre dans le Centre de sauvegarde](./media/backup-managed-disks/backup-center.png)

1. Sur l’écran **Lancer : Créer un coffre**, sélectionnez **Coffre Sauvegarde**, puis **Continuer**.

   ![Lancer : Create vault](./media/backup-managed-disks/initiate-create-vault.png)

1. Dans l’onglet **Informations de base**, indiquez l’abonnement, le groupe de ressources, le nom du coffre Sauvegarde, la région et la redondance du stockage de sauvegarde. Continuez en sélectionnant **Vérifier + créer**. Pour plus d’informations, consultez [Création d’un coffre Sauvegarde](./backup-vault-overview.md#create-a-backup-vault).

   ![Vérifier + créer un coffre](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Création d’une stratégie de sauvegarde

1. Dans le **Coffre Sauvegarde** *DemoVault* créé à l’étape précédente, accédez à **Stratégies de sauvegarde**, puis sélectionnez **Ajouter**.

   ![Ajout d’une stratégie de sauvegarde](./media/backup-managed-disks/backup-policies.png)

1. Dans l’onglet **Informations de base**, donnez un nom à la stratégie, puis sélectionnez **Disque Azure** comme **Type de source de données**. Le coffre est déjà prérempli. Les propriétés du coffre sélectionnées sont présentées.

   >[!NOTE]
   > Bien que le coffre sélectionné puisse posséder le paramètre de redondance globale, la sauvegarde de disque Azure ne prend en charge que le magasin de données d’instantanés. Toutes les sauvegardes sont stockées dans l’un des groupes de ressources de l’abonnement. Elles ne sont pas copiées dans le stockage du coffre Sauvegarde.

   ![Sélection de Type de source de données](./media/backup-managed-disks/datasource-type.png)

1. Dans l’onglet **Stratégie de sauvegarde**, sélectionnez la fréquence de planification de la sauvegarde.

   ![Sélection de la fréquence de planification de la sauvegarde](./media/backup-managed-disks/backup-schedule-frequency.png)

   La sauvegarde de disque Azure propose plusieurs sauvegardes par jour. Si vous avez besoin de sauvegardes plus fréquentes, choisissez la fréquence de sauvegarde **Horaire**, qui offre la possibilité d’effectuer des sauvegardes à intervalles de 4, 6, 8 ou 12 heures. Les sauvegardes sont planifiées en fonction de l’intervalle **Temps** sélectionné. Par exemple, si vous sélectionnez **Toutes les 4 heures**, les sauvegardes sont effectuées à intervalles de 4 heures environ, de façon à être réparties équitablement sur la journée. Si une sauvegarde par jour suffit, choisissez la fréquence de sauvegarde **Quotidienne**. Vous pouvez alors spécifier l’heure à laquelle s’effectuent vos sauvegardes. Il est à noter qu’il s’agit de l’heure de début de la sauvegarde, et non de l’heure de fin. Le temps nécessaire pour effectuer l’opération de sauvegarde dépend de différents facteurs, notamment la taille du disque et le taux d’attrition entre les sauvegardes consécutives. Toutefois, la sauvegarde de disque Azure est une sauvegarde sans agent qui utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md) sans impact sur le niveau de performance des applications de production.

1. Dans l’onglet **Stratégie de sauvegarde**, sélectionnez les paramètres de rétention qui répondent à l’exigence d’objectif de point de récupération (RPO).

   La règle de rétention par défaut s’applique si aucune autre règle de rétention n’est spécifiée. Il est possible de modifier sa durée de rétention, mais non de la supprimer. Pour ajouter une novuelle règle de rétention, sélectionnez **Ajouter une règle de rétention**.

   ![Ajout d’une règle de rétention](./media/backup-managed-disks/add-retention-rule.png)

   Vous pouvez choisir la **première sauvegarde réussie** effectuée tous les jours ou toutes les semaines, et indiquer la durée de rétention des sauvegardes spécifiques avant leur suppression. Cette option est utile pour conserver plus longtemps des sauvegardes spécifiques du jour ou de la semaine. Toutes les autres sauvegardes fréquentes peuvent être conservées pendant une durée plus réduite.

   ![Paramètres de rétention](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >La Sauvegarde Azure pour Disques managés utilise des instantanés incrémentiels qui sont limités à 200 par disque. Pour vous permettre d’effectuer des sauvegardes à la demande en dehors des sauvegardes planifiées, la stratégie de sauvegarde limite le nombre total de sauvegardes à 180. Pour plus d’informations, consultez [Instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md#restrictions) pour les disques managés.

1. Terminez la création de la stratégie de sauvegarde en sélectionnant **Vérifier + créer**.

## <a name="configure-backup"></a>Configurer une sauvegarde

- La sauvegarde sur disque Azure ne prend en charge que la sauvegarde de niveau opérationnel. La copie des sauvegardes dans le niveau de stockage coffre n’est pas disponible actuellement. Le paramètre de redondance de stockage du coffre de sauvegarde (LRS/GRS) ne s’applique pas aux sauvegardes stockées dans le niveau opérationnel.
Les instantanés incrémentiels sont stockés dans le stockage HDD Standard, quel que soit le type de stockage du disque parent. Pour plus de fiabilité, ils sont par défaut placés dans un [stockage redondant interzone](../storage/common/storage-redundancy.md) (ZRS, Zone-Redundant Storage) dans les régions qui le prennent en charge.

- La sauvegarde sur disque Azure prend en charge la sauvegarde entre abonnements. Elle effectue la restauration avec le coffre de sauvegarde dans un abonnement et le disque source dans un autre. La sauvegarde et la restauration inter-régions, en revanche, ne sont pas prises en charge. Le coffre de sauvegarde et les disques à sauvegarder peuvent ainsi se trouver dans le même abonnement ou dans des abonnements différents. Toutefois, le disque de sauvegarde et le disque à sauvegarder doivent se trouver dans la même région.

- Vous ne pouvez pas modifier le groupe de ressources d’instantané affecté à une instance de sauvegarde lorsque vous configurez la sauvegarde d’un disque. 

Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour configurer la sauvegarde des disques managés, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur les disques sources et les groupes de ressources dans lesquels les instantanés sont créés et gérés.

Une identité managée affectée par le système est limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Pour plus d’informations, consultez [Identités managées](../active-directory/managed-identities-azure-resources/overview.md).

Voici les prérequis de la configuration de la sauvegarde de disques managés :

1. Attribuez le rôle **Lecteur de sauvegarde de disque** à l’identité managée du coffre Sauvegarde sur le disque source à sauvegarder.

   1. Accédez au disque à sauvegarder.

   1. Accédez à **Contrôle d’accès (IAM)** , puis sélectionnez **Ajouter des attributions de rôles**.

   1. Dans le volet contextuel droit, sélectionnez **Lecteur de sauvegarde de disque** dans la liste déroulante **Rôle**. Sélectionnez l’identité managée du coffre Sauvegarde, puis **Enregistrer**.

   >[!TIP]
   >Tapez le nom du coffre Sauvegarde pour sélectionner l’identité managée du coffre.

   ![Ajout d’un rôle Lecteur de sauvegarde de disque](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Attribuez le rôle **Contributeur d’instantanés de disque** à l’identité managée du coffre Sauvegarde sur le groupe de ressources dans lequel les sauvegardes sont créées et gérées par le service Sauvegarde Azure. Les instantanés de disque sont stockés dans l’un des groupes de ressources de votre abonnement. Pour permettre au service Sauvegarde Azure de créer, de stocker et de gérer des instantanés, vous devez accorder des autorisations d’accès au coffre Sauvegarde.

   **Choix du groupe de ressources pour le stockage et la gestion des instantanés :**

   - Ne sélectionnez pas le même abonnement que celui du disque source.

   - En règle générale, il est recommandé de créer un groupe de ressources dédié comme magasin de stockage d’instantanés utilisable par le service Sauvegarde Azure. Cela permet en effet de restreindre les autorisations d’accès sur le groupe de ressources, garantissant ainsi la sécurité et la facilité de gestion des données de sauvegarde.

   - Vous pouvez utiliser ce groupe de ressources pour stocker des instantanés sur plusieurs disques en cours ou en attente de sauvegarde.  

   - Il n’est pas possible de créer un instantané incrémentiel pour un disque en particulier en dehors de l’abonnement de ce disque. Par conséquent, choisissez le groupe de ressources dans le même abonnement que celui du disque à sauvegarder. En savoir plus sur les [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md#restrictions) pour les disques managés.

   - Vous ne pouvez pas modifier le groupe de ressources d’instantané affecté à une instance de sauvegarde lorsque vous configurez la sauvegarde d’un disque.

   - Au cours d’une opération de sauvegarde, le service Sauvegarde Azure crée un compte de stockage dans le groupe de ressources d’instantané où sont stockés les instantanés. Un seul compte de stockage est créé par groupe de ressources d’instantané. Il est réutilisé sur toutes les instances de sauvegarde sur disque qui utilisent le même groupe de ressources comme groupe de ressources d’instantané.
     
     - Les instantanés ne sont pas stockés dans le compte de stockage. Les instantanés incrémentiels du disque managé constituent des ressources ARM qui sont créées sur le groupe de ressources et non dans un compte de stockage. 
     
     - Le compte de stockage est utilisé pour stocker les métadonnées de chaque point de récupération. Le service Sauvegarde Azure crée un conteneur d’objets blob par instance de sauvegarde sur disque. Un objet blob de blocs est créé pour chaque point de récupération afin de stocker les métadonnées le décrivant (par exemple l’abonnement, l’ID du disque, ses attributs, etc.). Ces métadonnées occupent un petit espace (quelques Kio).
     
     - Le compte de stockage est créé avec l’option RA-GZRS si la région prend en charge la redondance de zone, et avec l’option RA-GRS sinon.<br>Si une stratégie existante arrête la création d’un compte de stockage sur l’abonnement ou le groupe de ressources avec redondance GRS, le compte de stockage est créé avec l’option LRS. Le compte de stockage créé est **v2 universel**, les objets blob de blocs étant stockés sur le niveau chaud dans le conteneur d’objets blob. 
     
     - Le nombre de points de récupération est déterminé par la stratégie de sauvegarde utilisée pour configurer la sauvegarde de l’instance de sauvegarde sur disque. Les objets blob de blocs plus anciens sont supprimés selon le processus de garbage collection, à mesure que les points de récupération les plus anciens correspondants sont élagués.
   
   - N’appliquez pas de verrous de ressources ni de stratégies au groupe de ressources d’instantané ni au compte de stockage créé par le service Sauvegarde Azure. Le service crée et gère les ressources du groupe de ressources d’instantané affecté à une instance de sauvegarde lors de la configuration de la sauvegarde d’un disque. Le compte de stockage et les ressources qu’il contient sont créés par le service. Ils ne doivent être ni supprimés ni déplacés.

     >[!NOTE]
     >Si un compte de stockage est supprimé, les sauvegardes échouent, de même que la restauration pour tous les points de récupération existants.

Pour attribuer un rôle, procédez comme suit :

   1. Accédez au groupe de ressources, par exemple *SnapshotRG*, qui se trouve dans le même abonnement que le disque à sauvegarder.

   1. Accédez à **Contrôle d’accès (IAM)** , puis sélectionnez **Ajouter des attributions de rôles**.

   1. Dans le volet contextuel droit, sélectionnez **Contributeur d’instantanés de disque** dans la liste déroulante **Rôle**. Sélectionnez l’identité managée du coffre Sauvegarde, puis **Enregistrer**.

   >[!TIP]
   >Tapez le nom du coffre Sauvegarde pour sélectionner l’identité managée du coffre.

   ![Ajout d’un rôle Contributeur d’instantanés de disque](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Vérifiez que l’identité managée du coffre Sauvegarde possède le bon ensemble d’attributions de rôles sur le disque source et le groupe de ressources servant de magasin de données d’instantanés.

   1. Accédez à **Coffre Sauvegarde -> Identité**, puis sélectionnez **Attributions de rôles Azure**.

      ![Sélection de Attributions de rôles Azure](./media/backup-managed-disks/azure-role-assignments.png)

   1. Vérifiez que le rôle, le nom de la ressource et le type de ressource s’affichent correctement.

      ![Vérification du rôle, du nom de la ressource et du type de ressource](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Même si les attributions de rôles apparaissent correctement sur le portail, l’application de l’autorisation sur l’identité managée du coffre Sauvegarde peut prendre environ 15 minutes.

1. Une fois les prérequis respectés, accédez à **Coffre Sauvegarde -> Vue d’ensemble**, puis sélectionnez **Sauvegarde** pour commencer à configurer la sauvegarde des disques.

   ![Sélection de Sauvegarde](./media/backup-managed-disks/select-backup.png)

1. Dans l’onglet **Informations de base**, sélectionnez **Disque Azure** comme type de source de source.

   ![Sélection de Disque Azure](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >La Sauvegarde Azure utilise des [instantanés incrémentiels](../virtual-machines/disks-incremental-snapshots.md#restrictions) de disques managés qui ne stockent que les modifications différentielles apportées aux disques depuis le dernier instantané sur le stockage HDD Standard, quel que soit le type de stockage du disque parent. Pour plus de fiabilité, les instantanés incrémentiels sont stockés par défaut sur un stockage redondant interzone (ZRS) dans les régions qui le prennent en charge. Actuellement, la sauvegarde de disque Azure gère la sauvegarde opérationnelle des disques managés sans copie des sauvegardes dans le stockage du coffre Sauvegarde. Ainsi, le paramètre de redondance du stockage de sauvegarde du coffre Sauvegarde ne s’applique pas aux points de récupération.

1. Dans l’onglet **Stratégie de sauvegarde**, choisissez une stratégie de sauvegarde.

   ![Choisir une stratégie de sauvegarde](./media/backup-managed-disks/choose-backup-policy.png)

1. Dans l’onglet **Ressources**, sélectionnez **Sélectionner un disque Azure**. Dans le volet contextuel droit, sélectionnez les disques à sauvegarder.

   ![Sélection des disques à sauvegarder](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Le portail permet de sélectionner plusieurs disques et de configurer la sauvegarde. Cependant, chaque disque correspond à une instance de sauvegarde individuelle. Actuellement, la sauvegarde de disque Azure ne prend en charge que la sauvegarde de disques individuels. La sauvegarde dans le temps de plusieurs disques attachés à un disque virtuel n’est pas prise en charge.
   >
   >Sur le portail, seuls les disques du même abonnement peuvent être sélectionnés. Si vous avez plusieurs disques à sauvegarder ou s’ils sont répartis sur différents abonnements, vous pouvez utiliser des scripts pour automatiser l’opération.
   >
   >Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations de la sauvegarde de disque Azure, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

1. Sélectionnez un **Groupe de ressources d’instantanés**, puis **Valider**. Il s’agit du groupe de ressources dans lequel le service Sauvegarde Azure a créé et gère les instantanés incrémentiels pour le coffre Sauvegarde. Les autorisations de rôle figurant dans les prérequis sont attribuées à l’identité managée.

   ![Sélection du Groupe de ressources d’instantanés](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >La validation peut prendre quelques minutes avant qu’il ne soit possible de terminer la configuration du workflow de sauvegarde. La validation peut échouer dans les cas suivants :
   >
   > - Un disque n’est pas pris en charge. Pour connaître les scénarios non pris en charge, consultez [Matrice de prise en charge](disk-backup-support-matrix.md).
   > - L’identité managée du coffre Sauvegarde ne dispose pas d’attributions de rôles valides sur le **disque** à sauvegarder ou sur le **Groupe de ressources d’instantanés** dans lequel sont stockés les instantanés incrémentiels.

1. Une fois la validation réussie, sélectionnez **Vérifier et configurer** pour configurer la sauvegarde des disques sélectionnés.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

1. Dans le **Coffre Sauvegarde** *DemoVault* créé à l’étape précédente, accédez à **Instances de sauvegarde**, puis sélectionnez une instance de sauvegarde.

   ![Sélection d’une instance de sauvegarde](./media/backup-managed-disks/select-backup-instance.png)

1. L’écran **Instances de sauvegarde** comporte différentes informations :

   - **Informations essentielles**, notamment le nom du disque source, le groupe de ressources d’instantanés dans lequel sont stockés les instantanés incrémentiels, le coffre Sauvegarde et la stratégie de sauvegarde
   - **État du travail** présentant un résumé des opérations de sauvegarde et de restauration, ainsi que leur état au cours des sept derniers jours
   - Liste des **points de restauration** pour la période sélectionnée

1. Sélectionnez **Sauvegarder** pour lancer une sauvegarde à la demande.

   ![Sélectionner Sauvegarder maintenant](./media/backup-managed-disks/backup-now.png)

1. Sélectionnez l’une des règles de rétention associées à la stratégie de sauvegarde. Elle détermine la durée de rétention de cette sauvegarde à la demande. Sélectionnez **Sauvegarder maintenant** pour commencer la sauvegarde.

   ![Lancement de la sauvegarde](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Suivi d’une opération de sauvegarde

Le service Sauvegarde Azure crée un travail pour les sauvegardes planifiées ou en cas de déclenchement d’une opération de sauvegarde à la demande pour le suivi. Pour afficher l’état de la sauvegarde, procédez comme suit :

1. Accédez à l’écran **Instance de sauvegarde**. Il affiche le tableau de bord des travaux avec l’opération et l’état des sept derniers jours.

   ![Tableau de bord des travaux](./media/backup-managed-disks/jobs-dashboard.png)

1. Pour voir l’état de l’opération de sauvegarde, sélectionnez **Tout afficher** afin de faire apparaître les travaux en cours et passés de cette instance de sauvegarde.

   ![Sélection de Tout afficher](./media/backup-managed-disks/view-all.png)

1. Passez en revue la liste des travaux de sauvegarde et de restauration, ainsi que leur état. Sélectionnez un travail dans la liste des travaux pour en afficher les détails.

   ![Sélection du travail pour afficher les détails](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Étapes suivantes

- [Restauration de Disques managés Azure](restore-managed-disks.md)
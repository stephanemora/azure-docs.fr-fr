---
title: Sauvegarde des Disques managés Azure
description: Découvrez comment sauvegarder des Disques managés Azure sur le Portail Azure.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 6da98c8229d626edb5f1872ede5380f9cc61aa0b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224377"
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

- La sauvegarde de disque Azure prend en charge uniquement la sauvegarde de niveau opérationnel. La copie des sauvegardes dans le niveau de stockage du coffre n’est pas actuellement prise en charge. Le paramètre de redondance de stockage du coffre de sauvegarde (LRS/GRS) ne s’applique pas aux sauvegardes stockées dans le niveau opérationnel. 
  Les instantanés incrémentiels sont stockés dans un stockage HDD Standard, quel que soit le type de stockage du disque parent sélectionné. Pour plus de fiabilité, ils sont par défaut placés dans un [stockage redondant interzone](../storage/common/storage-redundancy.md) (ZRS) dans les régions qui le prennent en charge.

- La sauvegarde sur disque Azure prend en charge la sauvegarde et la restauration entre abonnements avec le coffre de sauvegarde dans un abonnement et le disque source dans un autre. La sauvegarde et la restauration inter-régions ne sont donc pas prises en charge, ce qui permet au coffre Sauvegarde et au disque d’être sauvegardés dans des abonnements identiques ou différents. Toutefois, le coffre de sauvegarde et le disque à sauvegarder doivent se trouver dans la même région.

- Une fois que vous avez configuré la sauvegarde d’un disque, vous ne pouvez pas modifier le groupe de ressources d’instantanés affecté à une instance de sauvegarde.

Pour configurer une sauvegarde de disque, effectuez les étapes suivantes :

1. Accédez à **Centre de sauvegarde** -> **Vue d’ensemble**, puis cliquez sur **+ Sauvegarde** pour démarrer la configuration de la sauvegarde du disque.

   :::image type="content" source="./media/backup-managed-disks/start-configuring-backup-of-disk-inline.png" alt-text="Capture d’écran montrant l’option de démarrage de la sauvegarde d’un disque Azure." lightbox="./media/backup-managed-disks/start-configuring-backup-of-disk-expanded.png":::

1. Sélectionnez **Disques Azure** dans la liste déroulante **Type de source de données**, puis cliquez sur **Continuer**.

   :::image type="content" source="./media/backup-managed-disks/select-azure-disks-as-datasource-type-inline.png" alt-text="Capture d’écran montrant le processus de sélection des disques Azure comme type de protection des données." lightbox="./media/backup-managed-disks/select-azure-disks-as-datasource-type-expanded.png":::

1. Sélectionnez un coffre de sauvegarde, puis cliquez sur **Suivant** pour continuer.

   >[!Note]
   >- Vérifiez que le coffre de sauvegarde et le disque à sauvegarder se trouvent au même emplacement.
   >- Sauvegarde Azure utilise des [_instantanés incrémentiels_](../virtual-machines/disks-incremental-snapshots.md#restrictions) de disques managés qui ne stockent que les modifications différentielles apportées aux disques depuis le dernier instantané sur le stockage HDD Standard, quel que soit le type de stockage du disque parent. Pour plus de fiabilité, ils sont par défaut placés dans un stockage redondant interzone (ZRS) dans les régions qui le prennent en charge. Actuellement, la sauvegarde de disque Azure gère la sauvegarde opérationnelle des disques managés sans copie des sauvegardes dans le stockage du coffre Sauvegarde. Ainsi, le paramètre de redondance du stockage de sauvegarde du coffre Sauvegarde ne s’applique pas aux points de récupération.

   :::image type="content" source="./media/backup-managed-disks/select-backup-vault-inline.png" alt-text="Capture d’écran montrant le processus de sélection d’un coffre Sauvegarde." lightbox="./media/backup-managed-disks/select-backup-vault-expanded.png":::

1. Sous l’onglet **Stratégie de sauvegarde**, choisissez une stratégie de sauvegarde.

   :::image type="content" source="./media/backup-managed-disks/choose-backup-policy-inline.png" alt-text="Capture d’écran montrant le processus de sélection d’une stratégie de sauvegarde." lightbox="./media/backup-managed-disks/choose-backup-policy-expanded.png":::

1. Sous l’onglet **Sources de données**, cliquez sur **+ Ajouter/modifier** pour choisir un ou plusieurs disques managés Azure pour lesquels vous souhaitez configurer la sauvegarde.

   :::image type="content" source="./media/backup-managed-disks/choose-azure-managed-disks-inline.png" alt-text="Capture d’écran montrant le processus de sélection de disques managés Azure." lightbox="./media/backup-managed-disks/choose-azure-managed-disks-expanded.png":::

   >[!Note]
   >Le portail permet de sélectionner plusieurs disques et de configurer la sauvegarde. Cependant, chaque disque correspond à une instance de sauvegarde individuelle. Actuellement, la sauvegarde de disque Azure ne prend en charge que la sauvegarde de disques individuels. La sauvegarde dans le temps de plusieurs disques attachés à une machine virtuelle n’est pas prise en charge.
   >
   >Dans le portail Azure, vous pouvez uniquement sélectionner des disques figurant dans le même abonnement. Si vous avez plusieurs disques à sauvegarder ou s’ils résident dans différents abonnements, vous pouvez utiliser des scripts ([PowerShell](./backup-managed-disks-ps.md)/[CLI](./backup-managed-disks-cli.md)) pour automatiser l’opération. 
   >
   >Pour plus d’informations sur la disponibilité par région, les scénarios pris en charge et les limitations de la sauvegarde de disque Azure, consultez la [matrice de prise en charge](./disk-backup-support-matrix.md).

1. Sélectionnez un **groupe de ressources d’instantanés** et cliquez sur **Valider** pour lancer les vérifications des prérequis.

   Choix du groupe de ressources pour le stockage et la gestion des instantanés :

   - Ne sélectionnez pas le même abonnement que celui du disque source.
   
   - En règle générale, il est recommandé de créer un groupe de ressources dédié comme magasin de stockage d’instantanés utilisable par le service Sauvegarde Azure. Cela permet en effet de restreindre les autorisations d’accès sur le groupe de ressources, garantissant ainsi la sécurité et la facilité de gestion des données de sauvegarde.

   - Vous pouvez utiliser ce groupe de ressources pour stocker des instantanés sur plusieurs disques en cours ou en attente de sauvegarde.

   - Il n’est pas possible de créer un instantané incrémentiel pour un disque en particulier en dehors de l’abonnement de ce disque. Choisissez par conséquent le groupe de ressources figurant dans le même abonnement que celui du disque à sauvegarder. [Apprenez-en davantage](../virtual-machines/disks-incremental-snapshots.md#restrictions) sur les instantanés incrémentiels pour les disques managés.

   - Une fois que vous avez configuré la sauvegarde d’un disque, vous ne pouvez pas modifier le groupe de ressources d’instantanés affecté à une instance de sauvegarde.

   - Au cours d’une opération de sauvegarde, le service Sauvegarde Azure crée un compte de stockage dans le groupe de ressources d’instantanés. Un seul compte de stockage est créé par groupe de ressources d’instantanés. Il est réutilisé sur toutes les instances de sauvegarde de disque qui utilisent le même groupe de ressources comme groupe de ressources d’instantanés.

     - Le compte de stockage ne stocke pas les instantanés. Les instantanés incrémentiels du disque managé sont des ressources ARM créées sur le groupe de ressources et non dans un compte de stockage.
     - Le compte de stockage stocke les métadonnées pour chaque point de récupération. Le service Sauvegarde Azure crée un conteneur d’objets blob par instance de sauvegarde de disque. Un objet blob de blocs est créé pour chaque point de récupération afin de stocker les métadonnées le décrivant (par exemple l’abonnement, l’ID du disque, ses attributs, etc.). Ces métadonnées occupent un petit espace (quelques Kio).
     - Le compte de stockage est créé avec l’option RA-GZRS si la région prend en charge la redondance de zone, et avec l’option RA-GRS si ce n’est pas le cas.
       Si une stratégie existante arrête la création d’un compte de stockage sur l’abonnement ou le groupe de ressources avec redondance GRS, le compte de stockage est créé avec l’option LRS. Le compte de stockage créé est **Usage général v2**, les objets blob de blocs étant stockés sur le niveau chaud dans le conteneur d’objets blob.
     - Le nombre de points de récupération est déterminé par la stratégie de sauvegarde utilisée pour configurer la sauvegarde de l’instance de sauvegarde sur disque. Selon le processus de garbage collection, les objets blob de blocs plus anciens sont supprimés à mesure que les points de récupération les plus anciens correspondants sont élagués.

   - N’appliquez aucun verrou de ressource, aucune stratégie et aucun pare-feu au groupe de ressources d’instantanés ou au compte de stockage créé par le service Sauvegarde Azure. Le service crée et gère les ressources du groupe de ressources d’instantanés affecté à une instance de sauvegarde lors de la configuration d’une sauvegarde de disque. Le service crée le compte de stockage et ses ressources. Ne les supprimez pas et ne les déplacez pas.

     >[!Note]
     >Si un compte de stockage est supprimé, les sauvegardes échouent, de même que la restauration pour tous les points de récupération existants.

   :::image type="content" source="./media/backup-managed-disks/validate-snapshot-resource-group-inline.png" alt-text="Capture d’écran montrant le processus de lancement des vérifications des prérequis." lightbox="./media/backup-managed-disks/validate-snapshot-resource-group-expanded.png":::

1. Une fois la validation terminée, vérifiez si des erreurs sont signalées dans la colonne Préparation de la sauvegarde.

   >[!Note]
   >La validation peut prendre quelques minutes. La validation peut échouer dans les cas suivants :
   >
   >- Un disque n’est pas pris en charge. Pour connaître les scénarios non pris en charge, consultez la [matrice de prise en charge](./disk-backup-support-matrix.md).
   >- L’identité managée du coffre de sauvegarde ne dispose pas d’attributions de rôles valides sur le _disque_ à sauvegarder ou sur le _groupe de ressources d’instantanés_ dans lequel sont stockés les instantanés incrémentiels.

   Si le message d’erreur « _Attribution de rôle non effectuée_ » apparaît dans la colonne **Préparation de la sauvegarde**, l’identité managée du coffre Sauvegarde a besoin d’autorisations de rôle sur le ou les disques sélectionnés et/ou sur le groupe de ressources d’instantanés. 

   :::image type="content" source="./media/backup-managed-disks/role-assignment-not-done-error-inline.png" alt-text="Capture d’écran montrant le message d’erreur Attribution de rôle non effectuée." lightbox="./media/backup-managed-disks/role-assignment-not-done-error-expanded.png":::

   Pour configurer la sauvegarde de disques managés, les prérequis sont les suivants :

   >[!Note]
   >Le coffre Sauvegarde utilise l’identité managée pour accéder à d’autres ressources Azure. Il est nécessaire, pour configurer la sauvegarde des disques managés, que l’identité managée du coffre Sauvegarde dispose d’un ensemble d’autorisations sur les disques sources et les groupes de ressources dans lesquels les instantanés sont créés et gérés.

   Une identité managée affectée par le système est limitée à une par ressource et liée au cycle de vie de celle-ci. Pour accorder des autorisations à l’identité managée, utilisez le contrôle d’accès en fonction du rôle (RBAC) Azure. L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Découvrez-en plus sur les [identités managées](../active-directory/managed-identities-azure-resources/overview.md).

   - Attribuez le rôle **Lecteur de sauvegarde de disque** à l’identité managée du coffre Sauvegarde sur le disque source à sauvegarder.
   - Attribuez le rôle Contributeur d’instantanés de disque à l’identité managée du coffre Sauvegarde sur le groupe de ressources dans lequel les sauvegardes sont créées et gérées par le service Sauvegarde Azure. Les instantanés de disque sont stockés dans l’un des groupes de ressources de votre abonnement. Pour permettre au service Sauvegarde Azure de créer, de stocker et de gérer des instantanés, vous devez accorder des autorisations d’accès au coffre Sauvegarde.

   >[!Note]
   >Le flux Configurer la sauvegarde avec le portail Azure vous aide à accorder les autorisations de rôle nécessaires aux ressources ci-dessus. 

1. Cochez la case en regard de chaque ligne contenant le message d’erreur _Attribution de rôle non effectuée_ dans la colonne Préparation de la sauvegarde, puis cliquez sur **Ajouter les rôles manquants** pour accorder automatiquement les autorisations de rôle nécessaires pour l’identité managée du coffre Sauvegarde sur les ressources sélectionnées.

   :::image type="content" source="./media/backup-managed-disks/add-missing-roles-inline.png" alt-text="Capture d’écran montrant le processus d’ajout des rôles manquants." lightbox="./media/backup-managed-disks/add-missing-roles-expanded.png":::

1. Cliquez sur **Confirmer** pour donner votre consentement. Sauvegarde Azure propage automatiquement les modifications d’attribution de rôle en votre nom et tente une revalidation.

   Si vous souhaitez accorder l’autorisation pour l’identité managée du coffre Sauvegarde au(x) disque(s) sélectionné(s) et au groupe de ressources d’instantanés, sélectionnez **Ressource** dans la liste déroulante **Étendue**. 

   :::image type="content" source="./media/backup-managed-disks/confirm-role-propagation-inline.png" alt-text="Capture d’écran montrant l’option permettant de donner son consentement à Sauvegarde Azure pour l’attribution automatique de rôle." lightbox="./media/backup-managed-disks/confirm-role-propagation-expanded.png":::

   >[!Tip]
   >À l’avenir, si vous envisagez de configurer la sauvegarde pour d’autres disques dans le même groupe de ressources/abonnement, vous pouvez choisir de fournir une autorisation au niveau du groupe de ressources ou de l’abonnement.

   :::image type="content" source="./media/backup-managed-disks/permission-deployment-in-progress-inline.png" alt-text="Capture d’écran montrant le déploiement de l’autorisation." lightbox="./media/backup-managed-disks/permission-deployment-in-progress-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/permission-waiting-to-propagate-inline.png" alt-text="Capture d’écran montrant l’autorisation en attente de propagation vers le groupe de ressources." lightbox="./media/backup-managed-disks/permission-waiting-to-propagate-expanded.png":::

   :::image type="content" source="./media/backup-managed-disks/revalidating-permission-propagation-inline.png" alt-text="Capture d’écran montrant les tentatives de revalidation de la propagation des autorisations pour les scénarios ayant échoué." lightbox="./media/backup-managed-disks/revalidating-permission-propagation-expanded.png":::

   >[!Note]
   >- Dans certains cas, la propagation des attributions de rôle peut prendre jusqu’à 30 minutes, provoquant l’échec de la revalidation. Dans ce scénario, recommencez l’opération plus tard.
   >- Si l’action **Ajouter les rôles manquants** ne parvient pas à attribuer des autorisations et génère l’erreur « Autorisation insuffisante pour l’attribution de rôle » dans la colonne Préparation de la sauvegarde, cela signifie que vous n’avez pas le privilège d’attribuer des autorisations de rôle. Choisissez Télécharger le modèle d’attribution de rôle pour télécharger les attributions de rôle en tant que scripts et demandez à votre administrateur informatique de vous aider à les exécuter pour répondre aux prérequis. 

   :::image type="content" source="./media/backup-managed-disks/permission-propagation-taking-long-time-inline.png" alt-text="Capture d’écran montrant que les instances de propagation d’autorisation prennent plus de temps, jusqu’à 30 secondes." lightbox="./media/backup-managed-disks/permission-propagation-taking-long-time-expanded.png":::

1. Une fois la validation réussie, cliquez sur **Suivant** pour passer à l’onglet **Vérifier et configurer**, puis cliquez sur **Configurer la sauvegarde** pour configurer la sauvegarde des disques sélectionnés.

   :::image type="content" source="./media/backup-managed-disks/configure-backup-of-selected-disks-inline.png" alt-text="Capture d’écran montrant le processus de configuration de la sauvegarde des disques sélectionnés." lightbox="./media/backup-managed-disks/configure-backup-of-selected-disks-expanded.png":::

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
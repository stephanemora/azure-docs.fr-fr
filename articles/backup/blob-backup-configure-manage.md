---
title: Configurer une sauvegarde opérationnelle pour des objets blob Azure
description: Découvrez comment configurer et gérer une sauvegarde opérationnelle pour des objets blob Azure (préversion)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051070"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Configurer une sauvegarde opérationnelle pour des objets blob Azure (préversion)

Le service Sauvegarde Azure vous permet de configurer facilement une sauvegarde opérationnelle pour la protection d’objets blob de blocs dans vos comptes de stockage. Cet article explique comment configurer une sauvegarde opérationnelle sur un ou plusieurs comptes de stockage à l’aide du portail Azure. Cet article aborde les points suivants :

- À savoir avant de commencer
- Création d’un coffre de sauvegarde
- Octroi d’autorisations au coffre de sauvegarde sur les comptes de stockage à protéger
- Création d’une stratégie de sauvegarde
- Configuration d’une sauvegarde opérationnelle sur un ou plusieurs comptes de stockage
- Effets sur les comptes de stockage de sauvegarde

## <a name="before-you-start"></a>Avant de commencer

- La sauvegarde opérationnelle d’objets blob est une solution de sauvegarde locale qui conserve des données pendant une durée spécifiée dans le compte de stockage source proprement dit. Cette solution ne gère pas une copie supplémentaire de données dans le coffre.
- Cette solution vous permet de conserver vos données à des fins de restauration pendant jusqu’à 360 jours. Toutefois, des durées de conservation longues peuvent allonger l’opération de restauration.
- Vous pouvez utiliser la solution pour effectuer des restaurations uniquement sur le compte de stockage source et cela peut entraîner un remplacement de données.
- Si vous supprimez un conteneur du compte de stockage en appelant l’opération Supprimer le conteneur, ce conteneur ne peut pas être restauré à l’aide d’une opération de restauration. Au lieu de supprimer un conteneur entier, supprimez chacun des objets blob si vous souhaitez les restaurer plus tard. En outre, Microsoft recommande d’activer la suppression réversible pour les conteneurs, en plus de la sauvegarde opérationnelle, pour éviter une suppression accidentelle de conteneurs.
- Pour en savoir plus sur les scénarios, les limitations et la disponibilité pris en charge, consultez la [matrice de prise en charge](blob-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Créer un coffre de sauvegarde

Un [coffre de sauvegarde](backup-vault-overview.md) est une entité de gestion qui stocke des points de récupération créés au fil du temps, et fournit une interface permettant d’effectuer des opérations liées à la sauvegarde. Cela inclut l’exécution de sauvegardes à la demande, l’exécution de restaurations et la création de stratégies de sauvegarde. Si la sauvegarde opérationnelle d’objets blob est une sauvegarde locale et ne stocke pas de données dans le coffre, celui-ci est requis pour diverses opérations de gestion.

>[!NOTE]
>Le coffre de sauvegarde est une nouvelle ressource utilisée pour sauvegarder de nouvelles charges de travail prises en charge. Il diffère du coffre de Recovery Services existant.

Pour obtenir des instructions sur la création d’un coffre de sauvegarde, consultez la [documentation sur le coffre de sauvegarde](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Accorder au coffre de sauvegarde des autorisations sur des comptes de stockage

Une sauvegarde opérationnelle protège également le compte de stockage (contenant les objets blob à protéger) contre des suppressions accidentelles en appliquant un verrou de suppression détenu par le service Sauvegarde. Cela nécessite que le coffre de sauvegarde dispose de certaines autorisations sur les comptes de stockage à protéger. Pour des raisons de commodité d’utilisation, ces autorisations ont été consolidées dans le rôle contributeur de sauvegarde de compte de stockage. Suivez les instructions ci-dessous pour les comptes de stockage à protéger :

1. Dans le compte de stockage à protéger, dans le volet de navigation gauche, accédez à l’**onglet Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter des attributions de rôles** pour attribuer le rôle requis.

    ![Ajouter des attributions de rôle](./media/blob-backup-configure-manage/add-role-assignments.png)

1. Dans le volet Ajouter une attribution de rôle :

    1. Sous **Rôle**, choisissez **Contributeur de stockage de compte de stockage**.
    1. Sous **Attribuer l’accès à**, choisissez **Utilisateur, groupe ou principal de service**.
    1. Tapez le **nom du coffre de sauvegarde** donc vous souhaitez protéger les objets blob dans ce compte de stockage, puis sélectionnez-le dans les résultats de recherche.
    1. Cela fait, sélectionnez **Enregistrer**.

        ![Options d’attribution de rôle](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Laissez s’écouler jusqu’à 10 minutes pour que l’attribution de rôle prenne effet.

## <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde régit généralement la rétention et la planification de vos sauvegardes. Étant donné que la sauvegarde opérationnelle d’objets blob est continue par nature, vous n’avez pas besoin d’une planification pour effectuer des sauvegardes. La stratégie est essentiellement nécessaire pour spécifier la période de rétention. Vous pouvez utiliser et réutiliser la stratégie de sauvegarde afin de configurer la sauvegarde de plusieurs comptes de stockage dans un coffre.

Voici les étapes de création d’une stratégie de sauvegarde pour la sauvegarde opérationnelle de vos objets blob :

1. Dans votre coffre de sauvegarde, accédez à **stratégies de sauvegarde**, puis sélectionnez **+Ajouter** pour démarrer la création d’une stratégie de sauvegarde.

    ![Stratégies de sauvegarde](./media/blob-backup-configure-manage/backup-policies.png)

1. Sous l’onglet **De base**, entrez un nom pour votre stratégie de sauvegarde, puis sélectionnez **Objets blob Azure** comme type de source de données. Vous pouvez également afficher les détails de votre coffre sélectionné.

    ![Créer la stratégie de sauvegarde](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Même si vous voyez la **Redondance du stockage de sauvegarde** du coffre, celle-ci ne s’applique pas vraiment à la sauvegarde opérationnelle d’objets blob, car la sauvegarde est locale par nature et aucune donnée n’est stockée dans le coffre de sauvegarde. Le coffre de sauvegarde est ici l’entité de gestion destinée à vous aider à gérer la protection des objets blob de blocs dans vos comptes de stockage.

1. L’onglet **Stratégie de sauvegarde** est l’emplacement dans lequel vous spécifiez les détails de la rétention. Vous verrez qu’il existe déjà une règle de rétention appelée **Par défaut** avec une période de rétention de 30 jours. Si vous souhaitez modifier la durée de rétention, utilisez l’icône **Modifier la règle de conservation** afin de modifier et spécifier la durée pendant laquelle les données doivent être conservées. Vous pouvez spécifier une durée de rétention jusqu’à 360 jours.

    ![Onglet Stratégie de sauvegarde](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Restaurer sur de longues durées peut conduire à des opérations de restauration plus longues. Par ailleurs, le temps nécessaire à la restauration d’un jeu de données dépend du nombre d’opérations d’écriture et de suppression effectuées au cours de la période de restauration. Par exemple, la restauration à un point situé 30 jours auparavant d’un compte avec 1 million d’objets, 3 000 objets ajoutés par jour et 1 000 objets supprimés par jour nécessite environ deux heures. Une période de rétention et une restauration à plus de 90 jours dans le passé ne sont pas recommandées pour un compte avec ce taux de changement.

1. Dans le volet **Vérifier + créer**, vérifiez tous les détails de la stratégie, puis sélectionnez **Créer** une fois que vous avez fini de créer la stratégie. Une notification confirme que la stratégie de sauvegarde a été créée et qu’elle est prête à être utilisée.

    ![Vérifier et créer une stratégie](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Configurer une sauvegarde

Une sauvegarde d’objets blob est configurée au niveau du compte de stockage. Ainsi, tous les objets blob du compte de stockage sont protégés par une sauvegarde opérationnelle.

Pour commencer à configurer une sauvegarde :

1. Tapez **Centre de sauvegarde** dans la zone de recherche.
1. Accédez à **Vue d’ensemble** ->  **+Sauvegarde**.

    ![Vue d’ensemble du Centre de sauvegarde](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Sous l’onglet **Lancer : Configurer la sauvegarde**, choisissez **Objets blob Azure (Stockage Azure)** comme type de source de données.

    ![Onglet Lancer : Configurer la sauvegarde](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Sous l’onglet **De base**, spécifiez **Objets blob Azure (Stockage Azure)** comme type de **source de source**, puis sélectionnez le coffre de sauvegarde auquel vous souhaitez associer vos comptes de stockage. Vous pouvez afficher les détails du coffre sélectionné dans le volet.

    ![Onglet Informations de base](./media/blob-backup-configure-manage/basics-tab.png)

1. Ensuite, sélectionnez la stratégie de sauvegarde à utiliser pour spécifier la rétention. Vous pouvez afficher les détails de la stratégie sélectionnée dans la partie inférieure de l’écran. La colonne du magasin de données opérationnelles affiche la rétention définie dans la stratégie. « Opérationnelles » signifie que les données sont conservées localement dans le compte de stockage source proprement dit.

    ![Choisir une stratégie de sauvegarde](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Pour créer une stratégie de sauvegarde. Pour ce faire, sélectionnez **Créer** et suivez les étapes ci-dessous :

    1. Spécifiez un nom pour la stratégie à créer. Vérifiez que les autres zones affichent le type de source de données et le nom de coffre corrects.
    1. Sous l’onglet **Stratégie de sauvegarde**, sélectionnez l’icône Modifier la règle de conservation pour modifier et spécifier la durée pendant laquelle vous voulez que les données soient conservées. Vous pouvez spécifier une durée de rétention jusqu’à 360 jours. Restaurer sur de longues durées peut conduire à des opérations de restauration plus longues.

        ![Créer une stratégie de sauvegarde](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Une fois que vous avez terminé, sélectionnez **Vérifier + créer** pour créer la stratégie de sauvegarde.

1. Ensuite, vous devez choisir les comptes de stockage pour lesquels vous souhaitez configurer la protection d’objets blob. Vous pouvez sélectionner plusieurs comptes de stockage à la fois, puis choisir **Sélectionner**.

    Toutefois, assurez-vous que le coffre que vous avez choisi dispose des autorisations requises pour configurer la sauvegarde sur les comptes de stockage comme indiqué ci-dessus dans [Accorder au coffre de sauvegarde des autorisations sur des comptes de stockage](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Sélectionner les ressources à sauvegarder](./media/blob-backup-configure-manage/select-resources.png)

    Le service Sauvegarde vérifie si le coffre dispose d’autorisations suffisantes pour permettre la configuration d’une sauvegarde sur les comptes de stockage sélectionnés.

    ![Le service Sauvegarde valide les autorisations.](./media/blob-backup-configure-manage/validate-permissions.png)

    Si la validation génère des erreurs (comme avec l’un des comptes de stockage dans la capture d’écran), accédez aux comptes de stockage sélectionnés et attribuez les rôles appropriés, comme indiqué [ici](#grant-permissions-to-the-backup-vault-on-storage-accounts), puis sélectionnez **Revalider**. Jusqu’à 10 minutes peuvent s’écouler avant que la nouvelle attribution de rôle prenne effet.

1. Une fois la validation effectuée pour tous les comptes de stockage sélectionnés, allez à **Vérifier et configurer** pour configurer la sauvegarde. Vous verrez des notifications vous informant de l’état de la configuration de la protection et de son achèvement.

## <a name="effects-on-backed-up-storage-accounts"></a>Effets sur les comptes de stockage sauvegardés

Une fois la sauvegarde configurée, les modifications qui se produisent sur des objets blob de blocs dans les comptes de stockage sont suivies, et les données sont conservées conformément à la stratégie de sauvegarde. Vous remarquerez les modifications suivantes dans les comptes de stockage pour lesquels la sauvegarde est configurée :

- Les fonctionnalités suivantes sont activées sur le compte de stockage. Celles-ci peuvent être affichées sous l’onglet **Protection des données** du compte de stockage.
  - Limite de restauration dans le temps pour les conteneurs : avec rétention telle que spécifiée dans la stratégie de sauvegarde
  - Suppression réversible pour les objets blob : avec rétention telle que spécifiée dans la stratégie de sauvegarde + 5 jours
  - Contrôle de version des objets blob
  - Flux de modification d’objet blob

  Si le compte de stockage configuré pour la sauvegarde avait déjà les options **Limite de restauration dans le temps pour les conteneurs** ou **Suppression réversible pour les objets blob** activées (avant la configuration de la sauvegarde), le service Sauvegarde vérifie que la rétention est au moins telle que définie dans la stratégie de sauvegarde. Par conséquent, pour chaque propriété :

  - Si la rétention dans la stratégie de sauvegarde est supérieure à la rétention initialement présente dans le compte de stockage, la rétention sur le compte de stockage est modifiée en fonction de la stratégie de sauvegarde.
  - Si la rétention dans la stratégie de sauvegarde est inférieure à la rétention initialement présente dans le compte de stockage, la rétention sur le compte de stockage reste définie sur la durée définie à l’origine.

  ![Onglet Protection des données](./media/blob-backup-configure-manage/data-protection.png)

- Le service Sauvegarde applique un **verrou de suppression** sur le compte de stockage protégé. Le verrou est destiné à protéger contre les cas de suppression accidentelle du compte de stockage. Vous pouvez l’afficher sous **Compte de stockage** > **Verrous**.

    ![Verrous de suppression](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Gérer une sauvegarde opérationnelle

Vous pouvez utiliser le Centre de sauvegarde comme volet unique pour gérer toutes vos sauvegardes. En ce qui concerne la sauvegarde opérationnelle pour les objets blob Azure, vous pouvez utiliser le Centre de sauvegarde pour effectuer les opérations suivantes :

- Comme nous l’avons vu ci-dessus, vous pouvez l’utiliser pour créer des coffres et des stratégies de sauvegarde. Vous pouvez également afficher l’ensemble des coffres et stratégies dans les abonnements sélectionnés.
- Le Centre de sauvegarde vous offre un moyen simple de surveiller l’état de protection des comptes de stockage protégés, ainsi que les comptes de stockage pour lesquels la sauvegarde n’est pas actuellement configurée.
- Vous pouvez configurer une sauvegarde pour tout compte de stockage à l’aide du bouton **+Sauvegarde** .
- Vous pouvez lancer des restaurations à l’aide du bouton **Restaurer** et effectuer le suivi des restaurations à l’aide de **Travaux de sauvegarde**. Pour plus d’informations sur l’exécution de restaurations, consultez [Restaurer des objets blob Azure](blob-backup-support-matrix.md).
- Analysez votre utilisation de la sauvegarde à l’aide de rapports de sauvegarde.

    ![Centre de sauvegarde](./media/blob-backup-configure-manage/backup-center.png)

Pour plus d’informations, consultez [Vue d’ensemble du Centre de sauvegarde (préversion)](backup-center-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer des objets blob Azure](blob-restore.md)

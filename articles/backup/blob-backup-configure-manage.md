---
title: Configurer une sauvegarde opérationnelle pour des objets blob Azure
description: Découvrez comment configurer et gérer une sauvegarde opérationnelle pour des objets blob Azure.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: cb2bc525018b33eb3441a8ed949d3e808c5051d8
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108767407"
---
# <a name="configure-operational-backup-for-azure-blobs"></a>Configurer une sauvegarde opérationnelle pour des objets blob Azure

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

Une sauvegarde opérationnelle protège également le compte de stockage (contenant les objets blob à protéger) contre des suppressions accidentelles en appliquant un verrou de suppression détenu par le service Sauvegarde. Cela nécessite que le coffre de sauvegarde dispose de certaines autorisations sur les comptes de stockage à protéger. Pour des raisons de commodité d’utilisation, ces autorisations minimales ont été consolidées dans le rôle **contributeur de sauvegarde de compte de stockage**. 

Nous vous recommandons d’attribuer ce rôle au coffre de sauvegarde avant de configurer la sauvegarde. Toutefois, vous pouvez également effectuer l’attribution de rôle lors de la configuration de la sauvegarde. [En savoir plus](#using-backup-center) sur la configuration de la sauvegarde à l’aide du centre de sauvegarde. 

Pour affecter le rôle requis pour les comptes de stockage que vous devez protéger, procédez comme suit :

>[!NOTE]
>Vous pouvez également attribuer les rôles au coffre au niveau de l’abonnement ou du groupe de ressources selon ce qui vous convient le mieux.

1. Dans le compte de stockage à protéger, dans le volet de navigation gauche, accédez à l’**onglet Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter des attributions de rôles** pour attribuer le rôle requis.

    ![Ajouter des attributions de rôle](./media/blob-backup-configure-manage/add-role-assignments.png)

1. Dans le volet Ajouter une attribution de rôle :

    1. Sous **Rôle**, choisissez **Contributeur de stockage de compte de stockage**.
    1. Sous **Attribuer l’accès à**, choisissez **Utilisateur, groupe ou principal de service**.
    1. Recherchez le coffre de sauvegarde que vous souhaitez utiliser pour la sauvegarde des objets blob dans ce compte de stockage, puis sélectionnez-le dans les résultats de recherche.
    1. Sélectionnez **Enregistrer**.

        ![Options d’attribution de rôle](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Jusqu’à 10 minutes peuvent s’écouler avant que l’attribution de rôle prenne effet.

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

Vous pouvez configurer une sauvegarde pour plusieurs comptes de stockage à l’aide du Centre de sauvegarde. Vous pouvez également configurer la sauvegarde pour un compte de stockage à l’aide des propriétés de **protection des données** du compte de stockage. Cette section décrit les deux façons de configurer la sauvegarde.

### <a name="using-backup-center"></a>Utiliser le Centre de sauvegarde

Pour commencer à configurer une sauvegarde :

1. Tapez **Centre de sauvegarde** dans la zone de recherche.

1. Accédez à **Vue d’ensemble** ->  **+Sauvegarde**.

    ![Vue d’ensemble du Centre de sauvegarde](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Sous l’onglet **Lancer : Configurer la sauvegarde**, choisissez **Objets blob Azure (Stockage Azure)** comme type de source de données.

    ![Onglet Lancer : Configurer la sauvegarde](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Sous l’onglet **De base**, spécifiez **Objets blob Azure (Stockage Azure)** comme type de source de données, puis sélectionnez le coffre de sauvegarde auquel vous souhaitez associer vos comptes de stockage.<br></br>Vous pouvez afficher les détails du coffre sélectionné dans le même volet.

    ![Onglet Informations de base](./media/blob-backup-configure-manage/basics-tab.png)

    >[!NOTE]
    >Seules les sauvegardes opérationnelles sont activées pour les objets blob, qui stockent les sauvegardes dans le compte de stockage source (et non dans le coffre de sauvegarde). Par conséquent, le type de redondance du stockage de sauvegarde sélectionné pour le coffre ne s’applique pas à la sauvegarde des objets blob.

1. Sélectionnez la stratégie de sauvegarde à utiliser pour spécifier la rétention.<br></br>Vous pouvez afficher les détails de la stratégie sélectionnée dans la partie inférieure de l’écran. La colonne du magasin de données opérationnelles affiche la rétention définie dans la stratégie. **Opérationnelles** implique que les données sont conservées localement dans le compte de stockage source.
    
    ![Choisir une stratégie de sauvegarde](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Pour créer une stratégie de sauvegarde. Pour ce faire, sélectionnez **Créer** et suivez ces étapes :
    
    1. Spécifiez un nom pour la stratégie à créer.<br></br>Vérifiez que les autres zones affichent le type de source de données et le nom de coffre corrects.
    
    1. Sous l’onglet **Stratégie de sauvegarde**, sélectionnez l’icône **Modifier la règle de rétention** pour la règle de rétention afin de modifier la durée de la rétention des données.<br></br>Vous pouvez définir une durée de rétention jusqu’à **360** jours. 
    
        >[!NOTE]
        >Si les sauvegardes ne sont pas affectées par la période de rétention, l’opération de restauration pour la restauration de sauvegardes plus anciennes peut prendre plus de temps.

       ![Créer une stratégie de sauvegarde](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Sélectionnez **Vérifier + créer** pour créer la stratégie de sauvegarde.

1. Choisissez les comptes de stockage requis pour la configuration de la protection des objets blob. Vous pouvez sélectionner plusieurs comptes de stockage à la fois, puis choisir Sélectionner.<br></br>Toutefois, vérifiez que le coffre que vous avez choisi a le rôle RBAC requis affecté pour configurer la sauvegarde sur les comptes de stockage. Consultez [Accorder au coffre de sauvegarde des autorisations sur des comptes de stockage](#grant-permissions-to-the-backup-vault-on-storage-accounts) pour en savoir plus.<br></br>Si le rôle n’est pas attribué, vous pouvez toujours attribuer le rôle lors de la configuration de la sauvegarde. Consultez l’étape 7.

    ![Vérifier les autorisations du coffre](./media/blob-backup-configure-manage/verify-vault-permissions.png)

    Le service Sauvegarde valide que le coffre dispose d’autorisations suffisantes pour permettre la configuration d’une sauvegarde sur les comptes de stockage sélectionnés. Un certain temps est nécessaire pour terminer les validations.
    
    ![Autorisations pour autoriser la configuration de la sauvegarde](./media/blob-backup-configure-manage/permissions-for-configuring-backup.png)

1. Une fois les validations terminées, la colonne **Préparation de la sauvegarde** indique si le coffre de sauvegarde dispose des autorisations suffisantes pour configurer des sauvegardes pour chaque compte de stockage.

   ![Informations sur les autorisations de coffre de sauvegarde](./media/blob-backup-configure-manage/information-of-backup-vault-permissions.png)

    Si la validation affiche des erreurs (pour deux des comptes de stockage indiqués dans l’illustration ci-dessus), vous n’avez pas attribué le rôle **Collaborateur de sauvegarde de compte de stockage** pour ces [comptes de stockage](#grant-permissions-to-the-backup-vault-on-storage-accounts). En outre, vous pouvez attribuer le rôle requis ici, en fonction de vos autorisations actuelles. Le message d’erreur peut vous aider à comprendre si vous disposez des autorisations requises et à prendre les mesures appropriées :

    - **Attribution de rôle non effectuée :** Cette erreur (comme indiqué pour l’élément _blobbackupdemo3_ de la figure ci-dessus) indique que vous (l’utilisateur) avez l’autorisation d’attribuer le rôle **Contributeur de sauvegarde de compte de stockage** et les autres rôles requis pour le compte de stockage dans le coffre. Sélectionnez les rôles, puis cliquez sur **Attribuer les rôles manquants** dans la barre d’outils. Cela affectera automatiquement le rôle requis au coffre de sauvegarde et déclenchera une revalidation automatique.<br><br>Parfois, la propagation des rôles peut prendre un certain temps (jusqu’à 10 minutes), entraînant l’échec de la revalidation. Dans ce scénario, patientez quelques minutes, puis cliquez sur le bouton « Revalider » pour retenter la validation.
    
    - **Autorisations insuffisantes pour l’attribution de rôle :** Cette erreur (comme indiqué pour l’élément _blobbackupdemo4_ dans l’illustration ci-dessus) indique que le coffre n’a pas le rôle requis pour configurer la sauvegarde, et que vous (l’utilisateur) ne disposez pas des autorisations suffisantes pour attribuer le rôle requis. Pour faciliter l’attribution de rôle, l’utilitaire de sauvegarde vous permet de télécharger le modèle d’attribution de rôle, que vous pouvez partager avec des utilisateurs disposant d’autorisations pour attribuer des rôles pour les comptes de stockage. Pour ce faire, sélectionnez ce type de compte de stockage, puis cliquez sur **Télécharger le modèle d’attribution de rôle** pour télécharger le modèle.<br><br>Une fois les rôles attribués, vous pouvez les partager avec les utilisateurs appropriés. En cas de réussite de l’attribution du rôle, cliquez sur **Revalider** pour valider à nouveau les autorisations, puis configurez la sauvegarde.
        >[!NOTE]
        >Le modèle contient uniquement des détails sur les comptes de stockage sélectionnés. Par conséquent, si plusieurs utilisateurs doivent attribuer des rôles pour différents comptes de stockage, vous pouvez sélectionner et télécharger des modèles différents en conséquence.
1. Une fois la validation effectuée pour tous les comptes de stockage sélectionnés, allez à **Vérifier et configurer** pour configurer la sauvegarde.<br><br>Vous recevrez des notifications sur l’état de la configuration de la protection et de son achèvement.

### <a name="using-data-protection-settings-of-the-storage-account"></a>Utilisation des paramètres de protection des données du compte de stockage

Vous pouvez configurer la sauvegarde des objets blob dans un compte de stockage directement à partir des paramètres « Protection des données » du compte de stockage. 

1. Accédez au compte de stockage pour lequel vous souhaitez configurer la sauvegarde des objets blob, puis accédez à **Protection des données** dans le volet gauche (sous **Gestion des données**).

1. Dans les options de protection des données disponibles, la première vous permet d’activer la sauvegarde opérationnelle à l’aide de la Sauvegarde Azure.

    ![Sauvegarde opérationnelle à l’aide de la Sauvegarde Azure](./media/blob-backup-configure-manage/operational-backup-using-azure-backup.png)

1. Cochez la case correspondant à l’option **Activer la sauvegarde opérationnelle avec la Sauvegarde Azure**. Sélectionnez ensuite le coffre de sauvegarde et la stratégie de sauvegarde que vous souhaitez associer.<br><br>Vous pouvez sélectionner le coffre et la stratégie existants, ou en créer de nouveaux, selon les besoins.

    >[!IMPORTANT]
    >Vous devez avoir affecté le rôle **Contributeur de sauvegarde du compte de stockage** au coffre sélectionné. Consultez [Accorder au coffre de sauvegarde des autorisations sur des comptes de stockage](#grant-permissions-to-the-backup-vault-on-storage-accounts) pour en savoir plus.
    
    - Si vous avez déjà affecté le rôle requis, cliquez sur **Enregistrer** pour terminer la configuration de la sauvegarde. Suivez les notifications du portail pour garder la trace de la progression de la configuration de la sauvegarde.
    - Si vous ne l’avez pas encore fait, cliquez sur **Gérer l’identité** et suivez les étapes ci-dessous pour attribuer les rôles. 

        ![Activer la sauvegarde opérationnelle avec la Sauvegarde Azure](./media/blob-backup-configure-manage/enable-operational-backup-with-azure-backup.png)


        1. Cliquer sur **Gérer l’identité** vous amène au panneau Identité du compte de stockage. 
        
        1. Cliquez sur **Ajouter une attribution de rôle** pour initier l’attribution de rôle.

            ![Ajouter une attribution de rôle pour initier l’attribution de rôle](./media/blob-backup-configure-manage/add-role-assignment-to-initiate-role-assignment.png)


        1. Choisissez l’étendue, l’abonnement, le groupe de ressources ou le compte de stockage que vous souhaitez affecter au rôle.<br><br>Nous vous recommandons d’attribuer le rôle au niveau du groupe de ressources si vous souhaitez configurer la sauvegarde opérationnelle pour les objets blob de plusieurs comptes de stockage.

        1. Dans la liste déroulante **Rôle**, sélectionnez le rôle **Contributeur de sauvegarde de compte de stockage**.

            ![Sélectionner le rôle contributeur de sauvegarde du compte de stockage](./media/blob-backup-configure-manage/select-storage-account-backup-contributor-role.png)


        1. Cliquez sur **Enregistrer** pour terminer l’attribution de rôle.<br><br>Une fois cette opération terminée, vous recevrez une notification par le biais du portail. Vous pouvez également voir le nouveau rôle ajouté à la liste des éléments existants pour le coffre sélectionné.

            ![Terminer l’attribution de rôle](./media/blob-backup-configure-manage/finish-role-assignment.png)

        1. Cliquez sur l’icône d’annulation (**x**) dans le coin supérieur droit pour revenir au panneau **Protection des données** du compte de stockage.<br><br>Une fois de retour, poursuivez la configuration de la sauvegarde.

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

Pour plus d’informations, consultez [Vue d’ensemble du centre de sauvegarde](backup-center-overview.md).

## <a name="stopping-protection"></a>Arrêt de la protection

Vous pouvez arrêter la sauvegarde opérationnelle pour votre compte de stockage selon vos besoins.

>[!NOTE]
>L’arrêt de la protection dissocie uniquement le compte de stockage du coffre de sauvegarde (et les outils de sauvegarde, tels que le Centre de sauvegarde), et ne désactive pas la restauration à un instant dans le passé des objets blob, le contrôle de version et le flux de modification qui ont été configurés.

Pour arrêter la sauvegarde d’un compte de stockage, procédez comme suit :

1. Accédez à l’instance de sauvegarde pour le compte de stockage en cours de sauvegarde.<br><br>Vous pouvez y accéder à partir du compte de stockage via **Compte de stockage** -> **Protection des données** -> **Gérer les paramètres de sauvegarde**, ou directement à partir du Centre de sauvegarde via le **Centre de sauvegarde** -> **Instances de sauvegarde** -> recherchez le nom du compte de stockage.

    ![Emplacement du compte de stockage](./media/blob-backup-configure-manage/storage-account-location.png)

    ![Emplacement du compte de stockage via le Centre de sauvegarde](./media/blob-backup-configure-manage/storage-account-location-through-backup-center.png)


1. Dans l’instance de sauvegarde, cliquez sur **Supprimer** pour arrêter la sauvegarde opérationnelle pour le compte de stockage en question. 
 
    ![Arrêter une sauvegarde opérationnelle](./media/blob-backup-configure-manage/stop-operational-backup.png)

Après l’arrêt de la sauvegarde, vous pouvez désactiver d’autres fonctionnalités de protection des données de stockage (activées pour la configuration de la sauvegarde) à partir du panneau Protection des données du compte de stockage.


## <a name="next-steps"></a>Étapes suivantes

[Restaurer des objets blob Azure](blob-restore.md)

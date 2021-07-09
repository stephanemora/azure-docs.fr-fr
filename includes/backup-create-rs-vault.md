---
title: Fichier include
description: Fichier include
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/01/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 7d354ce3e69f075d2f523a75ba96f9a6efb7d797
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783574"
---
## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

Un coffre Recovery Services est une entité de gestion qui stocke les points de récupération créés au fil du temps et fournit une interface permettant d’effectuer des opérations liées à la sauvegarde. Cela inclut l’exécution de sauvegardes à la demande, l’exécution de restaurations et la création de stratégies de sauvegarde.

Pour créer un coffre Recovery Services, effectuez les étapes suivantes.

1. Connectez-vous à votre abonnement sur le [portail Azure](https://portal.azure.com/).

1. Recherchez **Centre de sauvegarde** dans le portail Azure, puis accédez au tableau de bord du **Centre de sauvegarde**.

    ![Sélectionner Centre de sauvegarde](../includes/media/backup-create-rs-vault/backup-center-search-backup-center.png)

1. Sélectionnez **+Coffre** sous l’onglet **Vue d’ensemble**.

    ![Création d'un coffre](./media/backup-create-rs-vault/backup-center-create-vault.png) 

1. Sélectionnez **Coffre Recovery Services**, puis cliquez sur **Continuer**.

    ![Sélectionner le coffre Recovery Services](./media/backup-create-rs-vault/backup-center-select-recovery-services-vault.png) 

1. La boîte de dialogue **Coffre Recovery Services** s’ouvre. Attribuez des valeurs aux champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Configurer le coffre Recovery Services](./media/backup-create-rs-vault/backup-center-add-vault-details.png)

   - **Name** : entrez un nom convivial pour identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union.
   - **Abonnement**: choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
   - **Groupe de ressources** : Utilisez un groupe de ressources existant ou créez-en un. Pour voir la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Emplacement** : sélectionnez la région géographique du coffre. Pour créer un coffre en vue de protéger une source de données, le coffre *doit* se trouver dans la même région que la source de données.

      > [!IMPORTANT]
      > Si vous ne connaissez pas avec certitude l’emplacement de la source de données, fermez la boîte de dialogue. Accédez à la liste de vos ressources dans le portail. Si vous possédez des sources de données dans plusieurs régions, créez un coffre Recovery Services pour chaque région. Créez le coffre du premier emplacement avant de créer celui d’un autre emplacement. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et Sauvegarde Azure gèrent cela automatiquement.
      >
      >

1. Après avoir défini les valeurs, sélectionnez **Vérifier + créer**.

    ![Capture d’écran montrant le bouton Vérifier + créer dans le processus de création d’un coffre Recovery Services.](./media/backup-create-rs-vault/review-and-create.png)

1. Quand vous êtes prêt à créer le coffre Recovery Services, sélectionnez **Créer**.

    ![Créer le coffre Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    La création du coffre Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans la zone **Notifications** dans l’angle supérieur droit du portail. Une fois que le coffre est créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

     ![Actualiser la liste des coffres de sauvegarde](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Nous vous recommandons vivement de consulter les paramètres par défaut pour **type de réplication de stockage** et **Paramètres de sécurité** avant de configurer les sauvegardes dans le coffre. Pour plus d’informations, consultez la section [Définir la redondance du stockage](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy).
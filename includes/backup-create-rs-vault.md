---
title: Fichier Include
description: Fichier Include
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 74b5ebc89777f8043b44be4569a71fc8b33768d8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979887"
---
## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

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

   - **Name** : entrez un nom convivial pour identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Spécifiez un nom composé d’au moins deux caractères, mais sans dépasser 50 caractères. Il doit commencer par une lettre et ne peut être constitué que de lettres, chiffres et traits d’union.
   - **Abonnement**: choisissez l’abonnement à utiliser. Si vous êtes membre d’un seul abonnement, son nom s’affiche. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
   - **Groupe de ressources** : Utilisez un groupe de ressources existant ou créez-en un. Pour afficher la liste des groupes de ressources disponibles dans votre abonnement, sélectionnez **Utiliser existant**, puis sélectionnez une ressource dans la zone de liste déroulante. Pour créer un groupe de ressources, sélectionnez **Créer** et entrez le nom. Pour obtenir des informations complètes sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Emplacement** : sélectionnez la région géographique du coffre. Pour créer un coffre en vue de protéger des machines virtuelles, le coffre **doit** se trouver dans la même région que les machines virtuelles.

      > [!IMPORTANT]
      > Si vous ne connaissez pas avec certitude l’emplacement de vos machines virtuelles, fermez la boîte de dialogue. Accédez à la liste des machines virtuelles dans le portail. Si vous possédez des machines virtuelles dans plusieurs régions, créez un coffre Recovery Services dans chacune d’elles. Créez le coffre du premier emplacement avant de créer celui d’un autre emplacement. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et le service Azure Backup gèrent cela automatiquement.
      >
      >

5. Quand vous êtes prêt à créer le coffre Recovery Services, sélectionnez **Créer**.

    ![Créer le coffre Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    La création du coffre Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans la zone **Notifications** dans l’angle supérieur droit du portail. Une fois que le coffre est créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

     ![Actualiser la liste des coffres de sauvegarde](./media/backup-create-rs-vault/refresh-button.png)

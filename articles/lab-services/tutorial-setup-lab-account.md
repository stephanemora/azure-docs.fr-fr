---
title: Configurer un compte de laboratoire avec Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un compte de laboratoire avec Azure Lab Services, comment ajouter un créateur de laboratoire et comment spécifier les images de la Place de marché utilisées par les laboratoires dans le compte de laboratoire.
ms.topic: tutorial
ms.date: 07/26/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: d6107c1a70b22682636b63c0fb0f7374a0d96873
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634012"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutoriel : Configurer un compte lab avec Azure Lab Services
Dans Azure Lab Services, un compte de laboratoire sert de compte central, dans lequel sont gérés tous les laboratoires de votre entreprise. Dans votre compte lab, accordez l’autorisation à d’autres personnes pour créer des laboratoires et définissez des stratégies qui s’appliquent à tous les laboratoires du compte de laboratoire. Dans ce tutoriel, découvrez comment créer un compte de laboratoire. 

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte de laboratoire
> * Ajouter un utilisateur au rôle Créateur de laboratoire

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Sélectionnez **DevOps** dans **Catégories**. Ensuite, sélectionnez **Lab Services**. Si vous sélectionnez l’étoile (`*`) en regard de **Lab Services**, celui-ci est ajouté à la section **FAVORIS** dans le menu de gauche. Les fois suivantes, sélectionnez **Lab Services** sous **FAVORIS**.

    ![Tous les services -> Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Dans la page **Lab Services**, sélectionnez **Ajouter** dans la barre d’outils ou sélectionnez le bouton **Créer un compte de laboratoire** dans la page. 

    ![Sélectionnez Ajouter dans la page Comptes Lab.](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Sous l’onglet **De base** de la page **Créer un compte de laboratoire**, effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant ou choisissez **Créer**, puis entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 

        ![Compte Lab - Page De base](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Sélectionnez **Revoir + créer**.
    6. Passez en revue le récapitulatif, puis sélectionnez **Créer**. 

        ![Vérifier + créer -> Créer](./media/tutorial-setup-lab-account/create-button.png)    
5. Une fois le déploiement terminé, développez **Étapes suivantes**, puis sélectionnez **Accéder à la ressource**. 

    ![Accéder à la page du compte de laboratoire](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Vérifiez que vous voyez la page **Compte de laboratoire**. 

    ![Page Lab account (Compte de laboratoire)](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, attribuez-leur le rôle **Créateur de laboratoire**. Pour en savoir plus sur cette opération, consultez la page [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).

> [!NOTE]
> Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe dans ce tutoriel, ignorez cette étape. 


1. Sur la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)**

1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle (préversion)** .

    ![Page Contrôle d’accès (IAM) avec le menu Ajouter une attribution de rôle ouvert.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. Dans l’onglet **Rôle**, sélectionnez le rôle **Créateur de laboratoire**.

    ![Page Ajouter une attribution de rôle avec l’onglet Rôle sélectionné.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. Sous l’onglet **Membres**, sélectionnez l’utilisateur auquel vous souhaitez ajouter le rôle Créateur de laboratoire

1. Dans l’onglet **Passer en revue + affecter**, sélectionnez **Passer en revue + affecter** pour affecter le rôle.


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un compte de laboratoire. Pour en savoir plus sur la création d’un laboratoire pour une classe en tant que formateur, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md)


---
title: Gérer des comptes de laboratoire dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire dans un abonnement Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 09303a4d4fc730aae6fc7c04148d64b0f57e69ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>Gérer des comptes de laboratoire dans Azure Lab Services (anciennement Azure DevTest Labs)
Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="prerequisites"></a>Prérequis

Azure Lab Services est actuellement disponible en préversion contrôlée. Pour créer un compte de laboratoire, [inscrivez-vous pour bénéficier de la préversion](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu principal sur le côté gauche, sélectionnez **Créer une ressource** (en haut de la liste), pointez sur **Outils de développement**, puis cliquez sur **Lab Services (preview)**.
1. Dans la fenêtre **Create a lab account** (Créer un compte de laboratoire), sélectionnez **Create** (Créer).
2. Dans la fenêtre **Lab account** (Compte de laboratoire), effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 
    5. Sélectionnez **Créer**. 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Si vous ne voyez pas la page associée au compte de laboratoire, sélectionnez le bouton **Notifications**, puis cliquez sur le bouton **Go to resource** (Accéder à la ressource) dans les notifications. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. La page de **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle Créateur de laboratoire :

1. Dans la page **Lab account** (Compte de laboratoire), sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+Ajouter** dans la barre d’outils. 

    ![Page Lab account (Compte de laboratoire)](./media/tutorial-setup-lab-account/access-control.png)
2. Dans la page **Ajouter des autorisations**, sélectionnez **Créateur de laboratoire** pour **Rôle**, sélectionnez l’utilisateur que vous souhaitez ajouter au rôle Créateur de laboratoire, puis sélectionnez **Enregistrer**. 

    ![Ajouter un utilisateur au rôle Créateur de laboratoire](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Afficher les comptes de laboratoire
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu, sélectionnez **Toutes les ressources**. 
3. Sélectionnez **Lab Services** (Services Lab) pour le **type**. 
    Vous pouvez également filtrer par abonnement, par groupe de ressources, par emplacement et par balise. 

## <a name="delete-a-lab-account"></a>Supprimer un compte de laboratoire
Suivez les instructions de la section précédente, qui permettent d’afficher la liste de comptes de laboratoire. Utilisez les instructions suivantes pour supprimer un compte de laboratoire : 

1. Sélectionnez le **compte de laboratoire** que vous voulez supprimer. 
2. Sélectionnez **Supprimer** dans la barre d’outils. 
3. Tapez **Oui** pour confirmer.
4. Sélectionnez **Supprimer**. 

## <a name="next-steps"></a>Étapes suivantes
Commencez à configurer un laboratoire avec Azure Lab Services :

- [Configurer un laboratoire de classe](tutorial-setup-classroom-lab.md)
- [Configurer un laboratoire personnalisé](tutorial-create-custom-lab.md)

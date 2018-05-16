---
title: Configurer un compte de laboratoire avec Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous configurez un compte de laboratoire avec Azure Lab Services (anciennement DevTest Labs).
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 5af348bfdccb9392948af962cf582e33ebd40872
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>Didacticiel : Configurer un compte de laboratoire avec Azure Lab Services (anciennement Azure DevTest Labs)
Dans ce didacticiel, vous jouez le rôle d’un administrateur de laboratoire pour créer un compte de laboratoire avec Azure Lab Services. Ensuite, vous donnez aux formateurs l’autorisation de créer des laboratoires pour leurs classes dans ce compte de laboratoire. Un formateur peut configurer un laboratoire pour une classe à l’aide du [site web Azure Lab Services](https://labs.azure.com).   

Dans ce didacticiel, vous effectuez les actions suivantes :

> [!div class="checklist"]
> * Créer un compte de laboratoire
> * Ajouter un utilisateur au rôle Créateur de laboratoire

## <a name="prerequisites"></a>Prérequis


- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Azure Lab Services est actuellement disponible en préversion contrôlée. Pour créer un compte de laboratoire, [inscrivez-vous pour bénéficier de la préversion](https://aka.ms/azlabspreviewsignup).

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu principal sur le côté gauche, sélectionnez **Créer une ressource** (en haut de la liste), pointez sur **Outils de développement**, puis cliquez sur **Lab Services (preview)** (Lab Services (préversion)).
1. Dans la fenêtre **Create a lab account** (Créer un compte de laboratoire), sélectionnez **Créer**.
2. Dans la fenêtre **Lab account** (Compte de laboratoire), effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez une région/un emplacement dans lequel vous souhaitez créer le compte de laboratoire. 
    5. Sélectionnez **Créer**. 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. Si vous ne voyez pas la page associée au compte de laboratoire, sélectionnez le bouton **Notifications**, puis cliquez sur le bouton **Go to resource** (Accéder à la ressource) dans les notifications. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle Créateur de laboratoire :

1. Sur la page **Lab account** (Compte de laboratoire), sélectionnez **Contrôle d’accès (IAM)** et cliquez sur **+Ajouter** dans la barre d’outils. 

    ![Page Lab account (Compte de laboratoire)](./media/tutorial-setup-lab-account/access-control.png)
2. Sur la page **Ajouter des autorisations**, sélectionnez **Créateur de laboratoire** pour **Rôle**, sélectionnez l’utilisateur que vous souhaitez ajouter au rôle Créateur de laboratoire et sélectionnez **Enregistrer**. 

    ![Ajouter un utilisateur au rôle Créateur de laboratoire](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un compte de laboratoire. Pour en savoir plus sur la création d’un laboratoire pour une classe en tant que profession, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Set up a classroom lab](tutorial-setup-classroom-lab.md) (Configurer un laboratoire de classe)


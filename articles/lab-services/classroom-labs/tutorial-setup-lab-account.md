---
title: Configurer un compte de laboratoire avec Azure Lab Services | Microsoft Docs
description: Dans ce tutoriel, vous configurez un compte de laboratoire avec Azure Lab Services.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 600be7518bc526d3f147bb16377677854b676f63
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823127"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutoriel : Configurer un compte de laboratoire avec Azure Lab Services
Dans Azure Lab Services, un compte de laboratoire sert de compte central, dans lequel sont gérés tous les laboratoires de votre entreprise. Dans votre compte lab, accordez l’autorisation à d’autres personnes pour créer des laboratoires et définissez des stratégies qui s’appliquent à tous les laboratoires du compte de laboratoire. Dans ce tutoriel, découvrez comment créer un compte de laboratoire en tant qu’administrateur de laboratoire. 

Dans ce didacticiel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte de laboratoire
> * Ajouter un utilisateur au rôle Créateur de laboratoire

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-lab-account"></a>Créer un compte de laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un compte de laboratoire avec Azure Lab Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Créer une ressource** dans le menu principal de gauche.
3. Recherchez **Services Lab** dans la place de marché Azure, puis sélectionnez **Services Lab** dans la liste déroulante. 
4. Sélectionnez **Services Lap (version préliminaire)** dans la liste des services filtrés. 
1. Dans la fenêtre **Create a lab account** (Créer un compte de laboratoire), sélectionnez **Créer**.
2. Dans la fenêtre **Lab account** (Compte de laboratoire), effectuez les actions suivantes : 
    1. Pour **Lab account name** (Nom du compte de laboratoire), entrez un nom. 
    2. Sélectionnez **l’abonnement Azure** dans lequel vous souhaitez créer le compte de laboratoire.
    3. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez un nom pour le groupe de ressources.
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le compte de laboratoire. 
    5. Sélectionnez **Créer**. 

        ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Si vous ne voyez pas la page associée au compte de laboratoire, sélectionnez le bouton **Notifications**, puis cliquez sur le bouton **Go to resource** (Accéder à la ressource) dans les notifications. 

    ![Fenêtre Create a lab account (Créer un compte de laboratoire)](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. La page **Lab account** (Compte de laboratoire) suivante s’affiche :

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle Créateur de laboratoire :

1. Dans la page **Lab account** (Compte de laboratoire), sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+Ajouter** dans la barre d’outils. 

    ![Page Lab account (Compte de laboratoire)](../media/tutorial-setup-lab-account/access-control.png)
2. Dans la page **Ajouter des autorisations**, sélectionnez **Créateur de laboratoire** pour **Rôle**, sélectionnez l’utilisateur que vous souhaitez ajouter au rôle Créateur de laboratoire, puis sélectionnez **Enregistrer**. 

    ![Ajouter un utilisateur au rôle Créateur de laboratoire](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un compte de laboratoire. Pour en savoir plus sur la création d’un laboratoire pour une classe en tant que profession, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Set up a classroom lab](tutorial-setup-classroom-lab.md) (Configurer un laboratoire de classe)


---
title: Configurer un laboratoire de classe à l’aide d’Azure Lab Services | Microsoft Docs
description: Dans ce didacticiel, vous allez configurer un laboratoire à utiliser dans une classe.
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
ms.openlocfilehash: 39683c89db57dbeefd190a51415c783d012785e0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303773"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Didacticiel : Configurer un laboratoire de classe 
Dans ce tutoriel, vous allez configurer un laboratoire de classe avec des machines virtuelles utilisées par les étudiants dans la classe.  

Dans ce didacticiel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un laboratoire de classe
> * Configurer le laboratoire de classe
> * Envoyer un lien d’inscription aux étudiants

## <a name="prerequisites"></a>Prérequis
Pour configurer un laboratoire de classe dans un compte de laboratoire, vous devez être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Un propriétaire de laboratoire peut ajouter un utilisateur au rôle Créateur de laboratoire à l’aide des étapes fournies dans l’article suivant : [Ajouter un utilisateur au rôle Créateur de laboratoire](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Créer un laboratoire de classe

1. Accédez au [site web Azure Lab Services](https://labs.azure.com).
2. Sélectionnez **Se connecter** et entrez vos informations d’identification. 
3. Dans la fenêtre **New Lab** (Nouveau laboratoire), effectuez les actions suivantes : 
    1. Spécifiez le **nom** du laboratoire de classe. 
    2. Sélectionnez la **taille** de la machine virtuelle que vous prévoyez d’utiliser dans la classe.
    3. Sélectionnez **l’image** à utiliser pour créer la machine virtuelle.
    4. Spécifiez les **informations d’identification par défaut** pour vous connecter à des machines virtuelles dans le laboratoire. 
    7. Sélectionnez **Enregistrer**.

        ![Créer un laboratoire de classe](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Le **tableau de bord** du laboratoire s’affiche. 
    
    ![Tableau de bord de laboratoire de classe](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurer la politique d’utilisation

1. Sélectionnez **Politique d’utilisation**. 
2. Dans **Politique d’utilisation**, puis dans les paramètres, entrez le **nombre d’utilisateurs** autorisés à utiliser le laboratoire.
3. Sélectionnez **Enregistrer**. 

    ![Politique d’utilisation](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Configurer le modèle 
Le modèle de laboratoire est une image de machine virtuelle de base, à partir de laquelle toutes les machines virtuelles des utilisateurs sont créées. Configurez la machine virtuelle du modèle de façon qu’elle propose exactement ce que vous souhaitez fournir aux utilisateurs du laboratoire. Vous pouvez indiquer le nom et la description du modèle que voient les utilisateurs du laboratoire. Publiez le modèle au public pour rendre les instances du modèle de machine virtuelle accessible aux utilisateurs de votre laboratoire. 

### <a name="set-title-and-description"></a>Définir un titre et une description
1. Dans la section **Modèle**, sélectionnez **Modifier** (icône de crayon) pour le modèle. 
2. Dans la fenêtre **Vue Utilisateur**, entrez un **titre** pour le modèle.
3. Entrez une **description** pour le modèle.
4. Sélectionnez **Enregistrer**.

    ![Description du laboratoire de classe](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Configurer le modèle de machine virtuelle
 Vous vous connectez au modèle de machine virtuelle et vous installez les logiciels nécessaires avant de les rendre accessibles à vos étudiants. 

1. Attendez que la machine virtuelle soit prête. Une fois qu’elle est prête, le bouton **Démarrer** doit être activé. Pour démarrer la machine virtuelle, sélectionnez **Démarrer**.

    ![Démarrer le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Pour vous connecter à la machine virtuelle, sélectionnez **Se connecter**, puis suivez les instructions. 

    ![Se connecter au modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Installez les logiciels nécessaires pour que les étudiants puissent effectuer le laboratoire (par exemple Visual Studio, l’Explorateur de stockage Azure, et ainsi de suite). 
2. Déconnectez-vous (fermez votre session Bureau à distance) du modèle de machine virtuelle. 
3. **Arrêtez** le modèle de machine virtuelle en sélectionnant **Arrêter**. 

    ![Arrêter le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>Publier le modèle 
Quand vous publiez un modèle, Azure Lab Services crée les machines virtuelles dans le laboratoire à l’aide du modèle. Le nombre de machines virtuelles créées dans ce processus est identique au nombre maximal d’utilisateurs autorisés dans le laboratoire, que vous pouvez définir dans la politique d’utilisation du laboratoire. Toutes les machines virtuelles ont la même configuration que le modèle. 

1. Sélectionnez **Publier** dans la section **Modèle**. 

    ![Publier le modèle de machine virtuelle](../media/tutorial-setup-classroom-lab/public-access.png)
1. Dans la fenêtre **Publier**, sélectionnez l’option **Publié**. 
2. Cliquez maintenant sur le bouton **Publier**. Ce processus peut prendre un certain temps en fonction du nombre de machines virtuelles en cours de création, qui est identique au nombre d’utilisateurs autorisés dans le laboratoire.
    
    > [!IMPORTANT]
    > Une fois que vous avez défini un modèle comme étant public, vous ne pouvez plus rendre son accès privé. 
4. Basculez vers la page **Machines virtuelles** et vérifiez que cinq machines virtuelles se trouvent à l’état **Non affectée**. Ces machines virtuelles ne sont pas encore affectées aux étudiants. 

    ![Machines virtuelles](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Attendez que les machines virtuelles soient créées. Elles doivent être à l’état **Arrêtée**. Vous pouvez démarrer la machine virtuelle d’un étudiant, vous y connecter, l’arrêter et la supprimer dans cette page. Vous pouvez démarrer les machines virtuelles dans cette page ou laisser les étudiants le faire. 

    ![Machines virtuelles à l’état Arrêtée](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Envoyer un lien d’inscription aux étudiants

1. Basculez vers la vue **Tableau de bord**. 
2. Sélectionnez la vignette **Enregistrement de l’utilisateur**.

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez le bouton **Copier**. Le lien est copié dans le Presse-papiers. Collez-le dans un éditeur d’e-mail et envoyez un e-mail à l’étudiant. 

    ![Lien d’inscription de l’étudiant](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Dans la boîte de dialogue **Enregistrement de l’utilisateur**, sélectionnez **Fermer**. 


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un laboratoire de classe et configuré le laboratoire. Pour savoir comment un étudiant peut accéder à une machine virtuelle dans le laboratoire à l’aide du lien d’inscription, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Se connecter à une machine virtuelle dans le laboratoire de classe](tutorial-connect-virtual-machine-classroom-lab.md)

